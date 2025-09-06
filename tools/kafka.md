

# Apachie kafka

+ kafka is a *distributed* *publish-subscribe* *messaging system*
+ Apache Kafka is an event streaming platform used to handle real-time data feeds.

## Main Use Cases

+ Real-time analytics (e.g., monitoring user behavior).
+ Log aggregation (collecting logs from multiple services).
+ Event-driven architectures (microservices communication).
+ Data pipelines (streaming from databases, IoT devices, etc.).
+ Messaging system (like RabbitMQ, but optimized for throughput and persistence).


## Kafka vs RabbitMQ

+ Kafka stores messages for a configurable retention time (not deleted after consumption).
+ Consumers can re-read messages (offset-based).
+ Designed for high throughput (millions of messages/sec).
+ Supports both real-time and batch processing.


## core concepts

1. Topics:          Stream of records (like a table in a database).
2. Partitions:      Topics are split into partitions for parallelism.
3. Brokers:         Kafka servers that store data and serve clients.
4. Producers:       Applications that write data to Kafka topics.
5. Consumers:       Applications that read data from Kafka topics.
6. Consumer Groups: Groups of consumers that share workload.
7. Zookeeper:       Manages brokers, leader election, and metadata (deprecated in newer Kafka versions).
8. Replication:     Copies of partitions for fault tolerance.
9. Offset:          A unique ID for each record within a partition.





### topic and partition

+ `topic` is a named stream of messages in Kafka.
+ Topic is like a category where producers publish messages and consumers subscribe to read them.
+ Topics don’t store data directly; they are split into partitions.
+ `Partition` = Physical unit of storage
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

```
┌─────────┐      ┌────────┐      ┌──────────┐
│ Producer│ ───▶ │ Broker │ ───▶ │ Consumer │
└─────────┘      └────────┘      └──────────┘
```

###  2. Multiple producers and consumers

+ We can have multiple consumer and multiple producer with one Broker 
+ if this broker fails nobody can read/write messages
+ so we need to have multiple brokers(cluster of Brokers)

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

+ kafka cluster = kafka server(broker) + zookeeper

producers can wtire in different Brokers and consumers can read from different Brokers
if Brokers should be publicly accessible set *advertised listeners* in Broker config
How these Brokers can sync with each other? By zookeeper
zookeeper: 
1. Maintain list of active brokers
2. Maintain configuration of topics and partitions
3. elects controller


When you create any topic in kafka cluster, it is created basicly in zookeeper. and zookeeper will create a new folder for that topic. and zookeeper distribute its configuration to all brokers in the cluster ==> so zookeeper is needed for kafka

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

we can also have a cluster of zookeepers that called *ensemble*
in every ensumble we should set so-called *quorum*
Quorum is the minimum number of servers that should be up and running. If there are less servers than quorum, zookeeper is considered down and all coresponding  brokers that were connected to the cluster will be also down 
so the number of servers in ensemble should be odd like 3 or 5 or 7 (and quorum for them 2 or 3 or 4 ==> (n+1)+1) 9server and 5quorum)


```
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

## concetps

+ We can have multiple partitions in one topic. 
+ And this partitions can be spread between multiple brokers.
+ If we set a replication factor on the topic level you can create replicas of every message in every partition for that topic.
+ `Replication`: If we copy a partition (messages of a topic) from one broker to another broker (or from one prtition to another),
it is called *replication*. So if that broker fail we can use another.
+ `Leader`: The origin partition called *Leader* or **Leader prtition* or *Broker of leader partition* and destination partitions called *follower*. 
+ So Leader writes its content(replicates) inside followers. 
+ Consumers read just from leader and not from followers. 
+ if Leader fail, one of the follower become leader. Followers are just relax and wait for new messages from Leader. 
+ Kafka guarantees that every partition replica resides on a different broker.so the maximum replication factor is the number of brokers in the cluster.
It recommended to have at least two Replica. If you set `--replication-factor = 3` you can have 3 replica for each partition. so if you set `--partitions = 5` you can have 3 replica partition in each of 5 partition and in total 15 partitions.
One of the brokers that is responsible for managing the state of patitions and replicas called *controlelr*. Controller electes
automatically be zookeeper. and if the controller fail then new controller elected by zookeeper.
controller create different partitions, assign them to diffenre brokers, reassign in sace of faluire broker, and if there was a replication 
configuration on the topic level controller selecet leader for specific partition and select forwards. If leader fail controller decides next leader.
If one broker fail, controller will reassaing the partition of fail broker to a remain broker. controller elect leader partition.


The consumer offset is a way of tracking the sequential order in which messages are received by Kafka topics.

### folder contents
```bash
######## Config/
config/server.properties                                    # Kafka server config
config/zookeeper.properties                                 # Zookeeper config

######## Bin/

######## Logs/
# when you start kafka server, it will create a log folder in /kafka/logs across bin/ and config/
```

## Logs
```bash
# when you start kafka server, it will create a log folder in /kafka/logs across bin/ and config/
server.log                                                  # Kafka server log [samae as your terminal]


### /tmp
/tmp/zookeeper                                             # Zookeeper logs has diff path
/tmp/kafka-logs                                             # logs for producers and consumer [new topic creates a new folder]
0000000000000000000000.log                                # logs for messages of prodeucers
```

## Run kafka
```bash
bin/zookeeper-server-start.sh config/zookeeper.properties  # start Zookeeper in port 2181
bin/kafka-server-start.sh config/server.properties         # start Kafka server(broker) in port 9092 [every borker on the same computer must have: 1.unique port in listerners 2.unique broker.id 3.unique log.dir in the server.properties]
bin/zookeeper-shell.sh localhost:2081 ls /brokers/ids        # list all active brokers
bin/zookeeper-shell.sh localhost:2081 ls /brokers/ids/0       # information about specific broker
bin/kafka-topice.sh --create --bootstrap-server localhost:9092 replication-factor 1 partitions 3 --topic name_of_topice  # create a new topic with three partition in /tmp/kafka-logs [if you have multiple broker, partitions of a topic will spread among diffent brokers]
bin/kafka-topice.sh                                            # not run just show arguments
bin/kafka-topice.sh --create --bootstrap-server localhost:9092 --topic name_of_topice  # create a new topic with default arguements
bin/kafka-topice.sh --create --bootstrap-server localhost:9092,localhost:9093 --topic name_of_topice  # create multiple topics
bin/kafka-topice.sh --list --bootstrap-server localhost:9092      # list all available topics
bin/kafka-topice.sh --list --zookeeper localhost:2181           # list all available topics
bin/kafka-topice.sh --describe --bootstrap-server localhost:2181 --topic name_of_topice          # describe a topic
bin/kafka-topice.sh --describe --zookeeper localhost:2181 --topic name_of_topice  # describe configuration of topic, 1.pratitionCount: number of folders per topic, 2.ReplicationFactor: the number of servers where data will store 4.max size of segment 4. details for every partition[ number of folders +  id of broker by default is zero, leader: id of broker , replica: id broker, ISR: in sink replica(id of broker)]
# ReplicationFactor: if we have three servers every message will ne stores in every of three servers, so if one server can not be available the others can
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic name_of_topice  # connect to broker for writting messages
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic name_of_topice  # connect to broker for reading just new messages
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092,localhost:9093 --topic name_of_topice  # create one consumer for each broker
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic name_of_topice --from-beginning  # connect to broker for reading messages from beginning (all messages from offset zero) [if you run another consumer, the new consumer can read message too]
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic Name --from-beginning --partition 1 # read messages just from partition 1
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic Name --partition 1 --offset 2 # read messages from offset 2, we can not use --from-beginning with this option [--partition is required when offset specifid]
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic name_of_topice --group GROUP --from-beginning # connect to broker with consumer group
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --list  # list consumer groups 
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group GROUP # describe consumer group of each partitions [current-offset: how many message store(commit) in that partition,log-enf-offset: how many messages read successfully by consumer, lag is difference between current and end(producer are faster than consumers)]
bin/kafka-server-stop.sh                                      # stop Kafka server
rm -rf /tmp/zookeeper & rm-rf /tmp/kafka-logs & rm -rf yourpath/kafka/logs   # delete logs in kafka
bin/kafka-producer-perf-test.sh --topic name_of_topice --num-records 1000 --record-size 100 --throughput 1000 --producer-props bootstrap.servers=localhost:9092 # perfomance test [num-records: num of message , throughput: num of message per second, record-size: size of every message in byte]
bin/kafka-consumer-perf-test.sh --broker-list localhost:9092 --topic NAME --messages 10000   # read 1000 messages by consumer

```
## Facts
1. We can run multiple producers and consumers at the same time with same arguments at termial. but producers and consumers do not know about each other
2. if you stop one consumer, it will not stop other consumers and producers and vise versa.[they do just a  snigle job]
3. kafka does not store messages forever and after specific time/size will delete messages called *log retention period*[ default: 7days, 10MB ]
4. every consumer must be part of the consumer group and if we do not specify group name it will created with random id for each consumer seperately. ex: console-soncumer-1534 in logs
5. every topic has a unique name and every message inside of a topic has a unique number called offset. These numbers is immutable. And every new messages append to the end offset list. first message in each topic has offset 0 and consumers start reading messages starting form specific offset.When consumer connects to kafka cluster you are able to specify a specific offset number you want to start reading message from.[ --from-beginning ==> number zero]
6. Producers decides which partition to choose to write message inside that.
7. consumers can connect to one or multiple topices but usually they connect to only one topice
8. consumers can are able to consume messages from multiple partitions
9. if multiple consumers connect the one topice they belong to one consumer group,In such case every single message will be consumed only by one of the consumers in the consumer group. ????????????????????
10. segment is the palce that store log messages `/tmp/kafka-logs`. The default size is 1GB, you can check by `consumer --describe` and if the size get full the segment create a new log file inside that folder.
11. `__conumer_offsets` is the system topoic you can check it with `consumer --list`. it has 50 partitions. It automatically created after first connection of consumer to cluster.
12. if consumers > partitions in a topic, some consumers will not recive message at all. They called `IDEL`. Because every consumer connect to just specific partition, you can check `consumer --gropu --describe`. If one consumer will be fail one of the idles come in to picture.
















