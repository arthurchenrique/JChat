# JChat

A multi-room chat application written in Java, using TCP sockets and
threads. One server handles many clients at once, with chat rooms,
direct messages, @mention pings, and saved chat history.

## Features
- Multiple clients connected to one server at the same time
- Three chat rooms (`chat1`, `chat2`, `private`), chosen on login
- Unique usernames (duplicate names are rejected)
- Join and leave announcements per room
- Room history: new arrivals see earlier messages from that room
- Direct messages with `/dm username message`
- `@username` pings: the mentioned user gets a separate notification
- Messages and DMs logged to text files on the server
- `/quit` command for a clean disconnect
- Handles abrupt disconnects without crashing the server

## Commands
| Command | What it does |
|---|---|
| `/dm username message` | Send a private message to one user |
| `@username` in a message | Pings that user |
| `/quit` | Leave the chat |

## How it works
- **ChatServer** listens on port 3000 and accepts connections in a loop.
  Each new client gets its own thread. It keeps a synchronized list of
  all clients plus a map of rooms to the clients inside them, and
  handles broadcasting, DMs, and logging.
- **ClientHandler** runs once per client. It handles login, room
  selection, and the message loop, and cleans up when the client leaves.
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

The client connects to `localhost:3000`. Enter a username, pick a room,
and start chatting. The server creates its log files (`chat1.txt`,
`dm_*.txt`, and so on) in the folder where you run it.

## What I learned
- How TCP client/server communication works with Java sockets
- Handling multiple clients with one thread per connection
- Thread safety: synchronizing shared lists while broadcasting
- Organizing clients into rooms and routing messages to the right ones
- Reading and writing log files to persist chat history

## Known limitations / future work
- Messages are sent in plaintext (no TLS); I confirmed this by
  capturing the traffic in Wireshark
- No authentication, only a self-chosen username
- The `private` room has no access control, since anyone can join it
- Usernames aren't validated, and they're used to build log file names
- Log files are plain text on the server
- Server address and port are hardcoded

