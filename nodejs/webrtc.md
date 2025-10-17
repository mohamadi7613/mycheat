
# WebRTC (Web Real-Time Communication)

+ WebRTC is not a single protocol like Http
+ WebRTC is a collection of protocols, APIs, and standards

### Core Concepts

1. Peer-to-Peer (P2P) →     direct connection between clients(browsers).
2. MediaStream →            audio/video from getUserMedia.
3. RTCPeerConnection →      main API to connect peers.
4. RTCDataChannel →         send arbitrary data (chat, files, game state).
5. Signaling →              exchanging session descriptions (SDP) + ICE candidates (usually via WebSocket/HTTP server).


### Protocols WebRTC Uses

ICE (Interactive Connectivity Establishment)

Finds the best path for peer-to-peer connections.

STUN (Session Traversal Utilities for NAT)

Helps peers discover their public IP addresses behind NAT.

TURN (Traversal Using Relays around NAT)

Relays traffic when direct P2P connection is blocked.

DTLS (Datagram Transport Layer Security)

Encrypts all WebRTC data (audio, video, messages).

SRTP (Secure Real-Time Transport Protocol)

Carries audio and video streams securely.

SCTP (Stream Control Transmission Protocol)

Used over DTLS for RTCDataChannel (data transfer).


### Get Media (Camera/Microphone)

```js
const stream = await navigator.mediaDevices.getUserMedia({ 
  video: true, 
  audio: true 
});
videoElement.srcObject = stream;
```

### create peer connection

```js
const pc = new RTCPeerConnection({
  iceServers: [{ urls: "stun:stun.l.google.com:19302" }]
});

// Local ICE candidates
pc.onicecandidate = (event) => {
  if (event.candidate) {
    sendToSignalingServer(event.candidate); // custom signaling
  }
};

// Remote stream
pc.ontrack = (event) => {
  remoteVideo.srcObject = event.streams[0];
};

// Add local stream
stream.getTracks().forEach(track => pc.addTrack(track, stream));
```
