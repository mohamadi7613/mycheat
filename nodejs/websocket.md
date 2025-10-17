


# websocket

+ Protocol: ws:// (insecure), wss:// (secure, TLS).



### server

```js
const WebSocket = require("ws");
const wss = new WebSocket.Server({ port: 3000 });

wss.on("connection", (ws) => {
  console.log("Client connected");

  ws.on("message", (message) => {
    console.log("Received:", message.toString());
    ws.send(`Echo: ${message}`);
  });

  ws.on("close", () => console.log("Client disconnected"));
});
```

### Client

```js
// Create connection
const socket = new WebSocket("ws://localhost:3000");

// Connection opened
socket.onopen = () => {
  console.log("Connected!");
  socket.send("Hello Server!");
};

// Listen for messages
socket.onmessage = (event) => {
  console.log("Message from server:", event.data);
};

// Handle errors
socket.onerror = (err) => {
  console.error("WebSocket error:", err);
};

// Handle close
socket.onclose = () => {
  console.log("Connection closed");
};
```





















