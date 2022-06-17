## Getting Started with ASP.NET Core 2 SignalR

- OVERVIEW:
  - by Roland Guijt
  - This light-weight, compact course will teach you what ASP.NET Core SignalR is, how it uses underlying real-time web techniques, how to get started using it, and how it's different from earlier versions.
  - Prerequisites: ASP.NET Core. Javascript.

- UNDERSTANDING THE REAL-TIME WEB:
  - Real-time Web applications:
    - e.g.: Email clients. (No reload on *new* email.) Social media. Web documents. Auctions. Gaming. Sock quotes.
  - AJAX:
    - Allow the browser to perform a server request using JavaScript. No page reload.
  - Polling:
    - HTTP: The process of a client requesting from a server. We can periodically ask the server if there is an update. 
    - Many requests. Heavy load.
  - Long polling:
    - Browser requests. Server will not complete request, but leave it open. Without update, the request times out. 
    - Loop. Is there an update? Less requests with more open requests. NOTE: Server timeout does not always match client timeout.
    - BUT, we are using HTTP requests for something that it was not meant to do.
  - Server-sent events (SSE:)
    - HTML 5 feature. Server creates an HTTP connection and the browser listens for a stream of messages.
    ```csharp
      Response.ContentType = "text/event-stream";
    ```
    - One HTTP connection open. All events will pass through this connection. Auto reconnect. Simple HTTP. 
    - Polyfilled. Maximum connection issue. Text messages only. One-way connection.
  - WebSockets:
    - A standardized manner in which to leverage one TCP socket through which messages can be sent from server to client and vice versa.
    - No HTTP latency. Most efficient transport. Full duplex messaging. No 6 connection limit. 50 commections for Web Socket.
    - Multi-data -type support.
    - TCP socket upgrade. WS protocol.
    - TCP Socket [HTTP Handshake. Exchange Data. Close.]
  - WebSocket Handshake:
    - C: Shall we upgrade?
    - S: Yes.
    - C: Upgrade: websocket. With a key, protocol(s), and associated extensions.
    - S: Returns an HTTP 101. Switching protocols. (And server needs to support the proposed version.)
  - WebSocket Keys:
    - Client sends random string.
    - Server adds constant and hashes and base64 encodes.
    - Sends key back in accept header. Why header? Cache busting.
  = WebSocket Messages:
    - Text. Binary. Ping/pong. (Connection alive?) Close message.
    - Each message consists of one or more frames. 
    - All frames are binary. Header bits. What message type? Client-to-server messages are masked. Payload is the message content.
    - Frames instead of entire messages.
    ```csharp
      Microsoft.AspNetCore.WebSockets
    ```
    - Implement the low-level protocol. NOT Signal-R. NOTE: Via the socket: ws://localhost:60907/
    - NOTE: Via client degugging, use the "Frame" tab per the request.

- OVERVIEW OF ASP.NET CORE SIGNALR:
  - SignalR is an open source framework that wraps the complexity of real-time Web transports. SignalR & Transports.
  - ASP.NET Core SignalR Benefits:
    - Cross platform. Fast. Lightweight. Two (2) parts: Server side. Client side.
  - Transports:
    - Requires a client and server that supports transports. With a fallback mechanism.
    - SignalR will negotiate transport:
      - From long polling to SSE to Websockets.
    - RPC: Remote procedure call. 
      - Underlying transports. Serializing/deserializing complex types.
    - Hubs: A server-side class that sends messages to, and receives messages from, clients via RPC. A communication hub.
      - Calling a function on all clients, a specific client, or a group of clients. And a client can call a method on a hub. (e.g.: Chatroom.)
      - A hub protocol is a format used to serialize parameers to and deserialize parameters from.
        - JSON and MessagePack. (MessagePack is binary. Lighweight and easier to process.)
    - Differences between "classic" SignalR:
      - Simplified connection model.
      - Single hub per connection.
      - Async.
      - Binary & custom protocols.
      - No jQuery dependency for JavaScript client.
      - Sticky sessions are now required.
    - Scaling: (Running on multiple servers.)
      - Load balancer picks server.
      - Glorified "tunnel" between client and server. Ongoing & constant. Sticky.
      - Load balancer sets a cookie in the browser stating what server was used. (Long polling, SSE)
      - Web document? Servers share data via REDIS cache. SignalR supports REDIS cache.
    - Azure SignalR Service Recipe:


- WORKING WITH SIGNALR:
  - 