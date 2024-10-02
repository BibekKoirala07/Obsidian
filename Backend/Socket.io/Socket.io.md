Socket.io is a 
- bi-directional
- low-latency
- event-based communication package

[[ https://socket.io/docs/v4/ | socket-docs]]

There are two two variable `io` and `socket` in socket.io. 
### What is `socket` and `io` in socket.io
`io` means Server-side object, represents the entire Socket.IO server instance. `socket` Represents an individual client connection, exists on both server and client sides.
Each `socket` represents a unique connection between a client and the server

| `io`                                                                 | `socket`                                      |
| -------------------------------------------------------------------- | --------------------------------------------- |
| server-side object it represent the entire socket.io server instance | it represent the individual client connection |
| exists on server only                                                | exist on server and client both               |
| lifetime exist for entire server duration                            | only for time for which client exist.         |

### Setting up Socket.io
> [!tips] create and listen an event
> `.on()` always listen to the event. `.emit()` creates the event. it happens in both client and server
#### Server
```javascript

const { Server } = require("socket.io");

const express = require("express");
const app = express();
const PORT = 3000;

const http = require("http");
const server = http.createServer(app);
const io = new Server(server, {
  cors: {
    origin: "http://localhost:5173",
    methods: ["GET", "POST"],
    credentials: true,
  },
});

io.on('connection', (socket) => { 
	console.log('New client connected')
})

// Connecting to Database
connectDB(process.env.MONGO_URI)
  .then(() => {
    server.listen(PORT, () =>
      console.log(`Example app listening on port ${PORT}!`)
    );
  })
  .catch((error) => {
    console.log("Error while connecting to database", error);
  });
```
#### Client
Make sure that multiple socket connection aren't being formed for same user. To do that we need to connect socket right after user goes to he enters the website. However, there is dichotomy there. socket connection drain huge amount of battery. and make sure the component isn't rendered multiple times. it just becomes pain in the ass if more than one connection is being established 
```javascript
import { io, Socket } from "socket.io-client";

const Component = () => {
	const [socket, setSocket] = useState<Socket | null>();

	useEffect(() => {
    const newSocket = io("http://localhost:3000");
    setSocket(newSocket);

    newSocket.on("connect", () => {
      console.log("connected to server");
    });

    return () => {
      newSocket.disconnect();
    };
  }, [_id]);
}

```

It can help the server to send information to client whenever it wants like in chat app.

1. on in `socket.on` or `on` catches the event in both frontend and backend
2. There are several server events like messages, connection, disconnect, ping, join, leave, reconnect, etc. and there are several client events like connect, reconnect, connect_error, connect_timeout, etc.
3. There can be made custom Event also. there are two ways to this. Create Custom event on server side and catch on client side and vice versa.
### Custom Event
#### create event in server or server
```

socket.emit("customEvent", {data: something}) // always starts with emit if you want to create a event on both sides. it means we have committed

```
#### catch event in client or server
```
socket.on("customeEvent", function(data) {
	console.log("data)
})
```
### Broad-Casting
Here is the example in server
```

```

Now in Client
```

```

### Namespaces
1. By default, it is `/` but we can change that by using below example and we have change it in frontend too     `var socket = io("/custom-namespace")`
```
var = io.of("/custome-namespace")
```

### Rooms
In every namespace, we can create or define multiple channels. those channels are ==rooms==.
#### create a Simple Rooms
```javascript
var roomOne = 1;

io.on("connection", function(socket) {
	socket.join("room-" + roomOne) // joins in             room no roomOne

	io.sockets.in("room-" + roomOne).emit("connectedRoom", "you are connected to room no." + roomOne)

})

// In client side
var socket = io()
socket.on("connectedRoom", function(data) {
	console.log("data)
})
```
### Error Handing
it needs to be done only on Client side as in server there isn't that much logic
```javascript
var socket = io()
socket.on("connect_error", function() {
	alert("Sorry, Your connection failed")
})
```
there are many event in client 

### Multiple Server for one Client
We can run multiple backends for 
