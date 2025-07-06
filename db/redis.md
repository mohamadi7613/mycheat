
# Redis

## Redis (REmote DIctionary Server)
+ Redis  is a fast, open-source, in-memory data store
+ `in-memory` means Redis stores data in RAM for fast access
+ Redis used as: 1. database, 2. cache 3. message broker 4. session store
+ Redis known for extremely `low latency` (minimal delay)
+ Redis supports `persistence` to ensure your data survives restarts


## Pros and Cons
> Pros
    1. RAM is faster than Disk
    2. Redis can respond millions of queries per second

> Cons
    1. Max size of data is limited to the available RAM
    2. Complete data loss if Redis fails or restarts




### Start and information

```bash
sudo apt install redis           # 
sudo systemctl start redis       # start redis-server
redis-server                    # Start redis-server
vim /etc/redis-stable/redis.conf          # config file
tail -10 /home/.rediscli_history           # like linux history
redis-cli                    # connect me to redis  on port 6379
redis-cli -h HOST -p 6370            # connect to diff host and port
ping                              # Test connection
info                            # server info
redis-cli shutdown             # sudo systemctl stop redis   ---> stop redis
redis-cli -h 192.168.0.1 -p 6370 shutdown         # stop redis in remote
exit                           # exit from redis-cli
save                           # save a snapshot into RDB file
clear                          # clear the screan
```

### Reids-benchmark

```bash
redis-benchmark -q -n 1000                 # -n for number of requests
redis-benchmark -q -n 1000 -t get,set                #  -t or --tests specifies the list of commands for testing
redis-benchmark -q -n 1000 -c 5                #  -c or --clients specifies the number of parallel connections
```

### Authentication

```bash
auth 1234                         # set a password
```

###  KEYS (General)

```bash
set color red                #  set value [this will replace the previous value]
get color                      # give me the value
exists color                    # check if exists
set color:1 red                 # set value [this will not replace previous value]
set color:1 ex 5                     # ex means expire in 5 seconds (delete it)
set color:1 px 5                     # px means expire in 5 mili seconds
expire color:1 5                        # Set 5 second for expiration
persist color:1                          # persist means " do not exppire"
get color:1                      # get value
del color:1                      # delete a key-value
ttl color:1                       # print ttl or time_to_live
mset name ali age 12             # set multiple items
mget name age                      # get multiple items  (faster than using get multiple times)
rename name first_name              # rename key
type name                           # type of key
keys *                       # list all keys
dbsize                         # number of records which are available 
flushall                       # clear everything in very short time
```
### Data types

#### 1. String, Integer

```bash
set name "mohammad"                 # there is no need for qoutation
append name "iiii"
set mynumber 1                    # 1 is integer
incr mynumber                     # increament mynumber and print 2
incr age        # age += 1     # define if not exists
decr age                    # define if not exists (set -1)
incrby counter 10          # define if not exists
strlen name                  # length of string
```

#### 2. List

```bash
lpush mylist A B C D       # Push left   # no need for define a key previously    # D is the most left item (first item at position 0)
rpush mylist BB              # Push right to the existing list
get mylist                    # error: use 'lrange' for lists
lrange mylist 0 -1          # List Range gets items of list   # first element is at position 0         # -1 is the last element, -2, -3 ,...
lindex mylist 1             # List index for getting item at index
llen mylist                  # list length 
lpop mylist                 # left pop an item and return it
rpop mylist                 # right pop an item and return it
ltrim mylist 0 2              # just keep these items and delete others
lset mylist 1 "Z"            # Set index 1 to "Z" , previous value will be delete
linsert mylist BEFORE "B" "Q"  # Insert "Q" before "B"
linsert mylist AFTER "B" "R"   # Insert "R" after "B"
lrem mylist 2 "A"            # Remove 2 occurrences of "A" from head to tail
lrem mylist -1 "B"           # Remove 1 occurrence of "B" from tail to head
```

#### 3. Hash

```bash
hset user name "ali" age 20               # set is like dictionary or json
hget user name                                # get value of one item
hmget user name age                           # get multiple values
hgetall user                           # get all fields and values
hkeys user                             # get all field names
hvals user                             # get all field values
hexists user name                      # check if field exists
hdel user name age                     # delete multiple fields
hlen user                              # count number of fields
hincrby user age 1                     # increment field 'age' by 1
hincrby user age -1                    # decrement field 'age' by 1
hincrbyfloat user balance 10.5         # increment float field
```


#### 4. Set

```bash
# a set is an unordered collection of unique strings
sadd users_ip 1 2 3                                # set_add adds one or multiple elements
sadd users_ip 0 1                                 # just 0 will add since 1 is repetitive
scard users_ip                                 #  set_cardinality for length
smembers users_ip                              # print members
sismember users_ip 3                            # check membership of an element
spop users_ip                                # randomly remove one of elements
spop users_ip 2                                # randomly remove 2 of elements
srem users_ip "2"                                  # remove item which its value is 2 (not randomly)
srandmember users_ip                   # return one random member (does NOT remove it)
srandmember users_ip 3                 # return 3 random members
# Set operatiosn
sadd set1 2 3 4                                # add a new set
sadd set2 1 2 3                                # add a new set
sdiff set1 set2                          # prints 4          # unique in second set (set1 - set2)
sunion set1 set2                    # all unique members from both sets like OR
sinter set1 set2                    # intersection for members present in both sets like AND
smove users_ip new_set 1                       # move item 1 into new set
```


#### 5. sorted set (zset)

```bash
# a sorted set is a collection of unique strings with an automatically associated score (float)  [great for ranking]
zadd scores 100 "alice"                    # add member 'alice' with score 100   
zadd scores 200 "bob" 150 "carol"          # add multiple members   # like json with numbers
zincrby scores 10 "alice"                  # increase score of 'alice' by 10
zincrby scores -5 "bob"                    # decrease score
zrange scores 0 -1                         # get all members (low to high)
zrevrange scores 0 -1                      # get all members (high to low)
zrange scores 0 2 withscores               # get top 3 with scores
zrevrange scores 0 2 withscores            # get top 3 (high to low)
zscore scores "alice"                     # get score of a member
zrank scores "alice"                      # get rank (low to high)
zrevrank scores "alice"                   # get rank (high to low)
zrangebyscore scores 100 200              # members with score between 100–200
zrevrangebyscore scores 200 100           # same, but high to low
zcount scores 100 150                     # count members in score range
```


### Transaction

+ A transaction in Redis allows you to execute multiple commands in sequence and atomically.
+ `Atomic Transaction` means execute all or nothing
+ Redis transactions do not roll back on individual command failure (e.g., a wrong type). All commands still run.

```bash
multi                        # start a transaction
set key1 "value1"             # write multiple operations
set key2 "value2"
incr counter
exec                        # execute means runs all the queued commands in sequence
discard                     #  cancel the transaction
```


#### Optimistic Lock 

```bash
watch balance          # watch is used to monitor one or more keys before starting a transaction
get balance                # check balance value
multi                      # suppose someone cahnge the value: "set balance 2"
# unwatch balance          # we can unwatch
decr balance         # If balance changes between watch and exec, the transaction will be aborted, 
exec                  # if balance change exec returns null (nil)
```

### Mass Insertation

```bash
awk -F ',' '{print "set " $1 " " $2}' file.csv | redis-cli            # -F for filed-delimiter which is comma in csv file
awk -F ',' '{print "set " $1 " " $2}' file.csv | redis-cli --pipe     # --pipe means execute all commands once and just give me one output
```


### persistence

+ By default if you restart redis server all data will be lost.
+ `Redis persistence` is the mechanism that allows Redis to save data to disk, so it can recover that data after a restart or crash.
+ Redis keeps all data in RAM and without persistence, all data would be lost when the server shuts down
+ There are two main persistence mechanisms:  1. RDB 2. AOF

##### 1. RDB (Redis Database File)
+ Takes a binary snapshot of the dataset to a .rdb file on disk at regular intervals. 
+ Fast to restore, uses less disk space.
+ Data between snapshots will be lost if Redis crashes.


```bash
# vim ~/redis-stable/redis.conf
dbfilename dump.rdb          # RDB file name
dir ./                         # the place of RDB file is relative --> currenat location (the directory where you start redis server)
dir ./folder_name_for_backup                         # the place of RDB file is static for backup
# if you specify 'relative directory' for restoring date you should start the server from the location of dump.rdb
# and if you specify 'static directory' where ever you start the server tha backup data will restore automatically
save 900 1                   # save snapshot every 900 seconds when one key has been changed
save 300 10                         # After 300 sec if 10 keys changed
```


##### 2. AOF (Append-Only-File)
+ Logs every write operation to disk.(like set, incr)
+ Redis write these commands in a log file
+ AOF is more durable than RDB but usually slower since file size grows over time


```bash
appendonly yes                                    #  Enable AOF persistence  # by default it is no, make it yes if you want to use AOF method
appendfilename "appendonly.aof"                         # name of the AOF file
dir ./                                                  # location of AOF file
appendfsync everysec                            # 	fsync every second (good balance)
appendfsync always                            # 	fsync after every write (slow, safe)
appendfsync no                                 # Let OS handle fsync (fast, risky)
```

##### 3. Hybrid (RDB + AOF)

+ At restart, Redis will prefer to load AOF, since it's more up-to-date.
+ RDB for `backup` and AOF for `real-time recovery`.

```bash
appendonly yes              # AOF
save 900 1                 # RDB
# comment both if you don't want to use persistence at all
```



### Redis distribution


#### 1. Replication

+ `Replication` in Redis means copying data from one Redis server (`primary` or `master`) to one or more other servers (called `replicas`).
+ Primary accepts both reads and writes, but replicas are read-only.
+ All the servers (primary and replicas) are synced with each other.
+ If the primary server falls, we still can connect to the replicas for read-only operations.
+ This setup allows you to distribute data, scale read operations, and improve fault tolerance.
+ How it works? 
    1. A replica connects to the primary.
    2. Redis sends a full snapshot (RDB dump) of the data.
    3. The replica applies updates via a command stream from the primary.
    4. Updates are continuously synced from the primary to the replica.

```bash
cp -r ~/redis-stable redis-s1                # copy the config folder for multiple replicas (slaves)
redis-server ./redis-s1/redis.conf &           # run all the replicas in the background
cat /tmp/redis-m.log                          # check the log of master to see if replicas are successfully connected or not
#### redis.config
port 6371                                   # in the config file change the port for each replicas
logfile "/tmp/redis_s1.log"                 # change the name of log file for each replicas
dbfilename dump_s1.rbd                        # change the name of RDB
replicaof localhost 6370                    # add this line with 'primary-ip' and 'primary-port' to change the server into repilca
replicaof no one                            # add this line into primary server
```


#### 2. Redis high availablilty

+ `Redis High Availability (HA)` means designing a Redis system that remains accessible, reliable, and resilient even if parts of the system fail
+ If the main Redis server crashes, another takes over and clients can keep reading/writing without interruption.
+ `Redis Sentinel` is a tool which is bundeled with redis software.
+ `Redis Sentinel` monitors Redis instances, detects failures, and performs automatic failover by promoting a replica to primary.
+ `quorum` refers to the minimum number of Sentinel nodes that must agree in election that the primary Redis server is down.
+ Quorum prevents false positives — if one Sentinel mistakenly thinks the primary is down (due to a network), it can't trigger failover alone. 
+ When a replica node converts to a primary node, the previous primary node will convert to a replica automatically.
+ Typical Setup:
    1 Primary Redis node
    1+ Replica Redis nodes
    3+ Sentinel processes for quorum

```bash
redis-sentinel /redis-stable/sentinel.conf              # run redis-sentinel with this config
redis-cli -p 26379                                     # connect to redis-sentinel
sentinel master mymaster                               # print information about master
tail -f /tmp/sentinel.log                              # see logs of sentinel
```
> Schema

```text
                                   +------------------+
                                   |  Sentinel Node 1 |
                                   +------------------+
                                           |
                                   +------------------+
                                   |  Sentinel Node 2 |
                                   +------------------+
                                           |
                                   +------------------+
                                   |  Sentinel Node 3 |
                                   +------------------+
                                           |
                     +---------------------------------------------+
                     |              Redis Sentinel Layer           |
                     +---------------------------------------------+
                                           |
                                           ▼
                        Monitors Redis Primary and Replicas

                          +-------------------------------+
                          |         Redis Primary         |
                          |         (Write + Read)        |
                          +-------------------------------+
                                     /         \
                                    /           \
                                   ▼             ▼
                   +------------------+     +------------------+
                   | Redis Replica 1  |     | Redis Replica 2  |
                   |   (Read-only)    |     |   (Read-only)    |
                   +------------------+     +------------------+
```

+ Redis sentinel can be install on the same redis servers, and by default it runs in port: 26379

```css
                            +----------------------------------+
                            | Redis Primary (port:6379)         |
                            | - Sentinel Instance (port:26379)  |
                            +----------------------------------+
                                      /          \
                                     /            \
                                    /              \
                                   ▼                ▼
              +----------------------------------+        +----------------------------------+
              | Redis Replica 1 (port: 6379)     |        | Redis Replica 2 (port:6379)       |
              | - Sentinel Instance (port:26379) |         | - Sentinel Instance (port: 26379) |
              +----------------------------------+         +----------------------------------+
```


> 1. Redis Server (redis.conf)

```bash
# ========== BASIC ==========
port 6379                     # Default Redis port
bind 0.0.0.0                  # Listen on all interfaces    # by default it is 127.0.0.1
bind 192.168.0.1              # Change this to the actual ip address 'hostname -i'   
protected-mode yes            # Enable protected mode
logfile "tmp/redis-m.log"      # change the name of log file

# ========== AUTH (OPTIONAL) ==========
requirepass your_password     # Password for clients
masterauth your_password      # Password for replicas to auth with primary

# ========== REPLICATION ==========

# On replica nodes only:
replicaof <primary-ip> <primary-port>        # Define primary to replicate from

replica-read-only yes          # Disallow writes on replica (recommended)
repl-diskless-sync yes         # Avoid writing temp RDB file to disk
repl-diskless-sync-delay 5     # Wait before starting diskless sync

# ========== PERSISTENCE ==========
appendonly yes                # Enable AOF persistence
appendfsync everysec          # Recommended for durability & performance

# ========== OTHER ==========
daemonize yes                 # Run in background
logfile /var/log/redis.log    # Log file path
dir /var/lib/redis            # Working directory for data and dump
```


> 2. Sentinel (sentinel.conf)

```bash
# ========== BASIC ==========
port 26379                     # Default Redis sentinel port
bind 192.168.0.1              # Change this to the actual ip address 'hostname -i'   
daemonize yes                 # Redis runs in the background (daemon mode)   # default is no
logfile "tmp/sentianel.log"   # change the name of log file

# ========== MONITOR ==========
sentinel monitor mymaster 192.168.0.1 6379 2        # information of master node
#                    ^name  ^host    ^port ^quorum      # if you cahnged the name (mymaster), you should change it in other lines too

# ========== AUTH ==========
sentinel auth-pass mymaster your_password

# ========== FAILOVER SETTINGS ==========
sentinel down-after-milliseconds mymaster 5000             # Time (ms) before declaring primary "subjectively down"
sentinel failover-timeout mymaster 10000                 # Time (ms) to wait before trying next replica or retrying failover
sentinel parallel-syncs mymaster 1                      # How many replicas can sync with new primary at once
```


#### 3. Redis cluster

+ `Redis Cluster` is Redis’s built-in `distributed implementation` that allows you to scale horizontally your infrastructure across multiple nodes 
+ It allows you to `shard and distributed data` across multiple master nodes using automatic partitioning
+ It supports automatic failover, without external tools like `Sentinel`. 
+ unlike sentinel appraoch clustring uses `multiple primary node` with 'No Central Coordinator'
+ each master node having one or more replicas and if a master fails, a replica is promoted automatically.
+ clustering needs at least 6 servers to setup
+ `hash slots` are the way Redis shards (distributes) data across multiple nodes.
+ Every key is assigned to one hash slot
+ Redis Cluster has exactly 16,384 hash slots (numbered from 0 to 16383).
+ These hash slots are then `evenly distributed among master nodes` in the cluster.


```css
[Master1]──[Replica1]
   │
[Master2]──[Replica2]
   │
[Master3]──[Replica3]
```

```bash
# ========== command line ==========
touch n1.conf n2.conf n3.conf ... n6.conf                # create multiple config files if you have one server
redis-server n1.conf &                                   # first run 6 redis server
redis-cli --cluster create \                             # create a cluster with 3 master nodes and 3 replicas 
  192.168.1.1:7000 192.168.1.2:7000 192.168.1.3:7000 \      # first 3 ips becomes master and others become slaves
  192.168.1.4:7000 192.168.1.5:7000 192.168.1.6:7000 \
  --cluster-replicas 1                                       # one replica per master
redis-cli -c -p 7000                                     # connect to redids with Cluster Mode -c for cluster redirecting (access shard data)
redis-cli --cluster check 192.168.1.2:7000               # check the health of cluster
redis-cli -p 7000 cluster nodes                          # print all the cluster nodes information (master and slaces)
# ========== BASIC ==========
port 7000                          # Unique port for each Redis node
bind 0.0.0.0                       # Listen on all interfaces (optional)
protected-mode no                 # Required for external access

# ========== CLUSTER SETTINGS ==========
cluster-enabled yes               # Enable Redis Cluster mode
cluster-config-file nodes.conf    # Auto-generated file with cluster metadata
cluster-node-timeout 5000         # Timeout (ms) for node communication
cluster-announce-ip 192.168.1.1   # Optional: public IP if NAT is used
cluster-announce-port 7000        # Port to advertise to other nodes

# ========== REPLICATION & PERSISTENCE ==========
appendonly yes                    # Enable AOF for durability
appendfsync everysec              # Write AOF every second
dir /var/lib/redis7000            # Data directory (make sure it's unique per node)

# ========== LOGGING ==========
logfile /var/log/redis_7000.log   # Log file (also unique per node)
daemonize yes                     # Optional: run Redis in the background
```











































