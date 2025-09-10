
# kafka-python


## 1. producer

```py
from kafka import KafkaProducer

producer = KafkaProducer(bootstrap_servers="localhost:9092")                # producer with minimal config
producer = KafkaProducer(                                             # producer with common config
    bootstrap_servers="localhost:9092",
    acks="all",                 # wait for leader + replicas
    retries=5,                  # retry if send fails
    batch_size=16384,           # batch size in bytes
    linger_ms=5,                # wait before sending batch
    compression_type="gzip"     # or snappy, lz4, zstd
)


producer.send("my-topic", b"hello kafka")                                   # Simple string message (must be bytes)
producer.send("my-topic", "hello world".encode("utf-8"))                    # Encode string
producer.send("my-topic", key=b"user1", value=b"message for user1")         # With key
producer.flush()                                                            # Flush to ensure delivery
```


## 2. consumer

```py
from kafka import KafkaConsumer

consumer = KafkaConsumer(                        # consumer with common config
    "my-topic",
    bootstrap_servers="localhost:9092",
    group_id="my-group",
    auto_offset_reset="earliest",               # "latest" for only new messages   # 1. earliest 2. latest
    enable_auto_commit=True,
    max_poll_records=500
)

for message in consumer:
    print(f"Key={message.key}, Value={message.value}, Offset={message.offset}")
```


## 3. Create Topics (Admin)

```py
from kafka.admin import KafkaAdminClient, NewTopic

admin = KafkaAdminClient(bootstrap_servers="localhost:9092")

topic = NewTopic(name="new-topic", num_partitions=3, replication_factor=1)
admin.create_topics(new_topics=[topic], validate_only=False)
```



























