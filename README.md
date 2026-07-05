# Music Library Manager

A small client-server app for managing a music library — you can add, update, delete, and search for songs and albums. Built in Java with plain TCP sockets for the networking and Swing for the GUI.

## What it does

The server keeps track of songs and albums and handles requests from one or more clients at the same time. From the client, you can:

- Add, rename, or delete a song
- Add, update, delete, or search for an album
- Get the full list of songs

The main menu checks if the server is up before opening anything, so you get a clear "server not available" message instead of the app just hanging.

## How it works

It's a simple text-based protocol over sockets. The client sends a command like `ADD SONG:title:artist:duration`, and the server parses it, updates its internal data (just a `HashMap`/`HashSet` in memory — no database), and sends back a plain string response.

```
mainmenuclient (GUI)
   ├─ albumclient       →  ADD/UPDATE/DELETE ALBUM, SEARCH ALBUM
   ├─ clientmusic       →  ADD/UPDATE/DELETE SONG
   └─ searchalbumclient →  SEARCH ALBUM
             │
             ▼
       TCP socket (port 888)
             │
             ▼
     server.java (in-memory storage)
```

Full list of commands the server understands:

| Command | What it does |
|---|---|
| `ADD SONG:title:artist:duration` | Add a song |
| `UPDATE SONG:oldTitle:newTitle` | Rename a song |
| `DELETE SONG:title` | Remove a song |
| `GETSONGS` | Get all songs |
| `ADD ALBUM:title:description:genre:year:song` | Add an album |
| `UPDATE ALBUM:oldTitle:newTitle:description:genre:year:song` | Update an album |
| `DELETE ALBUM:title` | Remove an album |
| `SEARCH ALBUM:title` | Find an album by title |

## Project structure

```
JavaApplication37/
├── src/javaapplication37/
│   ├── Album.java              # immutable album model
│   ├── Song.java               # immutable song model
│   ├── server.java             # socket server, handles all the commands
│   ├── mainmenuclient.java     # client entry point / main menu
│   ├── albumclient.java        # album management window
│   ├── clientmusic.java        # song management window
│   └── searchalbumclient.java  # album search window
├── build.xml
└── nbproject/                  # NetBeans project files
```

## Running it

You just need a JDK (8+). Compile everything first:

```bash
cd src
javac -d ../build/classes javaapplication37/*.java
```

Then start the server:

```bash
java -cp build/classes javaapplication37.server
```

This also opens the client's main menu automatically. If you want to run additional clients (e.g. to simulate multiple users), just run:

```bash
java -cp build/classes javaapplication37.mainmenuclient
```

From there, click **Album** or **Music** to manage each part of the library.

## Known limitations

A few things I'd fix given more time:

- Data isn't saved anywhere — restart the server and the library is empty again.
- The protocol is just colon-separated strings, so a title containing a colon would break the parsing.
- The server spins up a client GUI window itself on startup, which isn't great practice — the server and client should really be fully separate processes.
- No real input validation yet (e.g. a negative year just gets printed to the console instead of being rejected properly).

## Author

Zacharias Kokkinakis
