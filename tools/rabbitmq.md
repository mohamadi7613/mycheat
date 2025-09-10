

# RabbitMQ

### why we need broker?

+ decouple and scale our application in a distributed system
+ integrate systems written in diff languages
+ aggregate info -> format info -> queue info for using later -> transform info
+ use `dump broker`: it means "No Logic Inside, Only Routing"  -> docouple info
+ increase fault tolerance level
+ high scalability


#### Why RabbitMQ?

######## 1. benefits

1. integrates diff protocols (ampq, mqtt, stomp, ...)
2. multiple auth schema
3. load balancing in a good way  (you can add more consumers to a single queue without scaling anything else)
4. built-in clustring
5. extenedable + configurable + supports plugins
6. written in Erlang
    + programing language for OTP (Open Telecom Platform)
    + focus on network and failure tolerance
7. widely used, open source, proven system

######## 2. drawbacks

1. lots of configuration
2. Queues have lots of properties
3. Tendncy for application might hide problems


### Erlang

+ process in Erlang: a lightweight concurrent unit of execution managed by the BEAM VM (Erlang’s virtual machine). 
+ processes are not OS processes or even OS threads — they’re much lighter and optimized for massive concurrency.
+ The maximum number of simultaneously alive Erlang processes is by default 1,048,576. (+1M)


### Kafka vs Rabbitmq

+ Type:
    + kafka is a 'message bus' which means it should store messages for some time (kafka need more storage)
    + rabbitmq is a 'message broker' which means it does not need to store messages
+ Artitecture:
    + kafka is log-based:           events written to immutable partitions, consumers read sequentially
    + rabbitmq is message-based:    messages pushed into queues, consumers pull from queues
+ Throughput:
    + kafka has higher throughput
+ Use case:
    + kafka: 1. Streaming pipelines 2. real-time analytics 3. event-driven architectures with high throughput
    + rabbitmq: 1. job distribution 2. async communication 3. microservices


### Install

```bash
# docker
docker pull rabbitmq

# windows 1. install Erlang 2. install .exe file

# linux
```

###  AMQP 

+ AMQP = Advanced Message Queuing Protocol
+ an application layer protocol for message-oriented middleware
+ it defines its own architecture, but in simple terms it has direct contact with various applications
+ it desined for async communication
+ RabbitMQ implements AMQP
+ AMQP message has 3 parts:
    1. Header           metadata key-value pairs about AMQP specification
    2. Properties       metadata key-value pairs about application
    3. Body             payload

#### Core components in AMQP

+ [Rabbitmq simulator](https://tryrabbitmq.com/)

######## 1. Broker

1. Queue:           A buffer that "stores" messages
2. Exchange:        Receives messages from producers and "routes" to queues
3. Binding:         Link between exchange and queue
4. Message:         Data sent from producer to consumer
5. Channel:         Lightweight connection inside a TCP connection
6. Virtual Host (Vhost):    Isolated environment within RabbitMQ instance

######## 2. Clients

1. Producer:        Application that "sends" messages
2. Consumer:        Application that "receives" messages

```
AMPQ:
    Producer → sends messages
    Exchange → routes messages
    Queue → stores messages
    Consumer → receives messages      [whenever message consumed, it will be deleted from queue]
    Binding → which queue the messages should be routed to

                    -------------------------------------
┌─────────┐       ┌──────────┐    Binding       ┌─────────┐       ┌──────────┐
│ Producer│ ───▶  │ Exchange │  ────────────▶   │  Queue  │ ───▶  │ Consumer │
└─────────┘       └──────────┘                  └─────────┘       └──────────┘
                    -------------------------------------
```

+ we can hev multiple queues in one exchange
+ we can have multiple producers and multiple consumers

```
┌─────────┐       ┌──────────┐       
│ Producer│ ───▶  │ Exchange │ 
└─────────┘       └────┬─────┘
                       │
         ┌─────────────┼─────────────┐    Binding
         ▼             ▼             ▼
     ┌─────────┐   ┌─────────┐   ┌─────────┐
     │  Queue  │   │  Queue  │   │  Queue  │
     └────┬────┘   └────┬────┘   └────┬────┘
          │             │             │
          ▼             ▼             ▼
     ┌──────────┐  ┌──────────┐  ┌──────────┐
     │ Consumer │  │ Consumer │  │ Consumer │
     └──────────┘  └──────────┘  └──────────┘
```

### Exchange

+ Exchange is like a post office inside RabbitMQ
+ Its job is to receive messages from producers and decide where to route them
+ Producers never send messages directly to a queue. They always send them to an exchange
+ Round-robin dispatching (sends each message to the next consumer in sequence)


### Common Exchange Types

1. Direct:  Routes with exact routing key match
2. Fanout:  Broadcasts to all bound queues
3. Topic:   Routes based on pattern matching (flexible)
4. Headers: Routes based on message headers (slow)
5. custome exchange: consistent-hash-plugin


##### 1. Direct type

1. Producers publish messages with a specific routing_key
2. Direct Exchange checks the routing key
3. Each Queue is bound with a specific key (A or B)
4. The message is routed to the Queue that matches the routing key ("A" in this example)
5. Consumers read from their respective queues

```
                    ┌─────────┐               
                    │ Producer│      data: "data" , routing_key="A"
                    └─────────┘
                        │
                        │
                 ┌──────────────────┐
                 │  Direct Exchange │  type: Direct
                 └─────────┬────────┘
                           │
                           │
          routing_key="A"  │   routing_key="B"
                 ▲         │          ▲
                 │         │          │
                 │         │          │
         ┌─────────────────┼─────────────────┐
         ▼                 │                 ▼
 ┌─────────────┐           │           ┌─────────────┐
 │   Queue A   │◀──────────┘           │   Queue B   │
 │  (bind=A)   │    data= "data"       │  (bind=B)   │
 └──────┬──────┘                       └──────┬──────┘
        ▼                                     ▼
 ┌─────────────┐                       ┌─────────────┐
 │  Consumer A │                       │  Consumer B │
 └─────────────┘                       └─────────────┘
```


##### 2. Fanout type

1. Producer sends a message to Fanout Exchange
2. Fanout ignores routing keys, and does not need any binding key 
3. The message is duplicated to all bound queues
4. Each Consumer receives its copy

```
                ┌─────────┐              
                │ Producer│      data: "data", routing_key= null 
                └────┬────┘
                     │
                     ▼
             ┌──────────────────┐
             │  Fanout Exchange │  type: Fanout
             └─────────┬────────┘
                       │
                       │
         ┌─────────────┼─────────────┐
         ▼             ▼             ▼
     data= "data"  data= "data"  data= "data"
     ┌─────────┐   ┌─────────┐   ┌─────────┐
     │  Queue  │   │  Queue  │   │  Queue  │
     └────┬────┘   └────┬────┘   └────┬────┘
          │             │             │
          ▼             ▼             ▼
     ┌──────────┐  ┌──────────┐  ┌──────────┐
     │ Consumer │  │ Consumer │  │ Consumer │
     └──────────┘  └──────────┘  └──────────┘
```

##### 3. Topic type

+ when we have a few topics we can use 'direct exchange', but when we have lots of diff topics we use 'Tpoic exchange'
+ for handeling topic exchange we use 'Regex-like' pattern
1. Producer publishes a message with routing_key = "user.signup".
3. Topic Exchange matches keys against binding patterns (regex):
    a. user.* → matches exactly one word after user. ( user.* gets messages like: user.log, user.err and not accept user.sport.log, user.news.log)
    b. user.# → matches one or more words after user. (user.# accecpts: user.log, user.news.log, user.news.sport.log ,...)
    c. order.* → does not match, so Queue C doesn’t get this one.
4. Consumers receive messages from their respective queues.

```
                     ┌──────────────────┐
                     │   Topic Exchange │
                     └─────────┬────────┘
                               │
                 routing_key="user.signup"
                               │
           ┌───────────────────┼────────────────────┐
           ▼                   ▼                    ▼
    ┌─────────────┐      ┌─────────────┐      ┌─────────────┐
    │   Queue A   │      │   Queue B   │      │   Queue C   │
    │ (bind:      │      │ (bind:      │      │ (bind:      │
    │  user.*)    │      │  user.#)    │      │  order.*)   │
    └──────┬──────┘      └──────┬──────┘      └──────┬──────┘
           ▼                   ▼                    ▼
    ┌─────────────┐      ┌─────────────┐      ┌─────────────┐
    │  Consumer A │      │  Consumer B │      │  Consumer C │
    └─────────────┘      └─────────────┘      └─────────────┘
```

#### 4. Headers type

+ if you can its better to use topic exchange, since headers exchange is slower
+ route messages based on multiple attributes
+ The binding between a queue and a headers exchange requires a special argument called `x-match`:
    1. x-match = all           
        a. Logical AND           
        b. The message must contain all the headers specified in the binding, and their values must match
    2. x-match = any
        a. Logical OR
        b. The message must contain at least one of the headers and their values specified in the binding must match
+ headers exchange ignores routing_key, so leave it empty in code



### Pre-Declared Exchanges

+ all these exchanges are durable

1. "" (empty string)
    + default exchange
    + type= direct
    + Automatic binding
    + Simple point-to-point messaging to a known queue name.
3. amq.direct	
    + type= direct
    + Explicit direct routing with a named exchange.
4. amq.fanout	
    + type = fanout	
    + Pub/Sub broadcasting to all bound queues.
5. amq.topic	
    + type= topic	
    + Pattern-based routing using wildcards.
6. amq.headers	
    + type= headers		
    + Routing based on message headers (rare).
7. amq.rabbitmq.trace	
    + type= topic	
    + Debugging with the Firehose tracer feature.


### Node

+ A node in RabbitMQ refers to a single Erlang runtime instance
+ A node is one running Erlang virtual machine (BEAM) that has the RabbitMQ code loaded into it.
+ 1 RabbitMQ node = 1 Erlang node running RabbitMQ
+ if a node goes down, messaging will stop
+ it is used for clustering

### Queue

+ Queue: A buffer that stores messages until a Consumer retrieves them.
+ queue is ordered collection with FIFO behavior (first in, first out)  (we can change this behavior, but not recommended)
+ queues are FIFO for producers but there is no garantee to behave exactly the same for consumers
+ both producer and consumer can create queue
+ queue is located on single node and refrenced by unique name
+ 1 queue = 1 Erlang process
+ up to 16384 messages can be loaded into RAM from one queue
+ we have perdefined queues which prefixed by "amq." and used for rabbitmq internal purposes
+ queue bound to Exchanges via bindings (Producers never send directly to a queue → always to an exchange.)
    + Default binding: amq.default
    + the default exchange bind to every queue
+ queues can have many properties :
    a. durable (survives broker restart)
    b. exclusive (for one connection only)
    c. auto-delete (deleted when no longer in use)
+ with `policies` we can change the behavior of quote without recreating it


### Binding

+ binding: Bind Queue to Exchange       `exchange --[binding]--> queue`
+ tells RabbitMQ how to route messages from an exchange to specific queues
+ Includes optional parameters like routing keys and headers
+ `rabbitmqctl list_bindings`
+ RabbitMQ server has a default exchange ---> "" (empty string) --> it is a direct exchange
+ The default exchange is implicitly bound to every queue


##### Durability vs Persistency

1. Durability:
    a. Applies to queues (and exchanges)
    b. A durable queue will survive broker restarts.
    c. `channel.queue_declare(queue='task_queue', durable=True)`
    d. ``
    e. Durability only ensures the queue definition survives, not the messages in it.

1. Persistence:
    a. Applies to messages
    b. A persistent message is written to disk instead of RAM
    c. If the queue is not durable, persistent messages will still be lost on restart
    d. Persistence only works if the queue and exchange be durable
    e. set `delivery_mode=2` in message properties
    ```py
    channel.basic_publish(
        exchange='',
        routing_key='task_queue',
        body=message,
        properties=pika.BasicProperties(
            delivery_mode=2                 # make message persistent
        )
    )
    ```

### Channel

+ Connection =  long-lived TCP socket to RabbitMQ
+ Channel =     A virtual connection inside a single TCP connection between your application and the RabbitMQ broker.
+ channel does the routing
+ A TCP connection is expensive (slow to create, uses system resources, use handshaking).
+ A Channel is lightweight and `multiplexes` (shares) the same TCP connection.
+ Avoids opening many TCP connections by multiplexing the same connection with channels.
+ Applications usually open "one connection" per app but "multiple channels" per thread/producer/consumer.
+ Basic methods:
    a. Basic.publish
    b. Basic.consume
    c. Basic.ack

```
                    (1 TCP connection)
┌────────────────────────────────────────────────────────┐
│  ┌────────┴────────┐                                   │
│  │   Channel 1     │ → Publishes to exchange           │
│  ├─────────────────┤                                   │
│  │   Channel 2     │ → Consumes from queue             │
│  ├─────────────────┤                                   │
│  │   Channel 3     │ → Another producer/consumer       │
│  └─────────────────┘                                   │
└────────────────────────────────────────────────────────┘
```



### Vhost

+ A vhost (virtual host) is like a namespace or a separate environment inside the broker
+ It isolates exchanges, queues, bindings, users, and permissions.
+ Each vhost has its own queues, exchanges, and bindings.
+ One RabbitMQ server can host multiple vhosts, its like diffrenet tables in one database
+ you cannot communicate between vhosts (isolation)
+ there is no direct communicatation between mini-brokers
+ Users must be given permissions per vhost.
+ Default vhost is /
+ in UI pannel access vhosts from "admin" tab

```
                   ┌────────────────────────────┐
                   │      RabbitMQ Server       │
                   │ (1 broker process, 1 node) │
                   └─────────────┬──────────────┘
                                 │
        ┌────────────────────────┼───────────────────────────────┐
        ▼                        ▼                               ▼
┌────────────────────┐      ┌────────────────────┐      ┌────────────────────┐
│    vhost /         │      │    vhost /app      │      │    vhost /test     │
│   (default)        │      │                    │      │                    │
│  ┌─────────────┐   │      │  ┌─────────────┐   │      │  ┌─────────────┐   │
│  │  Queue Q1   │   │      │  │  Queue Q2   │   │      │  │  Queue Q3   │   │ --> A queue isolated within its vhost.
│  │ Exchange E1 │   │      │  │ Exchange E2 │   │      │  │ Exchange E3 │   │ --> An exchange isolated within its vhost.
│  └─────────────┘   │      │  └─────────────┘   │      │  └─────────────┘   │
└──────┬─────────────┘      └──────┬─────────────┘      └──────┬─────────────┘
       │                           │                           │
+-------+---------+        +-------+---------+          +-------+---------+
|  User: admin    |        |  User: service_a|          |  User: service_b|     --> A user with permissions granted for this specific vhost.
|  Perms: /.*     |        |  Perms: /app_a.*|          |  Perms: /app_b.*|
+-----------------+        +-----------------+          +-----------------+
```

### rabbitmq patterns

+ we need patterns since we have diff use cases
+ for example: sometimes we need "fire and forget" and sometimes "publish/subscribe" 
+
+ message patterns:
    1. point-to-point (simple queue)                    1 producer, 1 queue, 1 consumer
    1. fire and forget                                  same as point-to-point but set TTL
    2. Work Queue / Task Queue/ Competing Consumers     Distribute tasks among multiple workers (balancing traffic)
    3. Publish/Subscribe                                Broadcast messages to multiple consumers (fanot)
    4. Routing (Direct Exchange)                        Selective message receiving based on routing key
    5. Topics                                           Pattern-based message routing
    6. RPC (Request-Reply)                              Remote procedure calls
    7. Dead Letter Queue (DLQ)                          Handle failed or expired messages
    8. Priority Queue                                   Process high-priority messages first
    9. Sharded Queue                                    Distribute load across multiple queues

#### 1. RPC

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


#### 2. DLX (Dead-Letter-EXchange)

+ first declare a DLX exchange, It can be of any type: direct, topic, fanout
+ for DLX we should set some properties in main queue
+ DLX is a property for queue

```py
channel.queue_declare(
    queue='main_queue',
    arguments={
        'x-dead-letter-exchange': 'exchange.dlx',       # value is name of exchange
        'x-dead-letter-routing-key': 'main_queue_dl',
        'x-message-ttl': 60000  # 1 minute TTL
    }
)
```

```
+----------------+      +-----------------+      +-----------------------+
|   Producer     |      |                 |      |                       |
|                |      |   Main Exchange |      |   Main Queue          |      not recived by consumer
| (Publishes Msg)|----->|                 |----->| (x-dead-letter-exchange|--------%-------%--------%-----> Consumer
|                |      | (e.g., 'app.')  |      |    = 'exchange.dlx')  |             redirect to DLX
+----------------+      +-----------------+      |                       |
                                                 +----------+------------+
                                                            |
                                  Conditions:               |
                                  1. Rejection (requeue=false)
                                  2. TTL Expired            |
                                  3. Queue Max Length       | Message
                                                            | becomes
                                                            | "dead"
                                                            v
+----------------+      +-----------------+      +----------+------------+
|   Administrator|      |                 |      |                       |
|     or         |      |  Dead Letter    |      |   Dead Letter Exchange  |
|   Consumer     |<-----|    Queue        |<-----|  name = exchange.dlx  |
| (Analyses Msg) |      |                 |      |                      |
|                |      |                 |      |                       |
+----------------+      +-----------------+      +-----------------------+
```



### python
+ read pika.md in this repo


### commands
+ you can run these commands inside docker container

```bash

# Start/stop server
docker run -d -p 15672:15672 -p 5672:5672 --name rabbitmq_my_name rabbitmq:3
rabbitmq-server start
rabbitmqctl stop


# Virtual hosts
rabbitmqctl add_vhost myvhost
rabbitmqctl list_vhosts


# 1. rabbitmqctl
rabbitmqctl list_queues
rabbitmqctl list_exchanges
rabbitmqctl list_bindings

# 2. rabbitmqadmin
rabbitmqadmin delete queue name=myqueue                     # Delete a queue

# 3. rabbitmq-plugins
```

### Windows

```bash
/rabbitMQServer/sbin/rabbitmq-server.bat             # start server windows      # check in services from task manager 
type /rabbitMQServer/sbin/rabbitmq.bat             # edit this file and specify the location of config file
rabbitmq-plugins.bat enable rabbitmq-management
```



###### 1. User Permissions

+ User = an account that connects to RabbitMQ
+ default username and password: "guest"
+ Permissions are always assigned per vhost.
+ Each permission is a regex applied to resource names (exchange/queue).
+ 
+ Each user has 3 regex-based permissions in a vhost:
    1. Configure               What resources the user can create/modify (queues, exchanges, bindings).
    2. Write                   Where the user can publish messages (exchanges).
    3. Read                    What resources the user can consume from (queues, exchanges). 
+
+ Tags:
    1. administrator → can manage users, policies, vhosts.
    2. monitoring → can view stats.
    3. management → can access UI.
    4. policymaker → can set policies.
    5. Impersonator
    6. (No tag) → normal user.

```bash
# User management
rabbitmqctl add_user myuser mypassword
rabbitmqctl delete_user myuser
rabbitmqctl list_users
rabbitmqctl change_password myuser newpassword

rabbitmqctl set_user_tags myuser administrator                    # Set User Tags (roles)
rabbitmqctl set_user_tags monitor monitoring                      # Read-only Monitoring User

rabbitmqctl set_permissions -p /vhost myuser ".*" ".*" ".*"     # 1. Configure regex 2. Write regex 3. Read regex   # Full Access
rabbitmqctl set_permissions -p / myproducer "" ".*" ""           # Can publish but not consume.     # "" or "^$" means no permission
rabbitmqctl set_permissions -p / myconsumer "" "" ".*"          # Can consume but not publish.
rabbitmqctl set_permissions -p / myconsumer "" "" "^(Q1|Q2)$"          # Consume from Q1 or Q2 and not other queues
rabbitmqctl list_user_permissions myuser                         # List Permissions of a User
rabbitmqctl list_permissions -p /vhost                             # List Permissions of a vhost
rabbitmqctl clear_permissions -p /vhost myuser                     # Clear Permissions of a User
```

##### 2. Policies

+ Policies let you apply configuration rules (key–value pairs) to multiple queues and exchanges automatically.

```bash
rabbitmqctl list_policies                                           # List Policies
rabbitmqctl set_policy [-p vhost] name pattern definition [priority]      # Set Policy
rabbitmqctl clear_policy policy_name
```


###### dashboard (rabbitmq_management)

+ we can send message in dashboard in 2 ways: 1. exchange tab 2. queues tab
+ we can create binding in dashboard in 2 ways: 1. exchange tab 2.queues tab


###### dashboard API

+ we can create our own UI by using REST api providing by rabbitmq_management
+ `localhost:15672/api/nodes`


###### rabbitmqadmin

+ localhost:15672/api/vhosts
+ localhost:15672/api/overview

```bash
rabbitmq-plugins enable rabbitmq_management              # Requires the rabbitmq_management plugin to be enabled.

rabbitmqadmin list users
rabbitmqadmin list vhosts
rabbitmqadmin list permissions

rabbitmqadmin list queues                                  # List all queues
rabbitmqadmin declare queue name=myqueue durable=true          # Create a queue
rabbitmqadmin delete queue name=myqueue                     # Delete a queue

rabbitmqadmin list exchanges                                                    # List exchanges
rabbitmqadmin declare exchange name=myexchange type=direct durable=true           # Create exchange
rabbitmqadmin delete exchange name=myexchange                                     # Delete exchange
rabbitmqadmin list bindings                                                     # List bindings

# Workflow
rabbitmqadmin declare queue name=task_queue durable=true                         # 1. Create queue
rabbitmqadmin declare exchange name=logs type=fanout durable=true                   # 2. Create exchange
rabbitmqadmin declare binding source=logs destination=task_queue                # 3. Bind queue to exchange
rabbitmqadmin publish exchange=logs payload="Hello RabbitMQ"               # 4. Publish message
rabbitmqadmin get queue=task_queue requeue=false                           # 5. Read message (consume)
```




### Config

+ we can config rabbitmq with:
    1. config file
        a. new in sysctl format           --> good for simple config (human readable)    ---> `rabbitmq.config`
        b. old in erlang format           --> good for complex config                    ---> `advanced.config`
        c. both: we can have both formats
    2. env variables


#### 1. rabbitmq.config

```bash
vim /etc/rabbitmq/rabbitmq.conf               # edit config file
docker container logs rabbitmq | head -n 20                  # check logs to see the location of config file
rabbitmqctl reload_config

## ------------------------------
## Networking
## ------------------------------
listeners.tcp.default = 5672          # Default AMQP listener (for producers/consumers)
management.listener.port = 15672      # RabbitMQ Management UI web port
management.listener.ip = 0.0.0.0      # Listen on all network interfaces (not just localhost)

## ------------------------------
## Authentication & Default Users
## ------------------------------
loopback_users.guest = false          # Allow 'guest' user to connect remotely (not only localhost)
default_user = admin                  # Create a default user on startup (username = admin)
default_pass = secret                 # Default user's password (should change in production!)

## ------------------------------
## Clustering
## ------------------------------
cluster_formation.peer_discovery_backend = rabbit_peer_discovery_classic_config   # Cluster discovery method
cluster_formation.classic_config.nodes.1 = rabbit@node1   # Node 1 hostname in the cluster
cluster_formation.classic_config.nodes.2 = rabbit@node2   # Node 2 hostname in the cluster

## ------------------------------
## Resource Limits
## ------------------------------
vm_memory_high_watermark.relative = 0.4   # Max memory usage (40% of system RAM before flow control triggers)
disk_free_limit.relative = 1.5            # Minimum free disk space = 1.5x system RAM (prevents data loss)

## ------------------------------
## Logging
## ------------------------------
log.console = true             # Enable logging to console (stdout)
log.console.level = info       # Console log level = info (can be debug, warning, error, etc.)
log.file.level = warning       # File log level = warning (only warnings and errors go to log files)

## ------------------------------
## TLS / SSL
## ------------------------------
listeners.ssl.default = 5671              # Enable SSL listener on port 5671
ssl_options.cacertfile = /etc/rabbitmq/certs/ca.pem        # Path to CA certificate
ssl_options.certfile   = /etc/rabbitmq/certs/server.pem    # Path to server's public certificate
ssl_options.keyfile    = /etc/rabbitmq/certs/server-key.pem # Path to server's private key
ssl_options.verify     = verify_peer     # Require clients to present valid certificates
ssl_options.fail_if_no_peer_cert = true  # Reject clients that do not provide a certificate
```

#### 2. advanced.config

```bash
vim /etc/rabbitmq/advanced.config            # edit config file

[
 {rabbit, [
   {vm_memory_high_watermark, 0.4},
   {disk_free_limit, {mem_relative, 1.0}}
 ]}
].
```

#### 3. Environment Variables

+ we can also config rabbitmq with environment variables
+ we can also set environment variables using docker

```bash
vim /etc/rabbitmq/rabbitmq-env.conf

# rabbitmq-env.conf
NODENAME=rabbit@myhost
NODE_PORT=5672
MNESIA_BASE=/var/lib/rabbitmq/mnesia
CONFIG_FILE=/etc/rabbitmq/rabbitmq
```

###### Erlang Cookie in RabbitMQ

+ Erlang nodes (like RabbitMQ brokers) communicate securely using a shared secret called the Erlang cookie.
+ it is create automatically by RabbitMQ
+ Think of it like a password that all nodes in the cluster must share
+ It’s just a string of characters and must be identical on all nodes in a RabbitMQ cluster
+ The cookie is stored in a hidden file named `.erlang.cookie`

```bash
cat /var/lib/rabbitmq/.erlang.cookie             # print cookie in terminal
type %HOMEDRIVE%%HOMEPATH%\.erlang.cookie        # type is equivalent to cat in windows
```




### RabbitMQ Clustering

+ clustering means multiple instance of rabbitmq which seems to be as a single rabbitmq from the outside
+ cluster is a group of RabbitMQ nodes (servers) that share metadata (users, vhosts, queues, exchanges, bindings).
+ Nodes can run on the same machine (for dev) or different machines (prod).
+ Clustering improves availability (fail safe) & scalability (load balancing)
+ we can check clusters in RabbitMQ Management UI, overview tab
+ Types of Data in Cluster:
    1. metadata
        - replicated across all nodes
        - Exchanges, queues (names only), bindings, users, vhosts, permissions, policies
    2. Queue contents
        - by default stored on a single node only
        - by default if a node falls, queue contents are lost. (solution is 'Mirrroed queues')
+ Node Types
    1. RAM Node → stores cluster state in memory (faster, but state lost if rebooted)
    2. Disk Node → stores cluster state on disk (at least one per cluster is required)

```bash
# create a cluster with 3 nodes                            # Copy the same .erlang.cookie file to all nodes (/var/lib/rabbitmq/.erlang.cookie)
rabbitmq-server -detached -name rabbit@node1               # Start a node with a name
rabbitmq-server -detached -name rabbit@node2               # set these names for hostname of linux server
rabbitmq-server -detached -name rabbit@node3

# Join another node to cluster                           # on node2 and node3
rabbitmqctl stop_app                                     # stop it first
rabbitmqctl join_cluster rabbit@node1                   # join this node to node1 (cluster)
rabbitmqctl start_app                                    # after join start it
rabbitmqctl cluster_status                                 # Check cluster status

rabbitmqctl forget_cluster_node rabbit@node2                  # Force remove a node
```


#### high-availability (HA)

+ hitgh-availability means a system stay operational and accessible even if parts of it fail (hardware, software, or network).
+ HA in RabbitMQ:
    1. Classic Mirrored Queues (old, deprecated)
    2. Quorum Queues (modern, recommended)

##### 1. Mirrored Queues

1. One node holds the master queue, and others hold mirrors (slaves)
2. When a client publishes a message → goes to the master queue
3. The master replicates it to all mirrors
4. If the master node fails → one mirror is automatically promoted to master.
5. Deprecated in RabbitMQ 3.8+

##### 2. Quorum Queues

+ Quorum Queues (QQ) is based on the Raft consensus algorithm
1. One node is the leader. Other nodes are followers (replicas).
2. A message is acknowledged only after a majority of nodes confirm it.
3. If the leader fails → a follower is promoted automatically.
4. Deploy at least 3 RabbitMQ nodes (odd number recommended).

+ for QQ we should do 3 things:
    1. Configuration
    2. Queue Creation (1. Manual Declaration or 2. Via Policy)

####### 1. Configuration

```bash
cluster_formation.peer_discovery_backend = rabbit_peer_discovery_classic_config
cluster_formation.classic_config.nodes.1 = rabbit@node1
cluster_formation.classic_config.nodes.2 = rabbit@node2
cluster_formation.classic_config.nodes.3 = rabbit@node3
```

####### 2. Queue Creation: 1. Manual Declaration

```bash
rabbitmqadmin declare queue name=my_quorum_queue durable=true arguments='{"x-queue-type":"quorum"}'
```

####### 2. Queue Creation: 2. Via Policy
+ convert many queues at once

```bash
rabbitmqctl set_policy quorum "^qq\." '{"queue-type":"quorum"}' --apply-to queues
```



### throughput

+ `Throughput` refers to how many messages per second the broker can accept, process, and deliver.
+ It’s a measure of performance (speed) rather than reliability.
+ Factors Affecting RabbitMQ Throughput:
    + Message Size
    + Durability
    + Acknowledgement
    + QoS
    + Number of Connections / Channels
    + Queue Type
    + Hardware
    + Network and clustering

+ Typical Throughput Numbers
    + Transient messages (no persistence):
        ~50K – 100K msg/sec per node.

    + Persistent messages (durable queues):
        ~5K – 20K msg/sec per node (SSD helps a lot).

    + Quorum Queues:
        Typically 2–3x slower than classic queues (due to replication & Raft).

+ Improve Throughput with:
    + SSD disks and enough RAM
    + Use multiple channels per connection.
    + Increase QoS prefetch.
    + If durability isn’t required → send non-persistent messages.
    + Use batch publishing and consumer acknowledgments.
    + make queues as short as possible with: TTL, max-length


### Logging

- Always monitor disk space if file logging is enabled.
- prefer console logging -> forward to ELK / Loki / Splunk.


```bash
cat /var/log/rabbitmq/rabbit@<hostname>.log       # General logs
cat /var/log/rabbitmq/rabbit@<hostname>-sasl.log  # Authentication & errors

rabbitmq-diagnostics log_tail      # Tail current logs
rabbitmq-diagnostics log_location  # Show log file locations
journalctl -u rabbitmq-server      # logging with Systemd (enable in config)
```


#### configuring logging

```bash
# Recommended: "info" for production, "debug" only temporarily.
# levels: debug | info | warning | error | critical  


## Enable console logging                  # logs in foreground
## =======================
log.console = true                 # Default: true     # in Docker collected by the container runtime.
log.console.level = info          # min severity level

## File Logging
## =======================
log.file = true                           # Enable logging to file. Files are rotated automatically.
log.file.level = warning               # Min severity level
log.file.rotation = 10      # number of rotated files to keep     # keeps the 10 most recent log archives and deletes older ones.
log.file.size = 20MB        # max size before rotation       # When exceeded, the file is rotated (archived) and a new one is created.

## Syslog / System Logging
## =======================
log.syslog = true                   # default: false # Enable logging to the system logger (syslog/journald).
log.syslog.level = info             # min severity level
```

### Monitoring


```bash
rabbitmqctl list_queues name messages consumers → queue status
rabbitmqctl list_connections → current connections
rabbitmqctl list_channels → active channels
rabbitmq-diagnostics status → node health
rabbitmq-diagnostics check_running → is it running?
rabbitmq-diagnostics memory_breakdown → memory use
```

### Plugins

+ rabbitmq has built-in plugins, and also we can write our custome plugin
+ you can see list of plugins in admin tab in UI Management
+ commong plugins:
    + 
    + Management Plugins
        1. rabbitmq_management                  - Web UI (http://localhost:15672)

    + Monitoring & Metrics
        1. rabbitmq_prometheus                  – Prometheus endpoint at /metrics.
        2. rabbitmq_top                         – Shows top resource consumers.
        3. rabbitmq_tracing                     – Traces message flow through broker.

    + Shovel & Federation:
        1. rabbitmq_shovel                      – Moves messages between brokers.
        2. rabbitmq_shovel_management           – Adds UI for shovel.
        3. rabbitmq_federation                  – Connects brokers in different networks.
        4. rabbitmq_federation_management       – Adds UI for federation. 

    + Authentication & Authorization
        1. rabbitmq_auth_backend_ldap           – LDAP integration.
        2. rabbitmq_auth_backend_http           – External HTTP-based auth.
        3. rabbitmq_auth_mechanism_ssl          – Auth via SSL certificates.

    + Messaging Protocols
        1. rabbitmq_mqtt                        – MQTT protocol support.
        2. rabbitmq_stomp                       – STOMP protocol support.
        3. rabbitmq_web_stomp                   – STOMP over WebSockets.
        4. rabbitmq_amqp1_0                     – AMQP 1.0 protocol support.

    + Other Useful Plugins
        1. rabbitmq_delayed_message_exchange    – Native delayed message scheduling.
        2. rabbitmq_consistent_hash_exchange    – Load balancing using hashing.
        3. rabbitmq_event_exchange              – Emits broker lifecycle events as messages.
        4. rabbitmq_stream                      – Stream messaging (newer model).


```bash
rabbitmq-plugins help                    # Help    # there are 4 options only
rabbitmq-plugins list                    # List all plugins    # first column e means enabled
rabbitmq-plugins list -e                    # List Only Enabled Plugins
rabbitmq-plugins enable rabbitmq_management    # Enable Plugin
rabbitmq-plugins disable rabbitmq_management    # Disable Plugin
rabbitmq-plugins enable rabbitmq_management rabbitmq_prometheus    # Enable multiple Plugin 
rabbitmq-plugins reset                                           # Reset to default
```
##### 1. Shovle plugin

+ it is a core RabbitMQ plugin
+ The Shovel plugin in RabbitMQ is a tool for moving (copying or transferring) messages between:
    + different brokers,
    + different clusters,
    + different virtual hosts (vhosts) within the same broker.
+ It’s often used for federation, migration, backup, or cross-datacenter replication.
+ it is used in client that consumes a message and publish that message again (indirect)
+ allow coupling
+ Types of Shovels:
    1. Static Shovel → defined in config files (rabbitmq.conf).
    2. Dynamic Shovel → created at runtime using CLI or HTTP API (more flexible and modern).

```bash
rabbitmqctl list_parameters
rabbitmqctl list_shovels
```


####### 1.1 Static Shovel (rabbitmq.conf)

```bash
# Enable shovel and management plugins
rabbitmq-plugins enable rabbitmq_shovel                   # allow to static config
rabbitmq-plugins enable rabbitmq_shovel_management         # allow to dynamic config

# Define a shovel
shovel.my_shovel.src-uri = amqp://user:pass@source-host/%2f
shovel.my_shovel.src-queue = my_source_queue

shovel.my_shovel.dest-uri = amqp://user:pass@dest-host/%2f
shovel.my_shovel.dest-exchange = my_exchange
```

####### 1.2. Dynamic Shovel (CLI)

```bash
rabbitmqctl set_parameter shovel my_dynamic_shovel \
'{
  "src-uri": "amqp://",
  "src-queue": "source_queue",
  "dest-uri": "amqp://",
  "dest-queue": "destination_queue"
}'
rabbitmqctl clear_parameter shovel my_dynamic_shovel              # remove shovel
```

##### 2. tracing plugin

+ Tracing lets you record and inspect messages that flow through RabbitMQ.
+ in simple terms: log something special
+ only use for debugging since it reduce the performance
+ Tracing can generate very large log files quickly
+ how it wokrs?
    1. You define a trace with filter patterns (exchange, vhost, etc).
    2. RabbitMQ copies 'matching messages' into a trace log file.
    3. Each trace is stored in:  `File (/var/log/rabbitmq/tracing/)`


```bash
rabbitmq-plugins enable rabbitmq_tracing       # enable plugin
rabbitmqctl trace_list
rabbitmqctl trace_start -p / my-trace         # start a trace
rabbitmqctl trace_stop -p / my-trace           # stop a trace
rabbitmqctl trace_delete -p / my-trace         # delete a trace
```


##### 3. Consistene Hash Exchange

+ The `rabbitmq_consistent_hash_exchange` plugin introduces a new exchange type `x-consistent-hash`
+ use case:
    1. spliting messages
        + with this plugin we can split long queues (lots of messages)  into smaller ones (few messages)
        + for example, instead of a queue with 20 messages, we will have 2 queues with 5 messages for each one
        + x-consistent-hash distribute messages equally between queues (set weight 1 for all)
    2. spliting messages with wieghts
        + x-consistent-hash can distribute messages into 1 queue twice or 3 times or more of another one
        + if you set 3 for weight of queue A and set 1 for weight of queue B, then queue A recive messages 3 times more than A


```bash
rabbitmq-plugins enable rabbitmq_consistent_hash_exchange           # enable
```

```py
channel.exchange_declare(exchange='hashed_orders', exchange_type='x-consistent-hash')
# Bind 2 queues with different weights
channel.queue_bind(exchange='hashed_orders', queue='order_queue_1', routing_key='1') # Weight 1
channel.queue_bind(exchange='hashed_orders', queue='order_queue_2', routing_key='3') # Weight 3
```











