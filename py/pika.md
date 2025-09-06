
### python

##### 1. connection and channel

```py
import pika

conn_param = pika.ConnectionParameters('localhost')
connection = pika.BlockingConnection(conn_param)                  # Connect to RabbitMQ (default localhost:5672, vhost "/")
channel = connection.channel()                                                # channel has access to all methods of lib
```

##### 2. connection parameters

```py
import pika                                                                # its good idea to write a function for connecting

credentials = pika.PlainCredentials("guest", "guest")                           # username and password for localhost
conn_param = pika.connection.ConnectionParameters(credentials=credentials)          # keyword calling
conn_param = pika.connection.ConnectionParameters('localhost',5672, / ,credentials)   # positional calling
connection = pika.BlockingConnection(conn_param)
```

##### 2. declare queue

+ we can declare queue in dashboard also and just connect to that in code
+ we have 2 type of argumets
    1. queue declare arguments:
        a. queue
        b. durable
        c. exclusive
        d. autoDelete
        e. passive
    2. x-arguments:
        a. x-message-ttl
        b. x-expires
        c. x-dead-letter-exchange

+ we can see non-durable queues in dashboard with no data, but we cannot see auto_delete queues after deleting

```py
channel.queue_declare(queue='task_queue', passive=True)                # does not create a new one and just check if it exists already
channel.queue_declare(queue='task_queue', durable=True)                # Durable queue (survives broker restart)
channel.queue_declare(queue='temp', auto_delete=True)               # Auto-delete queue (deleted when last consumer disconnects)
channel.queue_declare(       # multiple arguments
    queue='queue_name',      # Queue name (empty for server-generated)
    durable=False,           # Survive broker restart?
    exclusive=False,         # Private to this connection?
    auto_delete=False,       # Delete when no consumers?
    arguments={}             # Optional advanced x-arguments
)
channel.queue_declare(
    queue='my_important_queue',
    arguments={                           # times in milliseconds 
        'x-expires': 1800000,              # queue is deleted after 30 minutes of being unused (Queue expiration)
        'x-message-ttl': 300000,           # Messages expire after 5 min   (Message expiration)

        #  Dead letter handling
        'x-dead-letter-exchange': 'my-dlx',   # Send expired/rejected messages to 'my-dlx' (another exchange)
        'x-dead-letter-routing-key': "error.message",   #  re-routes to a new routing key

        # Length limits
        'x-max-length': 10000,             # Hold max 10,000 messages in queue (older messages of the queue are nacked or dead-lettered to make room)
        'x-max-length-bytes': 10485760,          # Max total size (10MB)

        # type and mode
        'x-queue-mode': 'lazy',             # Be kind to RAM, use disk  # values: 1. default 2. lazy
        'x-queue-type': 'quorum',           # 	Underlying queue implementation  # values: 1. classic (default) 2. quorum 3. stream
        'x-single-active-consumer': True,        # Only one consumer at a time        
    }
)

result = channel.queue_declare(queue='', exclusive=True)               # Temporary (exclusive, auto-delete) queue
queue_name = result.method.queue                                # queue = '' means Let server generate a name which is exclusive and unique
```


##### 3. declare exchange
```py
channel.exchange_declare(exchange='direct_logs', exchange_type='direct')          # Direct exchange   // 1. direct, 2. fanout, 3. topic 4. headers
channel.exchange_declare(exchange='logs', exchange_type='fanout')           # Fanout exchange
```


##### 4. declare binding

+ binding: Bind Queue to Exchange
+ we can see list of routing_key in dashboard
+ `rabbitmqctl list_bindings`

```py
channel.queue_bind(exchange='direct_logs', queue='Q1', routing_key='error')         # 1. Direct
channel.queue_bind(exchange='logs', queue='Q1')                                      # 2. Fanout (no routing key needed)
channel.queue_bind(exchange='topic_logs', queue='Q1', routing_key='user.*')                # 3. Topic

# 4. Headers (not recommend)
args = {"x-match": "all", "format": "json", "type": "report"}
channel.queue_bind(exchange='headers_logs', queue='Q2', arguments=args)
```

###### 7. Acknowledgments

+ `acknowledgement` is a signal sent from a consumer back to the RabbitMQ broker to confirm receipt of a message (Guaranteed Delivery)
+ Acknowledgments tell the broker what to do with a message:
    a. delete message immediately  `auto_ack = True`  (broker consider messages is sent to the consumer)
    b. delete message after receiving ack `auto_ack = False` (after setting this, wrtie `basic_ack`)

    + positive acknowledgement:
        a. basic_ack: delete single message
    + negative acknowledgement -----> use this in `try-execpt`
        a. basic_nack: reject one or multiple messages    
        b. basic_reject: reject one message


```py
def callback(ch, method, properties, body):
    try:                                                               # delivery_tag is a unique receipt number for a specific message
        ch.basic_ack(delivery_tag=method.delivery_tag)                    # <- This tells RabbitMQ to delete it (auto_ack=False)
    except:                                                                              # NEGATIVE ACKNOWLEDGE (NACK)
        ch.basic_nack(delivery_tag=method.delivery_tag, requeue=True)                     # Reject and requeue one msg if failed
        ch.basic_nack(delivery_tag=method.delivery_tag, requeue=False, multiple=True)     # Reject all msg if failed and not requeue
        ch.basic_reject(delivery_tag=method.delivery_tag, requeue=True)                 # Reject and requeue one msg if failed  # same

channel.basic_consume(queue='q1', on_message_callback=callback, auto_ack=True)     # UNSAFE: delete message immediately without considering
channel.basic_consume(queue='q1', on_message_callback=callback, auto_ack=False)    # SAFE: Manual Ack (you control when it's deleted)
```

##### 8. Qos

+ ack(Acknowledgment) and qos(Quality of Service) are features ensure messages are not lost
+ `QoS` is a mechanism that allows you to control how many messages can be sent to a consumer before the consumer acknowledges them.
+ The key setting is `prefetch_count`, which means how many unacknowledged messages a consumer can hold at a time.
+ If a consumer is slow, it may get overloaded. QoS prevents this by limiting "in-flight" messages.

```py
# Set QoS *before* starting to consume                 # Most common setting for fair round-robin
channel.basic_qos(prefetch_count=1)                    # Fair dispatch (don’t send new msg to consumer until it’s done with one)

# Now start consuming                                 # RabbitMQ will send only one unacknowledged message at a time to that consumer
channel.basic_consume(queue='hello', on_message_callback=callback, auto_ack=False)   
```

##### 5. Publish Message

+ basic_publish() ----> producer   `basic_publish(exchange, routing_key, body, properties)`
+ basic_consume() ----> consumer   `basic_consume(queue, on_message_callback, auto_ack)`

```py
channel.basic_publish(
    exchange='direct_logs',   # specific exchange
    exchange='',           # Use default direct exchange
    routing_key='error',   # routing key for direct/topic  (need if using default exchange and not used for fanout)
    body='Hello RabbitMQ!',   # data
    properties=pika.BasicProperties(   # optioal
        delivery_mode = 2,              # 1=non-persistent, 2=persistent (survives broker restart)
        delivery_mode=pika.spec.PERSISTENT_DELIVERY_MODE,    # same 2  # Make message persistent
        content_type = 'text/plain',    # e.g., 'application/json'
        content_encoding = 'utf-8',     # e.g., 'gzip'
        headers = {'key': 'value'},     # Custom key-value metadata
        priority = 0,                   # Message priority (0-9)
        correlation_id = '12345',       # For RPC responses    # correlation_id is the glue that binds a request to its response
        reply_to = 'reply_queue_name',  # For RPC responses
        expiration = '60000',           # TTL for message in ms (e.g., '60000' = 1 minute)
        message_id = 'msg_001',         # Application-specific message ID
        user_id = 'guest',              # MUST match broker user for validation
        app_id = 'my_app',              # Application identifier
        type = 'order.created'          # Application-specific message type
    )
)
```

##### 6. Consume Message

+ start a continuous, asynchronous process where a consumer application receives messages from a queue
+ callback function will be triggered whenever a message is available
+ check consume messages in dashboard
+
+ callback args:
    1. channel:            Channel object on which the message was received
    2. method:             object contains metadata and information about how the message was delivered
        + method.delivery_tag:   unique identifier for the message
        + method.routing_key:   routing key for the message
    3. properties:         object contains headers
        + properties.content_type:   e.g., 'text/plain'
        + properties.content_encoding:   e.g., 'utf-8'
    4. body:               message body

```py
def callback(ch, method, properties, body):        #  callback function invoked when a message is received
    print(f" [x] Received {body.decode('utf-8')}")                  # body is the message payload (decode if it is bytes)
    print(f" [x] Received {body}")                                   # if body is string use it directly
    ch.basic_ack(delivery_tag=method.delivery_tag)                    # Acknowledge the message manually (auto_ack=False)
# Start consuming
consumer_tag = channel.basic_consume(                      # basic_consume(q, callback)
    queue='task_queue',                   # consume from this queue
    on_message_callback=callback,          # callback
    auto_ack=False                        # True = auto-ack, False = manual ack (auto_ack=False recommended for reliability)
)                                         # auto_ack=True means message will be deleted immediately without checking any condition

print(' [*] Waiting for messages. To exit press CTRL+C')
channel.start_consuming()
channel.basic_cancel(consumer_tag)          # cancel consumer # Uses the tag returned by basic_consume
```


##### 1. Producer

```py
import pika

# Connect
connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()

# Declare queue
channel.queue_declare(queue='hello')

# Send message
channel.basic_publish(exchange='', routing_key='hello', body='Hello World!')

print(" [x] Sent 'Hello World!'")
connection.close()
```


##### 2. Consumer

```py
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()

channel.queue_declare(queue='hello')

def callback(ch, method, properties, body):          # ch for channel
    print(f" [x] Received {body}")
    ch.basic_ack(method.delivery_tag)

channel.basic_consume(queue='hello', on_message_callback=callback, auto_ack=True)

print(' [*] Waiting for messages. To exit press CTRL+C')
channel.start_consuming()
```


##### Publisher Confirms

+ When a message is published rabbitmq sends back an ack/nack to the publisher
    + Ack scenarios:                           when we get acknowledgement ?
        a. non routable transient message:     when a message has a key that is not matching to any key-bounding to any queue
        b. routable transient message:         acknowledged when message is reached the queue
        c. persistent messages:                acknowledged when the message is written to disk (durable exchange and queue are required)
    + Nack scenarios:
        a. Queue has max-length and overflow reject-publish
        b. Internal problems with Erlang

+ Two Types of Publisher Confirms:
    1. Synchronous
        Publisher waits after each publish until RabbitMQ confirms.
        Simple but slower (low throughput).
     
    2. Asynchronous
        Publisher keeps sending messages and handles confirms later via callback.
        Much faster (high throughput).


```
Publisher ---> Exchange ---> Queue
     ^                          |
     |                          v
     |<------ ACK / NACK -------+
```

###### 1. Synchronous confirm

```py
# The confirm_delivery() makes basic_publish() callable to returns a boolean.
channel.confirm_delivery()           # Enable publisher confirms on this channel
try:
    # basic_publish() will now block until it gets an ack/nack from the broker.
    # It returns True if confirmed (ack), False if not (nack) from rabbitmq
    is_confirmed = channel.basic_publish(
        exchange='some_exchange',
        routing_key='some.routing.key',
        body='Hello World!',
        properties=pika.BasicProperties(
            delivery_mode=2,  # make message persistent
        ),
        mandatory=True  # Ensure message is routed to a queue
    )
    if is_confirmed:
        print('Message was confirmed by broker!')
    else:
        print('Message was NOT confirmed (nacked)!')

except pika.exceptions.UnroutableError:
    print('Message was returned (unroutable)!')
except pika.exceptions.NackError:
    print('Message was nacked!')
```

###### 2. Asynchronous confirm

```py
def on_delivery_confirmation(frame):                               # Callback function
    if frame.method.NAME == 'Basic.Ack':
        print(f'Message confirmed (delivery_tag: {frame.method.delivery_tag})')
    elif frame.method.NAME == 'Basic.Nack':
        print(f'Message NOT confirmed (nacked)! (delivery_tag: {frame.method.delivery_tag})')

channel.confirm_delivery()                                       #  Enable publisher confirms on the channel
channel.add_on_return_callback(on_delivery_confirmation)          # Add callback to listener
channel.basic_publish(
    exchange='',
    routing_key='async_confirm_test',
    body=message,
    properties=properties,
    mandatory=True   # Ensure message is routed to a queue
)
```


###### policies ensuering delivery to queue

```py
# 1. publisher side
channel.basic_publish(..., mandatory=True)                                         # Return message if unroutable
channel.basic_publish(properties=pika.BasicProperties(delivery_mode=2))           # Persistent message

# 2. broker side
channel.queue_declare(queue='my_queue', durable=True)                               # Set queue to durable
channel.exchange_declare(exchange='my_exchange', durable=True)                     # Set exchange to durable

# 3. consumer side
channel.basic_consume(queue='my_queue', on_message_callback=callback, auto_ack=False)   # inside the callback use ch.basic_ack()
```




### Message Patterns

+ we need patterns since we have diff use cases
+ for example: sometimes we need "fire and forget" and sometimes "publish/subscribe" 
+
+ message patterns:
    1. point-to-point (simple)               1 producer, 1 queue, 1 consumer
    2. Work Queue / Competing Consumers      Distribute tasks among multiple workers
    3. Publish/Subscribe                     Broadcast messages to multiple consumers
    4. Routing (Direct Exchange)             Selective message receiving based on routing key
    5. Topics                                Pattern-based message routing
    6. RPC (Request-Reply)                   Remote procedure calls
    7. Dead Letter Queue (DLQ)               Handle failed or expired messages
    8. Priority Queue                        Process high-priority messages first
    9. Sharded Queue                         Distribute load across multiple queues


###### 0. Fire and forget

+ just set `TTL` which means if no one consume a msg if will delete

###### 1. Point-to-Point (simple)

+ One producer, one queue, one consumer
+ [Producer] -> (Default Exchange) -> [Queue] -> [Consumer]

```py
# Producer
channel.queue_declare(queue='hello')
channel.basic_publish(
    exchange='',
    routing_key='hello',
    body='Hello World!'
)

# Consumer
def callback(ch, method, properties, body):
    print(f"Received {body}")

channel.queue_declare(queue='hello')
channel.basic_consume(
    queue='hello',
    on_message_callback=callback,
    auto_ack=True
)
channel.start_consuming()
```

###### 2. Work Queue / Competing Consumers

+ Distribute tasks among multiple workers
+ [Producer] -> [Task_Queue] -> [Consumer][Consumer][Consumer]

```py
# Producer (round-robin distribution)
channel.queue_declare(queue='task_queue', durable=True)
channel.basic_publish(
    exchange='',
    routing_key='task_queue',
    body=message,
    properties=pika.BasicProperties(
        delivery_mode=2,  # make message persistent
    )
)

# Consumer (fair dispatch)
channel.queue_declare(queue='task_queue', durable=True)
channel.basic_qos(prefetch_count=1)  # Fair dispatch
channel.basic_consume(
    queue='task_queue',
    on_message_callback=callback
)
```

###### 3. Publish/Subscribe
+ Broadcast messages to multiple consumers
+ It's a "one-to-many" pattern
+ [Producer] -> [Fanout Exchange] -> [Queue] -> [Consumer][Consumer][Consumer]


```py
# Producer
channel.exchange_declare(exchange='logs', exchange_type='fanout')
channel.basic_publish(
    exchange='logs',
    routing_key='',  # ignored in fanout
    body=message
)

# Consumer (each gets own temporary queue)
result = channel.queue_declare(queue='', exclusive=True)
queue_name = result.method.queue
channel.queue_bind(exchange='logs', queue=queue_name)
channel.basic_consume(queue=queue_name, on_message_callback=callback)
```

###### 4. Routing (Direct Exchange)
+ Selective message receiving based on routing key
+ [Producer] -> (Direct Exchange {routing_key: 'error'}) -> [Queue {binding_key: 'error'}] -> [Consumer]

```py
# Producer
channel.exchange_declare(exchange='direct_logs', exchange_type='direct')
channel.basic_publish(
    exchange='direct_logs',
    routing_key='error',  # or 'warning', 'info'
    body=message
)

# Consumer (subscribe to specific severities)
channel.queue_bind(
    exchange='direct_logs',
    queue=queue_name,
    routing_key='error'  # can bind multiple keys
)
```


###### 5. Topics

+ Pattern-based message routing
+ example: Message "quick.orange.rabbit" matches "*.*.rabbit" and "quick.orange.#".

```py
# Producer
channel.exchange_declare(exchange='topic_logs', exchange_type='topic')
routing_key = 'kern.critical'  # e.g., 'app.error', 'db.query.slow'
channel.basic_publish(
    exchange='topic_logs',
    routing_key=routing_key,
    body=message
)

# Consumer (pattern matching)
channel.queue_bind(
    exchange='topic_logs',
    queue=queue_name,
    routing_key='*.critical'  # or 'kern.*', '#.error'
)
```

###### 6. RPC (Request-Reply or Request-Response)

+ Remote procedure calls
+ call a function (service) on another application, as if it were local — but under the hood, it uses message queues.
+ both client and server act as consumers and producers which means send/receive messages.
+ suppose client as producer and server as consumer.
+ in this model we have multiple clients and one server (or more)
+ use case: distribute tasks among multiple workers (servers) and wait for results
+ use case: when we should impleament one time-consuming service for multiple clients (consumers)
+ we have 2 queues in this senario: 1. 'request queue' and 2. 'callback queue'.
    + every client has its own 'callback queue'
    + server has 'request queue'
+ when we have multiple clients, clients recieve theier messages by correlation_id
+ How it works:
    1. Client sends a request message to a 'request queue'.
    2. Server (worker/consumer) listens on that queue, processes the request, and sends back a response.
    3. Client waits for the response on a 'callback queue'.

```
+------------------+                     +-------------------+
|                  |  Request Message    |                   |
|      Client      +-------------------->+      RPC Queue     |
|                  | (correlation_id,    |                   |
+--------+---------+  reply_to=callback) +---------+---------+
         ^                                           |
         |                                           v
         |                                 +---------+---------+
         |                                 |                   |
         |                                 |       Server      |
         |                                 | (Consumes request |
         |                                 |  from RPC queue,  |
         |                                 |  processes, and   |
         |                                 |  sends response)  |
         |                                 +---------+---------+
         |                                           |
         | Response Message                          |
         +<------------------------------------------+
           (correlation_id, sent to reply_to (callback_queue))
```


```py
# Client
result = channel.queue_declare(queue='', exclusive=True)       # system-generate queue for RPC
callback_queue = result.method.queue                           # name of generated queue 

correlation_id = str(uuid.uuid4())                            #  generates a random UUID  (Universally Unique Identifier)  (128-bit number)
channel.basic_publish(
    exchange='',                                         #  use default exchange
    routing_key='rpc_queue',                              # same queue in server
    properties=pika.BasicProperties(                      # correlation_id & reply_to: Essential for implementing the RPC pattern.
        reply_to=callback_queue,                          # when client recievs the respone from server, it detects that with correlation_id
        correlation_id=correlation_id,                    # at first client, glues correlation_id to every seding request to server
    ),                                                    # properties define headers of message, reply_to and correlation_id are headers
    body='request data'
)

# Server
def on_request(ch, method, props, body):
    response = process_request(body)                      # process_request() is what we do with request
    ch.basic_publish(                                     # inside consumer callback we send response to client 
        exchange='',                                       # use default exchange
        routing_key=props.reply_to,                         # props are same as properties in client
        properties=pika.BasicProperties(
            correlation_id=props.correlation_id            # same correlation_id from client
        ),
        body=str(response)
    )
    ch.basic_ack(delivery_tag=method.delivery_tag)        # in callback we send ack to exchange to delete message as other patterns

channel.queue_declare(queue='rpc_queue')
channel.basic_consume(queue='rpc_queue', on_message_callback=on_request)     # server is consumer generally
```


###### 7. Dead Letter Queue (DLQ)


```py
# Setup DLX
channel.exchange_declare(exchange='dlx', exchange_type='direct')      # dlx is name of exchange
channel.queue_declare(queue='dead_letters')

# Main queue with DLQ config
channel.queue_declare(
    queue='main_queue',
    arguments={
        'x-dead-letter-exchange': 'dlx',
        'x-dead-letter-routing-key': 'main_queue_dl',
        'x-message-ttl': 60000  # 1 minute TTL
    }
)

channel.queue_bind(
    exchange='dlx',
    queue='dead_letters',
    routing_key='main_queue_dl'
)
```


###### 8. Priority Queue


```py
channel.queue_declare(
    queue='priority_queue',
    arguments={'x-max-priority': 10}  # Support 10 priority levels
)

# Producer
channel.basic_publish(
    exchange='',
    routing_key='priority_queue',
    properties=pika.BasicProperties(priority=9),  # High priority
    body=message
)
```



###### 9. Sharded Queue

+ Distribute load across multiple queues

```py
# Producer - shard by some key
shard_id = hash(user_id) % 10
queue_name = f'user_events_{shard_id}'
channel.basic_publish(
    exchange='',
    routing_key=queue_name,
    body=message
)

# Consumer - listen to specific shard
channel.basic_consume(
    queue=f'user_events_{shard_id}',
    on_message_callback=callback
)
```











