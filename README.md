# simple-chat-app (chatapp)

A minimal Spring Boot demo showing WebSocket usage with STOMP over SockJS and a simple Thymeleaf frontend.

This repository is a small learning/demo project to understand how to integrate WebSocket messaging into a Spring Boot application and build a simple real-time chat UI.

## Project Structure

- `src/main/java/com/chat/chatapp/` - main application package
  - `ChatappApplication.java` - Spring Boot application entry
  - `config/WebSocketConfig.java` - WebSocket / STOMP configuration (STOMP endpoint, application prefixes, message broker)
- `src/main/resources/templates/chat.html` - Thymeleaf template that hosts the chat UI and client-side JS (SockJS + STOMP)
- `pom.xml` - Maven build file (project Java version is managed via the `java.version` property)
- `mvnw`, `mvnw.cmd` - Maven wrapper for consistent builds

## Features

- STOMP-over-SockJS WebSocket endpoint (server-side) to receive chat messages and broadcast them to subscribers
- Simple UI using Thymeleaf and client-side STOMP/SockJS libraries (served from CDN in `chat.html`)
- Demo tests included under `src/test/java` (run with Maven)

## Prerequisites

- Java JDK 17 (this project was developed using Java 17). The `pom.xml` target is currently set to Java 17.
- Maven (optional if you use the included Maven wrapper `./mvnw`)
- Internet access for CDN dependencies in `chat.html` (or download the client libs locally)

If you want to upgrade to Java 21 (LTS), see the "Optional: Upgrade to Java 21" section below.

## Build & Run (recommended: use Maven wrapper)

Open a shell (PowerShell on Windows) and run:

```
cd 'e:\Projects\Spring\chap-app\chatapp'
.\mvnw clean package
.\mvnw spring-boot:run
```

Or run the packaged JAR directly (after `clean package`):

```
java -jar target\chatapp-0.0.1-SNAPSHOT.jar
```

After the app starts, open your browser at:

```
http://localhost:8080/
```

The chat UI (`chat.html`) will connect to the server-side STOMP endpoint and subscribe to messages.

## WebSocket endpoints (server-side)

- STOMP endpoint (SockJS fallback) — configured at `/chat` (client connects with `new SockJS('/chat')`).
- Application destination prefix — `/app` (client sends to `/app/chat`).
- Broadcast topic — `/topic/messages` (server sends chat messages here; clients subscribe to `/topic/messages`).

These paths are defined in `config/WebSocketConfig.java`.

## Frontend (client) notes

- Client code is in `src/main/resources/templates/chat.html`.
- It uses the SockJS and STOMP clients loaded from CDN. Ensure the page has access to those scripts.
- Important: the SockJS global constructor is `SockJS` (capital `JS`); using `SockJs` will produce `ReferenceError: SockJs is not defined`.
- The app expects the client to send chat messages to `/app/chat` and subscribes to `/topic/messages`.

## Tests

Run unit tests with:

```
.\mvnw test
```

## Troubleshooting

- "SockJs not defined" or `ReferenceError: SockJs is not defined` on the browser console:
  - Confirm `sockjs-client` script is loaded before your script (in `chat.html`).
  - Ensure you use `new SockJS('/chat')` (capitalization matters) and declare the `stompClient` variable before assigning to it.
- WebSocket connection issues:
  - Check server logs for broker connect failures.
  - Confirm the endpoint and destination prefixes match between client and server (`/chat`, `/app`, `/topic/messages`).
  - If using a reverse proxy, ensure WebSocket/SockJS support and correct proxy settings.

## Optional: Upgrade to Java 21 (LTS)

To upgrade the project to Java 21 locally:

1. Install JDK 21 and set `JAVA_HOME` (or call `java` from JDK21 full path).
2. Update the `pom.xml` property `java.version` to `21` and set the Maven Compiler plugin `release` to `21`.
   - Example edits (in `pom.xml`):

```
<properties>
  <java.version>21</java.version>
</properties>

<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-compiler-plugin</artifactId>
  <configuration>
    <release>21</release>
  </configuration>
</plugin>
```

3. Rebuild with the JDK 21 runtime: `java -version` should show 21, then run `./mvnw clean package`.

Note: Upgrading to a newer Java major release can expose compatibility problems in dependencies or code. For a full upgrade flow, consider creating a branch, running the build and tests under JDK 21, and addressing compile/runtime errors iteratively.

## Contributing / Educational use

This repo is intended as a learning/demo project. You can:

- Inspect `WebSocketConfig.java` to see how Spring configures STOMP endpoints and message brokers.
- Modify the client `chat.html` to integrate a richer UI (React/Vue) or to bundle STOMP/SockJS locally.

## Contact

If you have questions about this demo, open an issue in the repository or contact the author.

---
_Small demo project: WebSocket + STOMP + SockJS + Spring Boot + Thymeleaf_
