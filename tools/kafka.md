

# Apachie kafka

+ kafka is a *distributed* *publish-subscribe* *messaging system*
+ Apache Kafka is an event streaming platform used to handle real-time data feeds.
+ kafka is writen with scala, developed by LinkedIn.

## Main Use Cases (post office)

+ Real-time analytics (e.g., monitoring user behavior).
+ Log aggregation (collecting logs from multiple services).
+ Event-driven architectures (microservices communication).
+ Dominoes and chain updates and events for several services. (Ecommerce with lots of services)
+ Data pipelines (streaming from databases, IoT devices, etc.).
+ Messaging system (like RabbitMQ, but optimized for throughput and persistence).


## Kafka vs RabbitMQ

+ Kafka stores messages for a configurable retention time (not deleted after consumption).
+ Consumers can re-read messages (offset-based).
+ Designed for high throughput (millions of messages/sec).
+ Supports both real-time and batch processing.


### install

+ in windows change file path in:
    1. zookeeper.properties:  `dataDir=C:\kafka\kafka_2.12-3.5.0\zookeeper`  
    2. server.properties:  `log.dirs=C:\kafka\kafka_2.12-3.5.0\logs`

```bash
sudo apt install openjdk-11-jdk                  # java --version  (java is prerequrement)
# On Linux/macOS                               # there is no command for getting with apt
tar -xzf kafka_2.13-3.6.1.tgz                    # we should install kafka manually
mv kafka_2.13-3.6.1 ~/kafka
cd ~/kafka                                    # add kafka to PATH
```

## core concepts

1. Topics:          Stream of records (like a table in a database).
2. Partitions:      To manage large volumes of data topics are split into partitions for parallelism.
3. Brokers:         Kafka servers that store data and serve clients.
4. Producers:       Applications that write data to Kafka topics.
5. Consumers:       Applications that read data from Kafka topics.
6. Consumer Groups: Groups of consumers that share workload.
7. Zookeeper:       Manages brokers, leader election, and metadata (deprecated in newer Kafka versions).
8. Replication:     Copies of partitions for fault tolerance. (backup)
9. Offset:          A unique ID for each message within a partition. (It starts from 0 and increases by 1 for every new message.)





### topic and partition

+ `topic` is a named stream of messages in Kafka.
+ Topic is like a category of feeds where producers publish messages and consumers subscribe to read them.
+ Topics don’t store data directly; they are split into partitions.
+ `Partition` = Physical unit of storage
+ `replica` is a copy of a partition stored on a different broker.
+ Data is distributed across partitions
+ Producers write (publish) records into a topic.
+ Consumers read (subscribe) to records from a topic.
+ Unlike a queue, messages are not deleted once consumed — they are kept for a retention period.
+ Messages in a topic are stored on disk.
+ Retention can be time-based (7 days), size-based (1GB), or log-compaction-based (only keep the latest message per key).

```
Topic: user-events
 ├── Partition 0 → [msg1, msg2, msg3...]
 ├── Partition 1 → [msg4, msg5, msg6...]
 └── Partition 2 → [msg7, msg8, msg9...]
```


## diagram

### 1. Broker diagram

+ Broker is responsible for saving messages of producers
+ each connection is TCP (bidirectional)

```
┌─────────┐      ┌────────┐      ┌──────────┐
│ Producer│ ───▶ │ Broker │ ───▶ │ Consumer │
└─────────┘      └────────┘      └──────────┘
```

###  2. Multiple producers and consumers

+ We can have multiple consumer and multiple producer with one Broker 
+ if this broker fails nobody can read/write messages
+ so we need to have multiple brokers(cluster of Brokers)
+ producers and consumers do not know about each other
+ if you stop one consumer, it will not stop other consumers and producers and vise versa.
+ every consumer must be part of the consumer group and if we do not specify group name it will created with random id for each consumer seperately. ex: console-soncumer-1534 in logs
6. Producers decides which partition to choose to write message inside that.
7. consumers can connect to one or multiple topices but usually they connect to only one topice
8. consumers can are able to consume messages from multiple partitions
9. if multiple consumers connect the one topice they belong to one consumer group,In such case every single message will be consumed only by one of the consumers in the consumer group. ????????????????????


```
┌─────────┐      ┌────────┐      ┌──────────┐
│ Producer│ ───▶ │        │ ───▶ │ Consumer │
└─────────┘      │        │      └──────────┘
                 │ Broker │
┌─────────┐      │        │      ┌──────────┐
│ Producer│ ───▶ │        │ ───▶ │ Consumer │
└─────────┘      │        │      └──────────┘
                 └────────┘
```

### 3. Broker cluster

+ Kafka cluster is a group of Kafka brokers (servers) working together.
+ A cluster makes Kafka scalable, distributed, and resilient.
+ Data is replicated across brokers for durability and high availability.
+ Producers and consumers interact with the cluster as a whole, not individual brokers.

```
                    cluster of brokers
┌─────────┐    ┌─────────────────────────────┐    ┌──────────┐
│ Producer│───▶│ ┌────────┐  ┌────────┐      │───▶│ Consumer │
└─────────┘    │ │ Broker │  │ Broker │      │    └──────────┘
               │ └────────┘  └────────┘      │
┌─────────┐    │ ┌────────┐  ┌────────┐      │    ┌──────────┐
│ Producer│───▶│ │ Broker │  │ Broker │      │───▶│ Consumer │
└─────────┘    │ └────────┘  └────────┘      │    └──────────┘
               └─────────────────────────────┘
```

### 4. Zookeeper

+ How these Brokers can sync with each other?   ---> By zookeeper
+ Zookeeper tasks:
    1. Maintain list of active brokers
    2. Maintain configuration of topics and partitions
    3. elects controller

+ kafka cluster = kafka server(broker) + zookeeper

+ producers can wtire in different Brokers and consumers can read from different Brokers
+ if Brokers should be publicly accessible set `advertised listeners` in Broker config

+ When you create any topic in kafka cluster, it is created basicly in zookeeper.
+ zookeeper will create a new folder for that topic. 
+ zookeeper distribute its configuration to all brokers in the cluster

####### ports

+ Zookeeper localhost:2181
+ Kafka server (broker) localhost:9092


```
                    ┌─────────────┐
                    │  Zookeeper  │
                    └─────────────┘                         
                            ▲
                            |
                            ▼
┌─────────┐    ┌─────────────────────────────┐    ┌──────────┐
│ Producer│───▶│ ┌────────┐  ┌────────┐      │───▶│ Consumer │
└─────────┘    │ │ Broker │  │ Broker │      │    └──────────┘
               │ └────────┘  └────────┘      │
┌─────────┐    │ ┌────────┐  ┌────────┐      │    ┌──────────┐
│ Producer│───▶│ │ Broker │  │ Broker │      │───▶│ Consumer │
└─────────┘    │ └────────┘  └────────┘      │    └──────────┘
               └─────────────────────────────┘
```

### 5. Zookeeper cluster

+ `Ensemble`: we can also have a cluster of zookeepers that called *ensemble*
in every ensumble we should set so-called *quorum*
Quorum is the minimum number of servers that should be up and running. If there are less servers than quorum, zookeeper is considered down and all coresponding  brokers that were connected to the cluster will be also down 
so the number of servers in ensemble should be odd like 3 or 5 or 7 (and quorum for them 2 or 3 or 4 ==> (n+1)+1) 9server and 5quorum)


```
                        Ensemble
                   ┌───────────────────┐
                   │  ┌─────────────┐  │
                   │  │  Zookeeper  │  │
                   │  └─────────────┘  │                      
                   │  ┌─────────────┐  │
                   │  │  Zookeeper  │  │
                   │  └─────────────┘  │                      
                   │  ┌─────────────┐  │
                   │  │  Zookeeper  │  │
                   │  └─────────────┘  │
                   └───────────────────┘
                            ▲
                            |
                            ▼
┌─────────┐    ┌─────────────────────────────┐    ┌──────────┐
│ Producer│───▶│ ┌────────┐  ┌────────┐      │───▶│ Consumer │
└─────────┘    │ │ Broker │  │ Broker │      │    └──────────┘
               │ └────────┘  └────────┘      │
┌─────────┐    │ ┌────────┐  ┌────────┐      │    ┌──────────┐
│ Producer│───▶│ │ Broker │  │ Broker │      │───▶│ Consumer │
└─────────┘    │ └────────┘  └────────┘      │    └──────────┘
               └─────────────────────────────┘
```

### 6. Zookeeper cluster quorum

+ It is recommended to have odd number of servers in the Zookeeper ensemble like 1, 3, 5, 7 etc 
+ quorum set to (n+1)/2 where n is qty of servers

```
# bad example
                cluster 1                           cluster 2
    ┌──────────────────────────────────┐ ┌──────────────────────────────────┐
    │ +-------------+  +-------------+ │ │ +-------------+  +-------------+ │
    │ |  Zookeeper  |  |  Zookeeper  | │ │ |  Zookeeper  |  |  Zookeeper  | │
    │ +-------------+  +-------------+ │ │ +-------------+  +-------------+ │
    └──────────────────────────────────┘ └──────────────────────────────────┘
                            ▲                     ▲
                            |                     |
                            |                     |
                            |                     |
                            |                     |
                            |                     |
                            ▼                     ▼
        ┌─────────┐    ┌─────────────────────────────┐    ┌──────────┐
        │ Producer│───▶│ ┌────────┐  ┌────────┐      │───▶│ Consumer │
        └─────────┘    │ │ Broker │  │ Broker │      │    └──────────┘
                       │ └────────┘  └────────┘      │
        ┌─────────┐    │ ┌────────┐  ┌────────┐      │    ┌──────────┐
        │ Producer│───▶│ │ Broker │  │ Broker │      │───▶│ Consumer │
        └─────────┘    │ └────────┘  └────────┘      │    └──────────┘
                       └─────────────────────────────┘
```

### KRaft

+ `kraft` is a new metadata management mode in Kafka that removes ZooKeeper.
+ Kafka used ZooKeeper to manage cluster metadata (brokers, topics, partitions, configs)
+ now Kafka uses its own built-in Raft consensus protocol to manage metadata
+ all metadata is now inside Kafka itself
+ Kafka 3.3+ use kraft and in version 4 zookeeper is removed completely





### Message structure

Timestamp
Offset number (unique across partition)
Key (optional)
Value (sequence of bytes)

## Replication

+ We can have multiple partitions in one topic. 
+ And this partitions can be spread between multiple brokers.
+ `Replication`: Replication means making copies of partitions across multiple brokers 
+ we can copy a partition (messages of a topic) from one broker to another broker or from one prtition to another
+ Replication Factor defined per topic when creating it.
+ If we set a replication factor on the topic level it will create a copy of every message in every partition for that topic.
+ with replication if one broker fails we can use another.
+ 
+ `Leader`: The origin partition called Leader
+ `Follower`: destination partitions called follower 
+ So Leader writes its content inside followers. 
+ Consumers read just from leader and not from followers. 
+ Each partition has:
    - 1 leader replica              → handles all reads/writes.
    - 0 or more follower replicas   → stay in sync by copying data from the leader.
+ if Leader fail, one of the follower become leader. Followers are just relax and wait for new messages from Leader. 
+ Kafka guarantees that every partition replica resides on a different broker. so the maximum replication factor is the number of brokers in the cluster.
+ It recommended to have at least two Replica. 
    + If you set `--replication-factor = 3` you can have 3 replica for each partition. 
    + and if you set `--partitions = 5` you will have 3 replica partition in each of 5 partition and in total 15 partitions.

+ `Controller`: One of the brokers that is responsible for managing the state of patitions and replicas called *controlelr*. 
+ Controller electes automatically be zookeeper. 
+ if the controller fail then new controller elected by zookeeper.
+ controller:
    + create different partitions
    + assign them to diffenre brokers
    + reassign in sace of faluire broker
    + if there was a replication configuration on the topic level controller selecet leader for specific partition and select forwards.
    + If leader fail controller decides next leader.
    + If one broker fail, controller will reassaing the partition of fail broker to a remain broker.
    + controller elect leader partition.

### offset

+ The consumer offset is a way of tracking the sequential order in which messages are received by Kafka topics.
+ every topic has a unique name and every message inside of a topic has a unique number called `offset`.
+ These numbers is immutable.
+ every new messages append to the end offset list. 
+ offset starts from 0 and increases by 1 for every new message.
+ first message in each topic has offset 0 and consumers start reading messages starting form specific offset.
+ When consumer connects to kafka cluster you are able to specify a specific offset number you want to start reading message from.[ --from-beginning ==> number zero]


### folder contents

```bash
kafka_4.1
├── bin/                    # Shell scripts for managing Kafka (Linux/macOS)
├── bin/windows/           # Batch scripts for managing Kafka (Windows)
├── config/                # Configuration files for all components
│     ├────── server.properties                # 
│     ├────── log4j.properties                      # Configures logging output
│     ├────── producer.properties                # 
│     ├────── consumer.properties                # 
│     └────── zookeeper.properties                # 
├── libs/                  # All Java JAR libraries (Kafka & dependencies)
├── logs/                  # Log files (created at runtime)
├── site-docs/             # Documentation
├── LICENSE
└── NOTICE
```

##### 1. Logs (folder content)

```bash
# when you start kafka server, it will create a log folder in /kafka/logs across bin/ and config/
# see the exact location for these logs in config/server.properties and config/log4j.properties
cat server.log          # Kafka server log [same as your terminal]
cat /tmp/zookeeper                                             # Zookeeper logs has diff path
cat /tmp/kafka-logs                                             # logs for producers and consumer [new topic creates a new folder]
cat 0000000000000000000000.log                                # logs for messages of prodeucers
```

##### 2. Log Segments

+ Once a segment reaches a certain size (e.g., 1 GB) or age, it is "closed," and a new active segment is created.
    + 00000000000000000000.log (Segment 1, offsets 0 - 500,000)
    + 00000000000005000001.log (Segment 2, offsets 500,001 - 1,000,000)
    + 00000000000010000001.log (Segment 3, the current active segment)


```text
kafka-logs/ (or your custom log.dirs path)
│
├── my-topic-0/ (Folder for Partition 0 of 'my-topic')
│   ├── 00000000000000000000.log (The actual message data)
│   ├── 00000000000000000000.index (Index for fast lookup by offset)
│   ├── 00000000000000000000.timeindex (Index for lookup by timestamp)
│   └── ... (New segments are created as the log grows)
│
├── my-topic-1/ (Folder for Partition 1 of 'my-topic')
│   ├── 00000000000000000000.log
│   ├── 00000000000000000000.index
│   └── ...
│
├── __consumer_offsets-0/ (Kafka's internal topic for storing consumer offsets)
│   ├── ...
│
└── ... (Folders for all other topic-partitions)
```

##### 3. config (folder content)

+ all of these files contains comments by default

```bash
vim ./config/server.properties                    # edit this file for broker configuration
vim ./config/producer.properties                  # producer config
vim ./config/consumer.properties                  # consumet config
vim ./config/zookeeper.properties                  # zookeeper config
vim ./config/log4j.properties                  # logging config
```

##### 2.1. Broker config (server config)

```bash
broker.id=1                         # The unique ID for each broker in a cluster. Must be unique for each server.
listeners=PLAINTEXT://:9092               # The port the socket server listens on.
log.dirs=/tmp/kafka-logs             # Comma-separated list of directories under which to store log files (the actual message data).
# log/ is for system logs but kafka-logs/ is for message logs
num.partitions=3                        # default number of log partitions per topic.
num.network.threads=3                  # number of threads the broker uses for handling network requests.
num.io.threads=8                           # number of threads the broker uses for processing requests, which may include disk I/O.
log.flush.interval.messages=10000            # number of messages to accumulate before forcing a flush to disk.
log.flush.interval.ms=1000            # max time (ms) to wait before forcing a flush to disk, even if `log.flush.interval.messages` hasn't been met.

# ==================== REPLICATION & DURABILITY ====================
offsets.topic.replication.factor=1          # number of replicas for the internal __consumer_offsets topic.
min.insync.replicas=2          # min number of replicas that must be alive for a partition to be available for write operations

# ==================== LOG RETENTION & CLEANUP ====================
log.retention.hours=168             # The retention period for log segments         # The default is 7 days. 
log.retention.hours= -1           # Set to -1 to never delete logs (useful for event sourcing).
log.retention.bytes=1073741824    # 1 GB       # The maximum size a partition's log can grow to before we discard old segments.
log.cleanup.policy=delete           # The policy for deleting old log segments when retention limits are hit.
                                    #   delete: (default) delete segments based on time or size.
                                    #   compact: cleanup to retain only the latest value for each key. Crucial for Kafka as a database.
```

#### 2. producer config

```bash
bootstrap.servers=localhost:9092,broker2:9092,broker3:9092               # List of Kafka brokers to connect to for bootstrapping.

# ==================== RELIABILITY & DURABILITY (Acks) ====================
# The number of acknowledgments the producer requires the leader to have received before considering a request complete.
acks=0                      # Fire-and-forget. Highest throughput, no guarantee. Data loss is possible.
acks=1                         # Wait for the leader to acknowledge. Default. 
acks=all                       #  Wait for the full set of in-sync replicas (ISR) to acknowledge. strongest guarantee. No data loss.
retries=5                        # How many times to retry a failed send request.
enable.idempotence=true          # The producer will ensure that exactly one copy of each message is written in the stream.


# ==================== PERFORMANCE & BATCHING ====================

# The producer will attempt to batch records together into fewer requests.
linger.ms=5                       # Time to wait for more records before sending batch
buffer.memory=33554432 # 32 MB       # The total bytes of memory the producer can use to buffer records waiting to be sent to the server.
max.request.size=1048576 # 1 MB         # The maximum size of a request in bytes. 
compression.type=lz4     # The compression type for all data generated by the producer.       #   none(default), gzip, snappy, lz4, zstd
max.in.flight.requests.per.connection=1    # Controls how many unacknowledged requests the producer can send on a single connection before blocking.
request.timeout.ms=30000 # 30 seconds       # The maximum time the client will wait for the response of a request.
retry.backoff.ms=100                     # Time to wait before retrying a failed request. Avoids rapid retries in a tight loop.
```


#### 2. consumer config

```bash
# ==================== ESSENTIAL CONNECTIVITY & DESERIALIZATION ====================

bootstrap.servers=localhost:9092,broker2:9092,broker3:9092
key.deserializer=org.apache.kafka.common.serialization.StringDeserializer       # Converts bytes back to an object (deserializer)
value.deserializer=org.apache.kafka.common.serialization.StringDeserializer          # Converts bytes back to an object. (deserializer)
group.id=my-application-consumer-group           # A unique string that identifies the consumer group this consumer belongs to.

# ==================== OFFSET MANAGEMENT ====================

# What to do when there is no initial offset in Kafka 
auto.offset.reset=earliest           #   earliest: Automatically reset to the earliest available.
auto.offset.reset=latest           #   latest: (default)   Automatically reset the offset to the latest 
auto.offset.reset=none             #   none:     Throw an exception to the consumer if no previous offset is found.

enable.auto.commit=true              # If true, the consumer's offset will be periodically committed in the background.
auto.commit.interval.ms=5000        # Time that the consumer offsets are auto-committed to Kafka.


# ==================== PERFORMANCE & POLLING ====================

max.poll.records=500           # The maximum number of records returned in a single call to poll().
max.poll.interval.ms=300000        # The maximum delay between invocations of poll() in milliseconds.
fetch.min.bytes=1               # The minimum amount of data the server should return for a fetch request.
fetch.max.bytes=52428800 # 50 MB      # The maximum amount of data the server should return for a fetch request.
fetch.max.wait.ms=500   # The maximum time the server will block before answering if there isn't sufficient data to satisfy `fetch.min.bytes`.
```

#### 3. zookeeper config

```bash
tickTime=2000           # The number of milliseconds of each tick.         # Default: 2000 (2 seconds)
dataDir=/var/lib/zookeeper/data           # The directory where ZooKeeper will store its in-memory database snapshots.
clientPort=2181          # port
maxClientCnxns=60        # The maximum number of client connections.          # Default: 60
initLimit=10          # (10 * 2000ms = 20 seconds)         # The number of ticks that can pass between sending a request and getting an ack
syncLimit=5                   # (5 * 2000ms = 10 seconds)    # The number of ticks that can pass between syncing with the leader.
preAllocSize=65536             # 64 MB         # Pre-allocated snapshot file size (in MB). Helps prevent fragmentation.
autopurge.snapRetainCount=3           # The number of snapshots to retain in the dataDir.      # Olders will deleted automatically.
autopurge.purgeInterval=1               # Purge task interval in hours. Set to a positive integer (e.g., 1) to enable auto-purge.

# Example for a 3-node cluster:               # The servers that make up the ZooKeeper cluster.
server.1=zk1.example.com:2888:3888                # Format: server.<myid>=<hostname>:<peer_port>:<leader_election_port>
server.2=zk2.example.com:2888:3888
server.3=zk3.example.com:2888:3888
```

## Commands

+ in docker you do not need to run any commands, like running a server or produce a message

#### 0. Broker

+ all commands have `--bootstrap-server localhost:9092` which tells the CLI which Kafka broker(s) to initially connect to.
+ Even though we only specify one broker in CLI , Kafka will discover broker-2 and broker-3 automatically from our config files.
+ we must specify this attribute for all commands, but in this file I do not write for my commands

##### 1. Kafka Server (CLI)

+ for running kafka you need to run:
    1. zookeeper        ----->     zookeeper-server-start
    2. kafka server     ----->     kafka-server-start
+ `BrokerId`: every kafka server has an id (you can check id id in logs) (related to broker.id=0 in config/server.properties)

```bash
zookeeper-server-start.sh config/zookeeper.properties  # start Zookeeper in port 2181
kafka-server-start.sh config/server.properties         # start Kafka server(broker) in port 9092 
kafka-server-stop.sh                                      # stop Kafka server
```

##### 2. info (CLI)

```bash
zookeeper-shell.sh localhost:2181 ls /brokers/ids        # list all active brokers
zookeeper-shell.sh localhost:2181 ls /brokers/ids/0       # information about specific broker
kafka-broker-api-versions.bat --bootstrap-server localhost:9092          # List of all brokers with connection strings & IDs
```

##### 1. Topics (CLI)

```bash
# add --bootstrap-server localhost:9092 to all commands
kafka-topice.sh                                            # not run just show arguments (help)
kafka-topics.sh --list                                         # List topics
kafka-topice.sh --list --zookeeper localhost:2181           # list all available topics

kafka-topice.sh --create --topic test-topic                                       # create a new topic with default arguements
kafka-topice.sh --create --bootstrap-server localhost:9092,localhost:9093 --topic name_of_topice  # create multiple topics
kafka-topics.sh --create --topic test-topic --partitions 3 --replication-factor 2    # Create a topic with custom config 
# create with three partition in /tmp/kafka-logs  # [if you have multiple broker, partitions of a topic will spread among diffent brokers]
kafka-topics.sh --create --topic test-topic \                      # Create with custom configs 
                --partitions 1 \
                --replication-factor 1 \   
                --config cleanup.policy=compact \                         # Keep last value per key
                --config retention.ms=604800000  \                      # 7 days retention (default)

kafka-topice.sh --describe --zookeeper localhost:2181 --topic test-topic  # describe configuration of topic
kafka-topics.sh --describe --topic test-topic             # Describe a specific topic
kafka-topics.sh --describe                              # Describe all topics

kafka-topics.sh --delete --topic test-topic         # Delete a topic
kafka-topics.sh --alter --topic my-topic --partitions 5    # alter properties of topic like increasing number of partitions
kafka-topics.sh --alter --topic my-topic --config retention.ms=172800000         # alter config to a topic 

# describe result                            ??????????
# 1.pratitionCount: number of folders per topic, 
# 2.ReplicationFactor: the number of servers where data will store 4.max size of segment 4. details for every partition[ number of folders +  id of broker by default is zero, leader: id of broker , replica: id broker, ISR: in sink replica(id of broker)]
# ReplicationFactor: if we have three servers every message will ne stores in every of three servers, so if one server can not be available the others can
```

##### 2. Producer (CLI)
```bash
# add --bootstrap-server localhost:9092 to all commands
kafka-console-producer.sh --topic test-topic                                  # Start a console producer
kafka-console-producer.sh  --topic my-topic --property parse.key=true --property key.separator=$'\t' # Produce with a key (format: key<tab>value)
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic name_of_topice  # connect to broker for writting messages
kafka-run-class.sh kafka.tools.GetOffsetShell --topic test-topic --time -1         # how many messages in a topic?
```




##### 3. Consumer (CLI)


```bash
# add --bootstrap-server localhost:9092 to all commands
kafka-console-consumer.sh --topic test-topic                                # Start a console consumer online (read new messages only)
kafka-console-consumer.sh --topic test-topic --from-beginning                # Start a console consumer (read from beginning of log)
kafka-console-consumer.sh  --topic my-topic --group my-consumer-group                 # Read from a specific consumer group
kafka-console-consumer.sh  --topic my-topic --partition 0                       # Read from a specific partition
kafka-console-consumer.sh   --topic my-topic \
                            --property print.timestamp=true \                 # print timestamp
                            --property print.key=true \                        # print key
                            --property print.value=true                       # print value


bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic name_of_topice  # connect to broker for reading just new messages
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092,localhost:9093 --topic name_of_topice  # create one consumer for each broker
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic name_of_topice --from-beginning  # connect to broker for reading messages from beginning (all messages from offset zero) [if you run another consumer, the new consumer can read message too]
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic Name --from-beginning --partition 1 # read messages just from partition 1
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic Name --partition 1 --offset 2 # read messages from offset 2, we can not use --from-beginning with this option [--partition is required when offset specifid]
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic name_of_topice --group GROUP --from-beginning # connect to broker with consumer group
```

##### 4. Consumer Group Management

```bash
kafka-consumer-groups.sh --list                                 # List all consumer groups
kafka-consumer-groups.sh --describe --group test-group          # Describe a consumer group (shows offsets & lag)
# describe result:
# current-offset: how many message store(commit) in that partition,
# log-enf-offset: how many messages read successfully by consumer
# lag is difference between current and end (producer are faster than consumers)

kafka-consumer-groups.sh \                                     # Reset offsets to earliest
  --reset-offsets \
  --to-earliest \
  --group test-group \
  --topic test-topic \
  --bootstrap-server localhost:9092 --execute

kafka-consumer-groups.sh \                                       # Reset offsets to latest
  --reset-offsets \
  --to-latest \
  --group test-group \
  --topic test-topic \
  --bootstrap-server localhost:9092 --execute

```


##### test perfomance

```bash
bin/kafka-producer-perf-test.sh --topic name_of_topice --num-records 1000 --record-size 100 --throughput 1000 --producer-props bootstrap.servers=localhost:9092 # perfomance test [num-records: num of message , throughput: num of message per second, record-size: size of every message in byte]
bin/kafka-consumer-perf-test.sh --broker-list localhost:9092 --topic NAME --messages 10000   # read 1000 messages by consumer
```



#### 6. log management

1. kafka does not store messages forever and after specific time/size will delete messages called *log retention period*[ default: 7days, 10MB ]
2. segment is the palce that store log messages `/tmp/kafka-logs`. The default size is 1GB, you can check by `consumer --describe` and if the size get full the segment create a new log file inside that folder.


```bash
rm -rf /tmp/zookeeper & rm-rf /tmp/kafka-logs & rm -rf yourpath/kafka/logs   # delete logs in kafka
```

## Internal Topics

1. __consumer_offsets
2. __transaction_state

+ `__conumer_offsets` is the system topic you can check it with `consumer --list`. 
+ it has 50 partitions. 
+ It automatically created after first connection of consumer to cluster.
+  if consumers > partitions in a topic, some consumers will not recive message at all. They called `IDEL`. Because every consumer connect to just specific partition, you can check `consumer --gropu --describe`. If one consumer will be fail one of the idles come in to picture.






### Docker with zookeeper



```yaml
# whereis kafka        /etc/kafka                # you can config files in this location
version: '3'
services:
# 1
  kafka:                            # if you do not run zookeeper this container will stop after a while
    image: ubuntu/kafka
    restart: always
    environment:
      - TZ=UTC
      - ZOOKEEPER_HOST=zookeeper
      - ZOOKEEPER_PORT=2181
    depends_on:
      zookeeper:
        condition: service_started
    networks:
      - netkaf

# 2                                 # zookeeper image is required for ubuntu/kafka image
  zookeeper:
    image: ubuntu/zookeeper  
    ports:
      - "2181:2181"  
    networks:
      - netkaf
```

#### Docker with Kraft








