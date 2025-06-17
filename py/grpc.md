
# gRPC
an open source framework based on `protocol buffers`

## protocol buffer
Protocol Buffers (Protobuf) is a language-neutral, platform-neutral developed by Google which uses a compiler for serializing structured data to binary format

## software architecture styles
> 1. Microservice Architecture: 
    Instead of putting all your code into one app, you break your app into microservices that are deployed independently and communicate with each other.
> 2. Monolithic Architecture (Modular)
    A single, unified codebase where all components (UI, business logic, database) are tightly coupled.
> 3. Event-Driven Architecture (EDA)
> 4. Service-Driven Architecture (SDA)
> 5. Serverless Architecture
> 6. Peer-to-Peer (P2P) Architecture

## Evolution of data
1. csv
    +:  
        1. easy to read
        2. easy to parse 
    -:  
        3. data type 
        4. data with comma 

1. xml
    +:
        1. human readable
        2. Structured & Hierarchical(nested data)
        3. Platform-Independent(java, python,...)
        4. comments
        5. Validation
        6. Unicode Support
    -:
        7. Verbose(Excessive tags increase file size (slower parsing/transmission))
        8. Complex Syntax(attributes and XSD/DTD files are hard to write/maintain)
        9. data type(All data is treated as strings (requires manual conversion))
    where?:
        10. Enterprise systems
        11. Document-heavy workflows (e.g., legal/medical records)
        12. Config files (Android layouts, Maven/SOAP). 

1. json
    +: 
        1. easy to share 
        2. has array 
        3. daynamic
    -: 
        4. redundancy( we can just copy and cant refer to object) 
        5. no comments and no metadata
    where?:
        1. Web APIs.

1. protocol buffer
    +:
        1. Typed
        2. comments
        3. binary (smaller and less CPU intensive to parse)
    -:
        7. not human readable
    where?:
        8. High-performance apps (use Protocol Buffers/MessagePack).
    


### API Types 
1. operating api (os) --> file system, network system, User interface elements, win32 api
2. Library api ---> python, java
3. Remote api ---> when two services are not in the same network but they should be written in the same programing lang and the same platform (remote api is not popular)
4. web api  --->for either a web server or a web browser



####  Web API Types (web API Architecture)
based on req/response

##### 1. SOAP
+ Simple_Object_Access_Protocol
+ maintaind by microsoft
+ XML-based (eXtensible_Markup_Language)
+ RPC style
+ outdated

##### 2. REST
+ REpresentaional_State_Transer
+ build around entitries and not operations
+ desinged in 2000
+ build around http standard and json
+ Message style
+ Uses URL for entity identification
+ REST desinged to run CRUD operations on entities


##### 3.GraphQL
+ developed by Facebook in 2015
+ flexible quering, updating and subsrcibing data changes
+ you can specify which fileds you need, in contrast to rest that returns the whole fields
+ based on json
+ graphql is not easy like rest


##### 4.gRPC
+ developed by google
+ google_Remote_Procedure_Call
+ using protocol buffer as message format
+ calling directly a method on the server from the client and get the response
+ in rest we have no idea what's the method name on the server
+ streaming support
+ good for microservices
+ very fast bcz based on HTTP2 (1. HTTP2 can send multiple request in one TCP connection 2. HTTP2 is binary but 1 is text based))
- Limited Browser support [google create GRPCWeb for addrersing this issue]
- Difficulty in learning and debugging
 

###### Problems with rest?
1. Performance: text-based json
2. request-response only (push notification does not need a request)
3. Limited syntax: Not suited for running actions like: start a new process, disable a device, perform a login
4. Text-based problems: 1. large packets (than binray) 2. Pasring (should be parsed by another software) 


###### Why gRPC?
1. solves problem with http
2. performance

### Concepts

1. Protobuf  : (`.proto` file)the binary format used by gRPC as json for rest (Theoritically gRPC can use other formats but not recommended)
2. Channel   : A definition of connection between clinet and server by specifing the host and port. Channels have a state [connected, idle, etc]
3. service   : A service that server porvides to the client
4. stub    : a stub is a client-side proxy automatically generated during compile that allows applications to call remote server methods as if they were local.
5. Timeout    : Duration of time  the client will wait
6. Deadline    : A fiexd point in time until which the client will wait
7. Metadata    : Information(key-value pairs) about a gRPC call like Authentication details
8. gRPC components: client/server




### Command line
if you compile with `protoc` you get one file, and if you compile with `grpc_tools` you get two files.

```bash
apt install -y protobuf-compiler             # winget install protobuf [widows]
pip install grpcio grpcio-tools
protoc --cpp_out=. *.proto        # --cpp_out, --java-out, --python-out means wihch folder you want for output?
protoc --java_out=. *.proto        # second argument means which files you want to compile?
cat ./path/file.bin | protoc --decode_raw          # decode a binray file using pipline without having proto file {tag:value}
cat ./path/file.bin | protoc --decode=Message_Name file_name.proto          # decode a binray file with having proto file {field_name:value}
cat ./path/file.bin | protoc --decode=Pacakge_name.Message_Name file_name.proto          # Message_Name is inside file_name 
cat ./path/file.txt | protoc --encode=Message_Name file_name.proto > file_name.bin        # encode a text file
python -m grpc_tools.protoc -I . --python_out=. --grpc_python_out=. file_name.proto       # compile with python 
# -I = directory where protoc looks for .proto files
```


### Protobuf syntax
1. define messages and services
2. generate code 
3. use the generated code for creating messages

+ The `protoc compiler` generates client code in the appropriate language
+ every language has its own special compiler

> Tags
    + filed names are not serialised(useful in code), tags are
    + smallest tag=1 , largest tag= 536,870,911
    + reserved tags= 19000,19999 by google for security
    + 1-15 ---> 1 byte long               [more important]
    + 16-2047 ---> 2 bytes long         [less important]

> Types

    + int32, int64 accpet negative values but not efficeient at serilising them
    + sint32, sint64 accept positive vlues but not efficient at serilising them
    + int32, sint32, uint32 are Varint since they allocate the smallest posible size, oposite of fiexed32

```py
    # numbers ---> defult value = 0
    int32, int64, sint32, sint64,            # sint just for negatives
    uint32, uint64,                          # uint just for positives
    fixed32, fixed64, sfixed32, sfixed64,     # fixed32, sfixed32 ---> 4 bytes    fixed64, sfixed64 ---> 8 bytes
    float, double
    # Boolean ---> default value= false
    bool
    # string ---> default value = ""
    string
    # bytes --->default value = empty bytes
    bytes            # useful for images
    ####################
    # Advanced data types: 1. oneof 2. map 3. repeated 4. enum 5. google types

    ####################        ### well-known types by google
    import "google/protobuf/duration.proto";
    import "google/protobuf/timestamp.proto";
    message Account {
        google.protobuf.Timestamp created_at =3;     # timestamp type
        google.protobuf.Duration validity =4;         # duration type
    }
```




#### 1.proto file sample

> naming conventions
    1. file: snake_case.proto
    2. message: CamelCase
    3. enum: CamelCase
    4. field: snake_case_id
    5. enum filed: UPPER_CASE
    6. repeated field: plural name 

```py
syntax = 'proto3';                        # name of file: account.proto
enum EyeColor {                             # enum type
   EYE_COLOR_UNSPECIFIED = 0;               # first tag =0  
   EYE_COLOR_GREEN = 1;                    # default value for enum is its first value
   EYE_COLOR_BLUE = 2;
}
/*
    multi line comments  ----> c like syntax
*/
message Account {
    uint32 id = 1;                 
    string name = 2;
    bool is_verified =3;
    int;                          # this field not be serilised and populated with default value
    repeated int32 phone = 4;      # repeated is for lists  # default value = empty list
    EyeColor eye_color = 7;         # enum type
}
```

#### 2. message type

```py
message City{
    string name =1;
    string country_name =2;
}
message Street{
    string name =1;
    City city =2;        # messgea type
}
```
#### 3. nested messages

```py
message City{                     # nested message
    string name =1;
    string zip_code =2;
    message Strret {
       string name =1;
       City city=2;
       message Building {
            string name= 1;
       }
    }
    enum EnumChild {
        ENUM_CHILD_UNSPECIFIED = 0;
    }
}
message Address{
    City city=1;
    City.Street street =2;
    City.Street.Building building =3;
}
```

#### 4. bottom-top nested message
```py
message Building{                     # nested message
    message Strret {
        message  City{
            string zip_code =2;
            string name= 1;
        }
        string name =1;
        City city=2;
    }
    string name =1;
    uint32 number =2;
    Street street =3;
}
message Address{
    Building.Street.City city =1;
    Building.Street street =2;
    Building building =3;
}
```

#### 5. import messages

```py
import 'my_file.proto'                 # ImportMessage is inside this file
message MyMessage {
    ImportMessage my_name =3;           # message type
}
```

#### 5. import packages

```py
# file: mypackagefile.proto
syntax ='proto3';
package my.package           # namge of my package
message MyMessage{}         # packages are good for name conflicting
# another file
syntax ='proto3';
import "mypackagefile.proto"    # improt file name
message Something{              # syntax: package_name.message_name
    my.package.MyMessage my_message = 6;       # use dot notation to access
}
```

#### 6. Reserved

> rules for editng:
    + its ok to add or remove fields and it does not affect to server or client
    + its ok to change the fields name but never change the tags
    + if you removed a field use `reserved` to prevent future mistakes.
    + beaware of defulat values, with defualt values we cannot have unset values bcz all of them gets a defualt, even we dont set any value.

```py
message Person{
    uint32 id = 1;                  # reserved is mostly use when you delete a field
    reserved 2;                    # this means dont use tag number 2 for other fields
    reserved 2,3,5 to 9;               # we can specify multiple tags
    reserved "first_name";        # optional # it means dont create a field_name by this value
}
```
#### 7. Options
+ options provide additional information providede for services or data.
 

1. filed options

```py
string old_name = 1 [deprecated = true];          # old fashioned
repeated int32 scores = 3 [packed = true];         #  Optimizes encoding for repeated fields of scalar numeric types
optional string language = 4 [default = "en"];      # set default value
```

1. message options
2. file options
4. enum options

#### 9. service
+ when you compile a file, it generates 2 files: one for message definition and one for services


```py
service Greeter{         # function_name (request) returns (response)
    rpc SayHello (HelloRequest) returns (HelloReply){}       # rpc defines a function
}
message HelloRequest{
    string name =1;         # request for a name and recieve a message
}
message HelloReply{
    string message =1;
}
```

## python code

### py: 1. simple message
```py
# simple.proto
syntax = "proto3";
package exmpale.simple;

message Simple{
    uint32 id=1;
    bool is_simple =2;
    string name =3;
    repeated int32 b = 4;         # b is an array of inst32 

}
######### main.py
from proto import simple_pb2       # by protoc compile the simple.proto file and import the compiled file
def simple():                     # simple_pb2 is the name of compiled file
    return simple_pb2.Simple(
        id = 45,
        is_simple = True,
        name= 'my name',
        b=[1,2,2]
    )
```

### py: 2. nested message

```py
syntax = "proto3";
package exmaple.complex;

message Dummy {
    int32 id =1;
    string name =2;
}

message Complex {
    Dummy one_dummy =1;
    repeated Dummy multiple_dummies = 2;
}
######### main.py
def complex():
    message = complex_pb2.Complex()   # for nested message call the constructor
    message.one_dummy.id = 1           # if you act like simple message you get error
    message.one_dummy.name = "myname"
    message.multiple_dummies.add(id=2, name="ali")          # multiple_dummies is array of object
    message.multiple_dummies.add(id=22, name="alisad")
    return message
```

### py: enum
```py
syntax = "proto3";
enum EyeColor{
    EYE_COLOR_UNSPECIFIED =0;
    EYE_COLOR_GREEN =1;
    EYE_COLOR_DlUE =2;
    EYE_COLOR_WHITE =3;
}
message Enumeration {
    EyeColor eye_color =1;
}
###### main.py
a= enumerations_pb2.Enumeration(
    eye_color = enumerations_pb2.EYE_COLOR_WHITE           # for the value of filed we need to call from enum
    # eye_color = 1     # another syntax without calling but not descriptive
)
```

### py: oneof
```py
message Oneof{
    oneof Result {
        string message =1;
        uint32 id =2;
    }
}
###### main.py
a = oneof_pb2.Result(message = "hello")
print(a)          # show: message: hello
a.id = 13;        # message field will delete and id will replace it ---> oneof
print(a)          # show: id:13
```

### py: map
```py
message IdWrapper {            # message with id inside
    uint32 id=1;                # cannot use 1.float 2.double 3.enum as key in maps
}
message MapExample{                     # map fields are dictionary allowing you to store key-value pairs
    map<string, IdWrapper> ids = 1;     # {key: string_type, value: message_type}  
    # map<string, int32> ids = 1;  // Key: string, Value: int32
}
###### main.py
a = map_pb2.MapExample()
a.ids["ali"].id = 45
a.ids["asad"].id = 42
print(a)
```


### py: serialise vs deserialize 
```py
book = book_pb2.Book()           # Create a Book instance
book.isbn = "123"  
book.title = "choice theory"

serialized_book = book.SerializeToString()      # Serialize to binary
print("Serialized:", serialized_book)  # Binary output (not human-readable)

deserialized_book = book_pb2.Book()                 # Deserialize back to a Book object
deserialized_book.ParseFromString(serialized_book)         # Human-readable Proto3 format
```

### py: read/write to disk
```py
message = simple_pb2.Simple(             # message is an instance
        id = 45,
        is_simple = True,
        name= 'my name',
)
with open("./path/myfile.bin","wb") as f:             # write in a bianry file
    bytes_as_string = message.SerializeToString()       # b'\x08-\x10\x01\x1a\x07my name'
    f.write(bytes_as_string)

with open("./path/myfile.bin","rb") as f:       # t() creates a new empty instance of the protobuf message class.
    # t = type(message)                                   # <class 'simple_pb2.Simple'>
    t= simple_pb2.Simple                             # call the constructor
    message2 = t().FromString(f.read())                  # Deserializes the binary data
```

### py: read/write to json

```py
import google.protobuf.json_format as json_format
msg = a_pb2.Simple(         
    id = 45,
    name= 'my name',
)
a = json_format.MessageToJson(msg)
print(a)
b= json_format.Parse(a, a_pb2.Simple)     # parse(json, constructor)
print(b)
```




## gRPC communications styles
1. Unary
    a. a req-response model(sever client model) like HTTP: clinet send a request and server returns a single response
    b. calling a method directly on the server

1. Client Streaming
    a. Clinet opens a connection to the server
    b. Clinet sends continouse messages to the server
    c. good for : chat, telemetry

1. Server Streaming
    a. The clinet opens a connection to the server
    b. Server sends a continouse message to the clinet
    c. great for notification, chat, etc

2. Bi-directional
    a. The clinet opens a connection to the server
    b. Both the client and the server send continouse message to each other


### 1. Unary

```py
syntax = "proto3";
service Greeter {                     # The greeting service definition.
    rpc SayHello (HelloRequest) returns (HelloReply) {}
  }
  
  message HelloRequest {         # The request message containing the user's name.
    string name = 1;
  }
  
  message HelloReply {         # The response message containing the greetings
    string message = 1;
  }
###################### server.py ###############################
import grpc                             # for server
from concurrent import futures         # for multi threading 
import hello_pb2                       # this file is for messages [HelloReply, HelloRequest]
import hello_pb2_grpc                 # this file is for service
# rpc methods impleament as classes
class Greeter(hello_pb2_grpc.GreeterServicer):  # inherit from rpc servicer       
    def Sayhello(self,request,context):     # context provies some functionality such as metadata and error handling
        return hello_pb2.HelloReply(message='HELLO %s' %request.name)

def server():       # create a server that performs parallel tasks
    server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))       # create a server
    hello_pb2_grpc.add_GreeterServicer_to_server(Greeter(), server)         # servicer(class(), server)
    server.add_insecure_port('[::]:50051')                          # we call insecure_channel in client.py
    server.start()                                                    # add_GreeterServicer_to_server()
    server.wait_for_termination()                                    # do not kill server automatically
 
################################ client.py  ############################
def run():            # with in python
    with grpc.insecure_channel('localhost:50051') as channel:         # create a changel
        stub = hello_pb2_grpc.GreeterStub(channel)                   # create a stub object
        a = hello_pb2.HelloRequest(name='Ali')                       # create a request
        response = stub.SayHello(a)  # with stub we can connect to server
        print(response.message)      # SayHello is a method inside server.py
```

### 3. server Streaming
```py
syntax = "proto3";              # client send a singel req and server respond with multiple responses in sequence
package example;
service ChatService {
    rpc ChatStream(ChatMessage) returns (stream ChatMessage) {}        # stream keyword is for server
}                                                        # stream means send data in stream format
message ChatMessage {
    string message = 1;
}
######################## server.py ###############################
import grpc
from concurrent import futures
import messages_pb2
import messages_pb2_grpc  
import time
# create a service by class
class ChatService(messages_pb2_grpc.ChatServiceServicer):  # inherit from service class inside generated code
    def ChatStreamFunction(self, request, context):  # process the request and send reponse
        messages = [                         # list of messages for streaming
            "Hi!",                            # we used 'stream' for response in proto file
            "Server Streaming Example",       # there is no need for `return` just Yield the message to client
            "Good Bye!"
        ]
        for message in messages:            # yield used in generator functions to produce a sequence of values lazily
            yield messages_pb2.ChatMessage(message=message)  # initialise the message_type with values
            time.sleep(1)     # the server streams 3 predefined messages with 1-second intervals.

def run_server():            # server is the same in unary
    server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))
    messages_pb2_grpc.add_ChatServiceServicer_to_server(ChatService(), server)
    server.add_insecure_port('[::]:50051')
    server.start()
    print("Server running on port 50051...")
    server.wait_for_termination()

################################### client.py ########################
def run_client():
    with grpc.insecure_channel('localhost:50051') as channel:       # create stub: ServiceNameStub
        stub = messages_pb2_grpc.ChatServiceStub(channel)         
        req = messages_pb2.ChatMessage(message="Client ready")      # create a request
        b = stub.ChatStreamFunction(req)      # call ChatStreamFunction from server.py
        for response in b:                     # b is an generator
            print(f"Received: {response.message}")
```
### 3. Client Streaming
```py
syntax = "proto3";
package streaming;

message RequestMessage {
    string data = 1;
}

message ResponseMessage {
    string result = 1;
}

service StreamingService {
    rpc StreamData(stream RequestMessage) returns (ResponseMessage);       # stream for client
}
############################# server.py ###############################
import grpc
from concurrent import futures
import streaming_pb2
import streaming_pb2_grpc  

class StreamingService(streaming_pb2_grpc.StreamingServiceServicer):  
    def StreamData(self, request_iterator, context):                      # request_iterator instead of request
        """Processes a stream of client messages and returns a single response."""
        result = []                                         # server return result
        for request in request_iterator:                   # Iterate through client's stream
            result.append(request.data)                    # Assuming 'data' is the field name in your proto
        
        print(result)                                     # Print all received data
        return streaming_pb2.ResponseMessage(             # Return a single response after processing all messages
            result=" ".join(result)                    # Combine all received data
        )                                            # Assuming ResponseMessage has 'result' field of type string

def run_server():
    server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))
    streaming_pb2_grpc.add_StreamingServiceServicer_to_server(StreamingService(), server)
    server.add_insecure_port('[::]:50051')
    server.start()
    print("Server running on port 50051...")
    server.wait_for_termination()
################################### client.py ########################
def generate_requests():
    messages = ["message 1", "message 2", "message 3"]
    for msg in messages:                    # instead of return we use yield
        yield streaming_pb2.RequestMessage(data=msg)

def run_client():
    with grpc.insecure_channel('localhost:50051') as channel:
        stub = streaming_pb2_grpc.StreamingServiceStub(channel)
        a = generate_requests()                # a is a generator
        response = stub.StreamData(a)           # call StreamData() from server.py and recieive response
        print(f'Response from server: "{response.result}"')
```

### 4. Bi-directional
```py
syntax = "proto3";
package example;

service ChatService {
    rpc Chat(stream ChatMessage) returns (stream ChatMessage) {}          # both stream
}

message ChatMessage {
    string message = 1;
}
########################### server.py ###############################
import grpc
from concurrent import futures
import messages_pb2
import messages_pb2_grpc  # Fixed import name
import time

class ChatService(messages_pb2_grpc.ChatServiceServicer):  # Fixed inheritance
    def Chat(self, request_iterator, context):
        """Bidirectional streaming RPC handler."""
        for request in request_iterator:
            # Process incoming message
            print(f"Client Message: {request.message}")  # Fixed string quote
            
            # Prepare response
            response_message = f"Server processed: {request.message}"
            print(f"Server Response: {response_message}")
            
            # Send response back to client
            yield messages_pb2.ChatMessage(message=response_message)  # Fixed comma to dot
            time.sleep(1)  # Optional delay between responses

def run_server():
    server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))
    messages_pb2_grpc.add_ChatServiceServicer_to_server(ChatService(), server)
    server.add_insecure_port('[::]:50051')
    server.start()
    print("Server running on port 50051...")
    server.wait_for_termination()


############################### client.py ###############################
def request_generator():
    """Generates messages to stream to the server"""
    messages = ['Hi!', "gRPC Bidirectional!", "Well!"]
    for msg in messages:
        print(f"[Client] Sending: {msg}")
        yield messages_pb2.ChatMessage(message=msg)
        time.sleep(2)  # 2-second delay between messages

def run_client():
    with grpc.insecure_channel('localhost:50051') as channel:
        stub = messages_pb2_grpc.ChatServiceStub(channel)
        a = request_generator()
        responses = stub.Chat(a)           # call Chat() from server.py which returns a response
        for response in responses:         # the response is a generator
            print(f"[Server Response]: {response.message}")

```

### Request cancellation
+ cancellation for 1. timeout 2. deadline 3. user experience 4. resource saving 5. error handling
+ request cancellation handle with asyncronous requests
+ to cancel a req a separate thread would need to be used to cancel the request after a certain time [ we create 10 thread]

```py
########################### server.py ###############################
class CancelService(cancel_example_pb2_grpc.CancelServiceServicer):
    def LongRunningOperation(self, request, context):       # use context for error handling, metadata
        for i in range(10):                       # use for loop to check if the request is cancelled
            if context.is_active():               # check if the request is still active
                print(f"Processing {i}...")
                time.sleep(1)
            else:                               # check cancellation for 10 seconds
                print("Request was cancelled")
                return cancel_example_pb2.Response(response_data="Cancelled")
        return cancel_example_pb2.Response(response_data="Completed")       # if not cancelled 

############################ client.py ###############################
def run_client():
    with grpc.insecure_channel('localhost:50051') as channel:
        stub = cancel_example_pb2_grpc.CancelServiceStub(channel)
        request = cancel_example_pb2.Request(request_data="Start")
        future = stub.LongRunningOperation.future(request)       # future object returned from an async call
        time.sleep(3)  # Wait 3 seconds before canceling
        future.cancel()  # grpc.Future.cancel() to cancel a request
        # + grpc.Future.cancelled() to check if a request is cancelled
        # + grpc.Future.done() to check if a request is compelte
    try:
        response = future.result()  # Attempt to get result
        print(f"Response received: {response.response_data}")           # print response is evertyhing ok
    except grpc.FutureCancelledError as e:
            print("Request was successfully cancelled.")
```
### Data compression

+ data compression: 
    1. saves network bandwidth 
    2. increases performance and speed 
    3. reduce costs

+ considerations: 
    1. compretion level(higher compretion level increase decompression time) 
    2. message size (for small size not recommended) 
    3. cpu usage

Server Side Compression:
    + `grpc.server(compression=...)`: Sets default compression for all server responses
    + `context.set_compression(...)`: Changes compression for individual RPC responses

Client Side Compression:
    + `grpc.insecure_channel(compression=...)`: Sets default compression for all client calls
    + `channel.unary_unary(... compression=...)`: Sets compression for specific RPC calls

Compression Methods:
    `grpc.Compression.NoCompression`: No compression
    `grpc.Compression.Deflate`: Deflate algorithm
    `grpc.Compression.Gzip`: Gzip algorithm  -- recommended

```py
########### server
server = grpc.server(futures.ThreadPoolExecutor(max_workers=10),compression=grpc.Compression.Gzip)   # entire server side
def rpcMethod(self, request, context):
    context.set_compression(grpc.Compression.Gzip)            # set compression just for this rpc method
########### client
with grpc.insecure_channel('localhost:50051', compression=grpc.Compression.Gzip) as channel:        # entire client side
    # channel.unary_unary(rpcMethod) 
    stub = helloworld_pb2_grpc.GreeterStub(channel)
    response = stub.SayHello(helloworld_pb2.HelloRequest(name='you'), compression=grpc.Compression.Deflate)   # just for this in client side
```

### Interceptors

an Interceptor intervenes in the process of transfering and allows specific tasks to be done before and after grpc calls
    . monitoring
    . logging
    . error handling
    . Authentication

there are 2 types:
 1. client interceptor : operates before sending a request + after receiving a response
 2. server interceptor : operates afer receiving a request + before sending a response

```py
import grpc
from concurrent import futures
import interceptor_example_pb2
import interceptor_example_pb2_grpc

class LoggingInterceptor(grpc.ServerInterceptor):   # create an interceptor
    def intercept_service(self, continuation, handler_call_details):      # we should impleament internal logic by overriding intercept_service()
        method = handler_call_details.method                           # handler_call_details contains details about the request
        print(f"Received request for method: {method}")                 # method of request
        response = continuation(handler_call_details)       # continuation passes control back to the RPC method after the execution of interceptor
        return response

class EchoServiceServicer(interceptor_example_pb2_grpc.EchoServiceServicer):  # our service
    def Echo(self, request, context):
        print("Echo")                            # before this line interceptor will be executed
        return interceptor_example_pb2.EchoResponse(message=request.message)

def run_server():
    server = grpc.server(
        futures.ThreadPoolExecutor(max_workers=10),
        interceptors=[LoggingInterceptor()]                    # register the interceptor inside the server
    )
    interceptor_example_pb2_grpc.add_EchoServiceServicer_to_server(EchoServiceServicer(), server)     # same
    server.add_insecure_port('[::]:50051')
    server.start()
    server.wait_for_termination()

################################### client.py ########################
class LoggingInterceptor(grpc.UnaryUnaryClientInterceptor):        # create an interceptor
    def intercept_unary_unary(self, continuation, client_call_details, request):    # diff with server
        method = client_call_details.method                    # 1. unary_unary 2. stream_unary 3. unary_stream 4. stream_stream
        print(f"Sending request to method: {method}")                    # Log outgoing request
        response = continuation(client_call_details, request)             # Continue RPC call
        return response

def run_client():
    with grpc.insecure_channel('localhost:50051') as channel:                            # Create channel
        intercepted_channel = grpc.intercept_channel( channel, LoggingInterceptor())      # Add interceptor to channel
        stub = interceptor_example_pb2_grpc.EchoServiceStub(intercepted_channel)          # Create stub
        response = stub.Echo(interceptor_example_pb2.EchoRequest(message="hello, gRPC!"))
        print(f"Response: {response.message}")
```


### Status codes
```py
#################################### server.py ########################
class Calculator(error_handling_example_pb2_grpc.CalculatorServicer):
    def Divide(self, request, context):
        if request.divisor == 0:
            context.set_code(grpc.StatusCode.INVALID_ARGUMENT)       # set StatusCode for sending to client
            context.set_details("Divisor cannot be zero.")         # set a message for sending to client
            return error_handling_example_pb2.DivideResponse()     # return empty response
        quotient = request.dividend / request.divisor
        return error_handling_example_pb2.DivideResponse(quotient=quotient)
################################### client.py ########################
def run():
    with grpc.insecure_channel('localhost:50051') as channel:
        stub = error_handling_example_pb2_grpc.CalculatorStub(channel)
        try:
            response = stub.Divide(error_handling_example_pb2.DivideRequest(dividend=10, divisor=2))
            print(f"Quotient: {response.quotient}")
        except grpc.RpcError as e:                             # for handling exceptions
            print(f"RPC failed: {e.code()} - {e.details()}") # code for status code
```

### Health checking
+ Health ckeck allow continouse monitoring of the service's functionality. if an issue arises it can be detected immediately.
+ `pip install grpcio-health-checking`
+ we dont need to compile the protofile(its predefined and precompiled)

```py
syntax = "proto3";               # we dont need this file and we dont need to compile it (its predefined and compiled)
package grpc.health.v1;            # we just override it

service Health {        #// Health service enables servers to expose their health status to clients.
    rpc Check(HealthCheckRequest) returns (HealthCheckResponse);      #// Check returns the health status of the requested service.
    rpc Watch(HealthCheckRequest) returns (stream HealthCheckResponse); #// Watch streams health status updates
}

message HealthCheckRequest {  # predefined messages
    string service = 1;  
}

message HealthCheckResponse {
    enum ServingStatus {     #// Response message containing the health status.
        UNKNOWN = 0;         #// Status is indeterminate.
        SERVING = 1;        # // Service is healthy.
        NOT_SERVING = 2;    # // Service is not healthy.
        SERVICE_UNKNOWN = 3; # // Requested service is unknown.
    }
    ServingStatus status = 1;  # // Current status.
}
############################ server.py ############################
# we can use predefined messages and services without overriding them
health_servicer = health.HealthServicer()
health_servicer.set("my_service", health_pb2.HealthCheckResponse.SERVING)
health_pb2_grpc.add_HealthServicer_to_server(health_servicer, server)

############################ server.py ############################
# with overriding
from concurrent import futures
import grpc
import time
from grpc_health.v1 import health_pb2
from grpc_health.v1 import health_pb2_grpc
from grpc_health.v1.health import HealthServicer

class HealthChecker(HealthServicer):         # inherit from HealthServicer
    def __init__(self):                      # create a field for the class
        self.status_map = {
            "": health_pb2.HealthCheckResponse.SERVING,
            "my_service": health_pb2.HealthCheckResponse.SERVING,
        }

    def Check(self, request, context):              # check and watch are very similar 
        status = self.status_map.get(               # override Check method
            request.service, 
            health_pb2.HealthCheckResponse.SERVICE_UNKNOWN
        )
        return health_pb2.HealthCheckResponse(status=status)          # return the status of the class

    def Watch(self, request, context):                         # override Watch method
        while True:                                                # for streaming
            status = self.status_map.get(
                request.service,
                health_pb2.HealthCheckResponse.SERVICE_UNKNOWN
            )
            yield health_pb2.HealthCheckResponse(status=status)        # yield the status of the class
            time.sleep(5)

def run_server():           # create a server
    server.start()

############################## client.py ############################
import grpc
from grpc_health.v1 import health_pb2, health_pb2_grpc
def run_check():
    with grpc.insecure_channel('localhost:50051') as channel:
        stub = health_pb2_grpc.HealthStub(channel)
        response = stub.Check(health_pb2.HealthCheckRequest(service="my_service"))
        print(f"Health Check Status: {response.status}")
```

### Reflection
+ Reflection allow us to know the services and methods available on the server
+ usefull for debugging
+ enables clients to discover services and methods available on the server and intract with them without knowing its protocol details
+ but not all services information can be retrieved using reflection (prevent sensitive information)
+ `pip install grpcio-reflection`  ---> for server, client
+ `apt install grpcurl`            ---> for client without python




```py
syntax = "proto3";
package reflection_example;

service EchoService {  
    rpc Echo (EchoRequest) returns (EchoResponse);  
}

message EchoRequest {  
    string message = 1;  
}

message EchoResponse {  
    string message = 1;  
}
################################ server.py ############################
from concurrent import futures
import grpc
from grpc_reflection.v1alpha import reflection
import reflection_example_pb2
import reflection_example_pb2_grpc

class EchoServiceServlet(reflection_example_pb2_grpc.EchoServiceServicer):          # its better that name of the class be same as service name
    def Echo(self, request, context):
        return reflection_example_pb2.EchoResponse(message=request.message)

def serve():
    server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))                           # as usual
    reflection_example_pb2_grpc.add_EchoServiceServicer_to_server(EchoServiceServlet(),server) # as usual
    # reflection just have 2 steps: 1. list of services 2. enable reflection 
    SERVICE_NAMES = (            # this tupple contains all service names which we want to expose
        reflection_example_pb2.DESCRIPTOR.services_by_name['EchoService'].full_name,   # this name comes from proto file
        reflection.SERVICE_NAME,                  # syntax
    )
    reflection.enable_server_reflection(SERVICE_NAMES, server)          # enable reflection

    server.add_insecure_port('[::]:50051')
    server.start()
    server.wait_for_termination()
################################ client.py ############################
import grpc
from grpc_reflection.v1alpha import reflection_pb2, reflection_pb2_grpc           # predefined files

def run():
    with grpc.insecure_channel("localhost:50051") as channel:
        stub = reflection_pb2_grpc.ServerReflectionStub(channel)

        # List all services
        request = reflection_pb2.ServerReflectionRequest(list_services="")       # use predefined files to get list of services
        response = stub.ServerReflectionInfo(iter([request]))            # it is a bidirectional streaming RPC
        for service in response:                                    # there is a default service called ServerReflection in this list
            for service_response in service.list_services_response.services:
                print(f"Service: {service_response.name}")

        # Get proto descriptor for a specific service
        service_name = "reflection_example.EchoService"           # Change this as needed
        request = reflection_pb2.ServerReflectionRequest(file_containing_symbol=service_name)     # like previous but for specific 
        response = stub.ServerReflectionInfo(iter([request]))          # like previous
        for file_descriptor in response:
            for proto in file_descriptor.file_descriptor_response.file_descriptor_proto:
                print(f"Proto: {proto}")
```
#### Reflection: grpcurl

```bash
apt install grpcurl                                     # choco install grpcurl
grpcurl -plaintext localhost:50051 list
grpcurl -plaintext localhost:50051 SERVICE_NAME           # SERVICE_NAME from list command
grpcurl -plaintext -d '{"message": "hello, gRPC!"}' localhost:50051 SERVICE_NAME            # send message with -d
grpcurl -plaintext -d '{"message": "hello, gRPC!"}' localhost:50051 reflection_example.EchoService/Echo   # example
```

### Wait

+  wait: is the process where the client waits for a response from the server (this process includes several mechanisms)
+  a timeout is set for wating for a response and if the response is not received within the timeout, the client will raise an exception

```py
# client.py
response = stub.FunctionFromServer(my_example_pb2.MessageRequest(message="Hello, gRPC!"), timeout=1)    # wait 1 second
```

### Keepalive
+ Keepalive is a mechanism that allows the client to send pings(signals) to the server to keep the connection alive
+ Due to network env or firewalls inactive connections can be closed

```py
keep_alive_options = [
    ('grpc.keepalive_time_ms', 10000),  # 10 seconds  # Sets the interval between keepalive pings
    ('grpc.keepalive_timeout_ms', 5000),       # Sets the time the sender waits for a ping ack before closing the connection
    ('grpc.keepalive_permit_without_calls', 1),  # Allows keepalive pings even when there are no active RPC calls
    # additional info by http2
    ('grpc.http2.max_pings_without_data', 0),  # Sets the max number of pings that can be sent without any data/header frames (0 =unlimited)
    ('grpc.http2.min_time_between_pings_ms', 10000),  # Sets the minimum time between consecutive pings
    ('grpc.http2.min_ping_interval_without_data_ms', 10000) # Sets the min time between pings when there is no data/headers
]
########### server.py
server = grpc.server(futures.ThreadPoolExecutor(max_workers=10,options=keep_alive_options))
########## client.py
with grpc.insecure_channel('localhost:50051',options = keep_alive_options) as channel
```

### Retry



### gRPC in Browser

browser ---1--> gRPC-Web Proxy ---2--> gRPC server
        <--4---                <--3---

1: send req in Http1 or Http2
2: translate to grpc request
3: server returns grpc response
4: proxy translate the grpc response to grpc-web response

+ grpc-web only supprts 1. unray and 2. server-streaming communications
+ we have 2 npm packages for grpc-web proxy: 1. Google gRPC-Web client 2. Improbable gRPC-Wev client

##### Google gRPC-Web client
+ comes with Envoy-based proxy
+ send payload as text and not binary
