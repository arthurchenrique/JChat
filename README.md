# JChat

A multi-user chat application written in Java, using TCP sockets and threads.
One server accepts many clients at once, and every message is relayed to
everyone else in the room.

## Features
- Multiple clients connected to one server at the same time
- Unique usernames (duplicate names are rejected)
- Join and leave announcements
- `@username` pings: mentioned users get a direct notification
- `/quit` command for a clean disconnect
- Handles abrupt disconnects without crashing the server

## How it works
- **ChatServer** listens on port 3000 and accepts connections in a loop.
  Each new client is handed to its own thread.
- **ClientHandler** runs per client. It reads that client's messages and
  broadcasts them to everyone else. Connected clients are kept in a
  synchronized list so threads can add and remove users safely.
- **ChatClient** connects to the server and sends what you type.
- **ReadThread** runs in the background on the client so you can receive
  messages while typing.

## How to run
Requires Java 8 or newer.

```bash
javac *.java

# Terminal 1
java ChatServer

# Terminal 2, 3, ... (one per user)
java ChatClient
```

The client connects to `localhost:3000`. Enter a username when prompted
and start chatting. Type `/quit` to leave.

## What I learned
- How TCP client/server communication works with Java sockets
- Handling multiple clients with one thread per connection
- Thread safety: synchronizing the shared client list during broadcasts
- Cleaning up resources when a client disconnects, cleanly or not

## Known limitations / future work
- Messages are sent in plaintext (no TLS)
- No authentication, only a self-chosen username
- Server address and port are hardcoded
