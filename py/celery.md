

# Celery



### install

```bash
pip install celery
pip install celery[redis]   # with Redis broker
pip install celery[rabbitmq] # with RabbitMQ broker
```


### Concept

+ Celery is an asynchronous task queue/job queue system written in Python.
+ Celery is like having background workers that you can “hire” to do tasks while your main app stays fast and responsive
+ How It Works? 
    1. Producer (your app) → sends a task (e.g., send_email.delay("user@test.com")).
    2. Broker (Redis/RabbitMQ) → stores the task in a queue.
    3. Worker → pulls the task from the broker, executes it.
    4. Result Backend (optional) → stores the result so you can fetch it later.


```
          +---------------------+
          |     Producer        |
          |  (Your App / API)   |
          +---------------------+
                     |
                     |   .delay() / .apply_async()
                     v
          +---------------------+
          |       Broker        |
          | (Redis / RabbitMQ)  |
          +---------------------+
            |       |       |
   ---------+-------+-------+---------
   |               |                 |
   v               v                 v
+--------+    +--------+        +--------+
| Worker |    | Worker |  ...   | Worker |
|        |    |        |        |        |
+--------+    +--------+        +--------+
     |              |                |
     +--------------+----------------+
                    |
                    v
          +---------------------+
          |   Result Backend    |
          | (Redis / Database)  |
          +---------------------+
                     |
                     v
          +---------------------+
          |   Client / App      |
          |  (Gets Results)     |
          +---------------------+
```

### python


#### Basic Application Setup

```py
from celery import Celery
from time import sleep

# 1. create App      
app = Celery("mpdeul_name", broker="redis://localhost:6379/0")   # 1. name of module (convention)  2. broker
app = Celery("module_name", broker="amqp://guest:guest@localhost:5672")        # broker rabbitmq -> username:password

# 2. define a task
@app.task                            # introduce this function as a task with decorator to celery
def add(x, y):                       # add() is a task
    sleep(5)                         # suppose this is an async task
    return x + y                     # should return a result

# 3. Send task
result = add.delay(4, 6)   # runs in background with delay()
print(result.get())        # -> 10 (retrieved from result backend)
```


##### project layout

```py
my_project/            # name of the module
├── __init__.py
├── celery.py          # Celery app instance and configuration
├── tasks.py           # Your tasks are defined here
└── config.py          # Configuration (optional)
```

#### 1. App Creation (celery.py)

```py
from celery import Celery                # Celery is a class, so we should create an instance

app = Celery(                               # instance from class
    'my_project',                           # App name
    broker='redis://localhost:6379/0',     # Message broker URL   (redis or rabbitmq)
    backend='redis://localhost:6379/0',    # Result backend URL (optional)
    include=['my_project.tasks']            # Modules to import containing tasks
)

# Optional: Configure settings   ---> config.py
app.conf.update(
    task_serializer='json',
    result_serializer='json',
    accept_content=['json'],
    timezone='UTC',
    enable_utc=True,
)
```


### 2. Defining Tasks (tasks.py)

```py
from celery import app

@app.task
def add(x, y):
    return x + y

@app.task(bind=True, max_retries=3) # `bind=True` for access to `self`
def might_fail(self, x, y):
    try:
        return x / y
    except ZeroDivisionError as e:
        self.retry(exc=e, countdown=5) # Retry after 5 seconds
```

##### 3. Keeping results

```py
from celery import Celery

app = Celery(
    "proj",
    broker="amqp://guest:guest@localhost:5672//",   # RabbitMQ broker
    backend="rpc://"                               # Use RPC backend (results via RabbitMQ)
)

@app.task
def add(x, y):
    return x + y

result = add.delay(2,3) 
print(result.get())
```


### 3. call the task

+ we can call the task in 3 ways:
    1. apply_async()
    2. delay()
    3. apply()

```py
from my_project.tasks import add

# 1. apply_async() The preferred way: it has lots of options 
result = add.apply_async(args=[2, 2])      # Returns an AsyncResult object    # sends task message to the broker

# 2. delay() Shortcut for `apply_async` with minimal options
result = add.delay(2, 2)            # async

# 3. apply() Synchronous call (AVOID in production - blocks until done)
result = add.apply(args=[2, 2])
print(result.get()) # Output: 4
```

###### 3.1 apply_async() options

```py
add.apply_async(args=[2, 2], countdown=10)               # Execute after 10 seconds
add.apply_async(args=[2, 2], soft_time_limit=5, time_limit=10)      # Set a time limit (in seconds) for execution
add.apply_async(args=[2, 2], queue='high_priority')      # Assign to a specific queue
add.apply_async([2, 3], eta=datetime(2025, 8, 27, 12, 0))    # Execute at a specific time (datetime object)
```

#### signature

+ Signature is basically a serialized representation of a task invocation.
+ It allows you to pass tasks without executing them immediately

```py
sig = add.signature((2,3))   # Signature of add(2, 3)    you can pass this variable to another program 

# Execute later
print(sig.args)
print(sig.)
sig.delay()         # Send to broker (RabbitMQ/Redis)
sig.apply_async()   # Same, more options
```

+ types of signature
    1. partial
    2. 


### 4. Start a worker

```bash
# From the directory containing `celery.py`           # -l or --loglevel  values: 1. 
celery worker -A my_project --loglevel=INFO          # -A or --app for name of worker (by convention: name of module)
celery -A my_project worker --loglevel=info                       # conventional syntax
celery -A my_project worker --concurrency=4          # For production with multiple processes (-c or --concurrency)
celery -A proj worker -Q queue1,queue2                # specific queues
# For real-time monitoring with event stream
celery worker -A my_project --loglevel=INFO --pool=solo --without-heartbeat --without-mingle --without-gossip
```

###### 4.1 output

```py
 -------------- celery@YourComputer v5.3.0 (emerald-rush)
--- ***** -----
-- ******* ---- Windows/Linux/macOS
- *** --- * ---
- ** ---------- [config]
- ** ---------- .> app:         my_project:0x1234567890
- ** ---------- .> transport:   redis://localhost:6379/0  # Your broker
- ** ---------- .> results:     redis://localhost:6379/0  # Your backend (if set)
- *** --- * --- .> concurrency: 8 (prefork)               # Number of worker processes
-- ******* ---- .> task events: OFF (enable -E to monitor tasks)
--- ***** -----
 -------------- [queues]
                .> celery           exchange=celery(direct) key=celery
                # ^^ The queue(s) this worker is consuming from

[tasks]
  . my_project.tasks.add
  . my_project.tasks.might_fail
  # ^^ List of all registered tasks this worker can execute
```

##### 5. Periodic Tasks (Beat Schedule)

```py
from celery.schedules import crontab

app.conf.beat_schedule = {
    'add-every-30-seconds': {
        'task': 'my_project.tasks.add',
        'schedule': 30.0, # Every 30 seconds
        'args': (16, 16)  # Arguments
    },
    'every-monday-morning': {
        'task': 'tasks.send_weekly_report',
        'schedule': crontab(hour=7, minute=30, day_of_week=1), # Monday 7:30 AM
    },
}
app.conf.timezone = 'UTC'
```


##### Flower

+ Flower is a real-time web-based monitor for Celery.
+ it is a dashboard where you can watch and manage your Celery workers and tasks.
+ visit http://localhost:5555

```bash
pip install flower                         # install flower
flower -A my_project                             # same for laucnh
celery -A my_project flower                     # lauch the server and open http://localhost:5555
celery -A my_project flower --port=5555          # default port 5555
celery -A my_project flower --address=0.0.0.0    # expose externally
celery -A my_project flower --broker=redis://localhost:6379/0
celery -A my_project flower --help              # show options
```


### commnds


```bash
celery                           # help for list of commnds 
celery worker --help           # help for worker
celery -A proj worker              # Start a worker instance.	
celery -A proj beat               # Start the beat scheduler for periodic tasks.	
celery -A proj shell              # Start a Python shell with the app context.	
celery multi start 2 -A proj -l INFO       # Start multiple workers in the background. (Linux/Mac)	
# Check status of workers
celery -A my_project list                                    # List all available tasks
celery -A my_project inspect active                    # list active tasks
celery -A my_project inspect scheduled                  # list scheduled ETA tasks
celery -A my_project inspect registered                    # 	Show list of registered task names.
celery -A my_project inspect reserved                    # list reserved tasks
celery -A my_project inspect ping -d worker1@hostname         # Check if a worker is responsive
celery -A my_project inspect stats                                            # stats in terminal
celery -A my_project purge                                 # Remove all waiting tasks (cannot be undone)
```





















