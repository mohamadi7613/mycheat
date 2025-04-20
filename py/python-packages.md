

# xml-rpc
```py
############################  server.py
from xmlrpc.server import SimpleXMLRPCServer
def add(num1,num2):       # register this function
    return num1+ num2
server = SimpleXMLRPCServer(("localhost",6789))   # run a server and client can send req
server.register_function(add,"add")           # by the second arguemnt I access in client
server.serve_forever()
############################  client.py
import xmlrpc.client
proxy =xmlrpc.client.ServerProxy("http://localhost:6789")
a = proxy.add(45,20)
print(a)    # 65
```
