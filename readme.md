
## Demo Application

An example project to show live event streaming from Kafka topic to a react application via Server Sent Events and Websocket channels.

Steps to run the demo:
- Backend
    - `cd backend/docker` and `docker compose up` to run local kafka cluster
    - `cd backend` and `npm start` to run the backend.
- UI
    - `cd ui-streaming-charts` and `npm start`

The demo is then accessible at `localhost:3000` showing realtime events from `SSE` and `WS`.

## Polling Vs Long Polling Vs SSE and WebSockets

A number of options available when we want to display continuous stream of data in the client.
- Short Polling
- Long Polling
- WebSockets
- Server Sent Events

Client can poll the server to check if there is any new data available via either short polling or long polling.

Short polling is when client sends request in short intervals to the server, and the server responds with new data
if it is available or an empty response if none available. There is lot of overhead with this approach in terms of establishing connection, populating headers, security just to find out there is no data yet available from the server.


Long polling is when the server just waits until data is available or the request times out for the client. Once client receives the response, it immediately sends another request. The need to re-establish connection, security, headers means there is more bandwidth consumed although this is much better than short polling. There are issues with scaling and performance, as well as message ordering in which case we need to implement logic to keep track of last event sent to the client when the client has reconnected again. We have to reinvent the wheel to manage all this while all of this is handled as a primitive with SSE.


WebSockets are duplex channels, initiated through http request with an `upgrade` header which will establish WebSocket connection if the server supports it. There is no limitation on the number of connections, but the flip-side is servers can be overloaded easily unless we have mechanism in place restrict this is put in place. Since WebSocket is a different protocol, firewalls and proxies need additional configuration to allow the communication which may be against security policy in an enterprise set up where only http communication is allowed. It is useful for applications that need bi-directional communication for example chat and gaming applications.


SSE is native feature of HTTP with long lived connection, a special mime content-type of event stream and certain predefined format for
the messages to be sent to the clients. With this we can implement a publish subscribe model between the server and the web application. Problems that are automatically taken care of for us — clients and servers being made aware when connections drop out, server being able to continue with messages on reconnect since client last lost its connection and automatic multiplexing when using HTTP/2. The multiplexing support is not available in WebSockets.
For most of typical web applications, we only need unidirectional events from server, and SSE is a option since a lot of grunt work that we would need to implement ourselves to implement a reliable and scalable publish subscribe model over traditional http has already been done.