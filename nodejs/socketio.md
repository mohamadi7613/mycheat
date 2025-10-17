

# socket.io


### WebSocket

+ A protocol built on top of HTTP
+ Starts as an HTTP handshake, then upgrades the connection
+ After upgrade → full-duplex, persistent connection (client ⇆ server).
+ websocket is different from socket.io (they cannot work with each other)
+ Socket.IO = library that uses HTTP + WebSocket
+ in Rest model the client always initiates communication but socket i bidirectional
+ rest model is for CRUD APIs and JSON over HTTP
+ Rest and socket are both Communication Model

### install

```bash
npm install socket.io
npm install socket.io-client   # (for frontend if needed)
```


### Basic server

```js
const express = require("express");                // 1. express
const http = require("http");                       // 2. http
const { Server } = require("socket.io");            // 3. socket.io

const app = express();                              // 1. express app
const server = http.createServer(app);              // 2. http server() 
const io = new Server(server, {                     // 3. io server is top of all
  cors: { origin: "*" }                             // configure properly in production
});

io.on("connection", (socket) => {                    // 'connection' is conventional
  console.log("New client connected:", socket.id);

  socket.on("disconnect", () => {
    console.log("Client disconnected:", socket.id);
  });
});

server.listen(3000, () => console.log("Server running on port 3000"));
```


### Basic js client

```js
<script src="/socket.io/socket.io.js"></script>
<script>
  const socket = io("http://localhost:3000");

  socket.on("connect", () => {
    console.log("Connected:", socket.id);
  });

  socket.on("message", (msg) => {
    console.log("Message from server:", msg);
  });

  // Send message
  socket.emit("message", "Hello Server!");
</script>
```

### Basic react client

```js
import React, { useEffect, useState } from 'react';
import { io } from 'socket.io-client';

const socket = io('http://localhost:3000');

function ChatComponent() {
  const [message, setMessage] = useState('');
  const [chat, setChat] = useState([]);

  useEffect(() => {
    socket.on('connect', () => {
      console.log('Connected:', socket.id);
    });

    socket.on('message', (msg) => {
      setChat((prev) => [...prev, msg]);
    });

    return () => {
      socket.off('connect');
      socket.off('message');
    };
  }, []);

  const sendMessage = () => {
    socket.emit('message', message);
    setMessage('');
  };

  return (
    <div>
      <h3>Chat</h3>
      {chat.map((msg, i) => <p key={i}>{msg}</p>)}
      <input value={message} onChange={(e) => setMessage(e.target.value)} />
      <button onClick={sendMessage}>Send</button>
    </div>
  );
}
```

#### 1. Auth

```js
const socket = io('http://localhost:3000', {
  auth: { token: 'your_jwt_token' }
});
```


