# 42. TypeScript in Real-time Applications (WebSockets, etc.)

Real-time applications, such as chat apps, live dashboards, collaborative tools, and online games, require persistent, low-latency communication between clients and servers. TypeScript can greatly enhance the development of these applications by providing type safety for messages, event names, and state management logic.

This tutorial explores how to build real-time applications using TypeScript with technologies like WebSockets, Server-Sent Events (SSE), and popular libraries.

## Table of Contents
1.  [Introduction to Real-time Communication](#intro-real-time)
    *   WebSockets: Bidirectional, full-duplex communication.
    *   Server-Sent Events (SSE): Unidirectional communication from server to client.
    *   Long Polling (brief mention as an older alternative).
2.  [Why TypeScript for Real-time Applications?](#why-ts-real-time)
    *   Type-safe message payloads.
    *   Clearly defined event contracts.
    *   Improved reliability and maintainability.
    *   Easier refactoring of communication protocols.
3.  [WebSockets with TypeScript](#websockets-ts)
    *   [**Server-Side (Node.js with `ws` library)**](#websockets-server-ts)
        *   Setting up a WebSocket server.
        *   Defining message types (interfaces/types for payloads).
        *   Handling connections, messages, errors, and disconnections type-safely.
        *   Broadcasting messages to clients.
    *   [**Client-Side (Browser's WebSocket API)**](#websockets-client-ts)
        *   Connecting to a WebSocket server.
        *   Sending and receiving typed messages.
        *   Handling `onopen`, `onmessage`, `onerror`, `onclose` events.
4.  [Server-Sent Events (SSE) with TypeScript](#sse-ts)
    *   [**Server-Side Implementation**](#sse-server-ts)
        *   Setting up an SSE endpoint (e.g., with Express).
        *   Sending typed events to clients.
    *   [**Client-Side (`EventSource` API)**](#sse-client-ts)
        *   Connecting to an SSE endpoint.
        *   Listening to named events and handling typed data.
5.  [Popular Libraries for Real-time Communication with TypeScript](#real-time-libraries-ts)
    *   [**Socket.IO**](#socketio-ts)
        *   Features: fallback mechanisms, rooms, namespaces.
        *   Server-side setup with TypeScript.
        *   Client-side setup with TypeScript.
        *   Defining and using typed events and payloads.
    *   [**GraphQL Subscriptions**](#graphql-subscriptions-ts)
        *   Real-time updates for GraphQL data (e.g., with Apollo Server/Client).
        *   Generating types for subscription payloads.
    *   Others (e.g., `µWebSockets.js` for high performance, `SignalR` client for .NET backends).
6.  [Defining a Type-Safe Communication Protocol](#type-safe-protocol-ts)
    *   Using enums for event names/message types.
    *   Shared interfaces/types for message payloads (e.g., in a shared monorepo package).
    *   Versioning communication protocols.
7.  [State Management in Real-time TypeScript Apps](#state-management-real-time-ts)
    *   Updating client-side state based on real-time messages.
    *   Integrating with state management libraries (Redux, Zustand, etc.) type-safely.
8.  [Authentication and Authorization](#auth-real-time-ts)
    *   Authenticating WebSocket connections (e.g., via tokens).
    *   Authorizing messages and actions in real-time.
9.  [Scaling Real-time Applications](#scaling-real-time-ts)
    *   Horizontal scaling considerations (sticky sessions, backplanes like Redis pub/sub).
    *   Load balancing WebSockets.
10. [Testing Real-time Components with TypeScript](#testing-real-time-ts)
    *   Mocking WebSocket servers/clients.
    *   Testing message flows and event handling.
11. [Best Practices](#best-practices-real-time-ts)
12. [Key Takeaways](#key-takeaways-ts-real-time)

## 1. Introduction to Real-time Communication <a name="intro-real-time"></a>
*   **WebSockets:** Provide a persistent, bidirectional communication channel over a single TCP connection. Ideal for chat, gaming, and collaborative applications.
*   **Server-Sent Events (SSE):** A simpler, unidirectional protocol where the server pushes updates to the client over an HTTP connection. Good for live feeds, notifications, etc.

## 2. Why TypeScript for Real-time Applications? <a name="why-ts-real-time"></a>
*   **Type-Safe Payloads:** Ensures that data sent and received matches expected structures, reducing runtime errors.
*   **Clear Contracts:** Explicitly define event names and message shapes, making the communication protocol understandable and maintainable.
*   **Reliability:** Catch inconsistencies between client and server logic at compile time.

## 3. WebSockets with TypeScript <a name="websockets-ts"></a>

### Server-Side (Node.js with `ws` library) <a name="websockets-server-ts"></a>
Install `ws` and its types:
```bash
npm install ws
npm install -D @types/ws
```
```typescript
// server.ts
import WebSocket, { WebSocketServer } from 'ws';

// Define message types
interface ChatMessage {
  type: 'chat';
  sender: string;
  content: string;
  timestamp: number;
}

interface UserStatusMessage {
  type: 'status';
  userId: string;
  isOnline: boolean;
}

type AppMessage = ChatMessage | UserStatusMessage;

const wss = new WebSocketServer({ port: 8080 });

console.log('WebSocket server started on port 8080');

wss.on('connection', (ws: WebSocket) => {
  console.log('Client connected');

  ws.on('message', (message: WebSocket.RawData) => {
    try {
      const parsedMessage: AppMessage = JSON.parse(message.toString());
      console.log('Received:', parsedMessage);

      // Handle different message types
      switch (parsedMessage.type) {
        case 'chat':
          // Broadcast chat message to all clients (except sender if needed)
          wss.clients.forEach(client => {
            if (client !== ws && client.readyState === WebSocket.OPEN) {
              client.send(JSON.stringify(parsedMessage));
            }
          });
          break;
        case 'status':
          // Handle status update
          break;
      }
    } catch (error) {
      console.error('Failed to parse message or invalid message structure:', error);
      ws.send(JSON.stringify({ type: 'error', message: 'Invalid message format' }));
    }
  });

  ws.on('close', () => {
    console.log('Client disconnected');
  });

  ws.on('error', (error) => {
    console.error('WebSocket error:', error);
  });

  // Send a welcome message
  ws.send(JSON.stringify({ type: 'info', message: 'Welcome to the WebSocket server!' }));
});
```

### Client-Side (Browser's WebSocket API) <a name="websockets-client-ts"></a>
```typescript
// client.ts (using the same AppMessage types from server)
// Assume AppMessage, ChatMessage, UserStatusMessage types are available (e.g., from a shared file)

const socket = new WebSocket('ws://localhost:8080');

socket.onopen = () => {
  console.log('Connected to WebSocket server');
  const statusMessage: UserStatusMessage = {
    type: 'status',
    userId: 'user123',
    isOnline: true,
  };
  socket.send(JSON.stringify(statusMessage));
};

socket.onmessage = (event: MessageEvent) => {
  try {
    const message: AppMessage = JSON.parse(event.data as string);
    console.log('Received from server:', message);

    switch (message.type) {
      case 'chat':
        console.log(`Chat from ${message.sender}: ${message.content}`);
        // Update UI with chat message
        break;
      case 'status':
        console.log(`User ${message.userId} is ${message.isOnline ? 'online' : 'offline'}`);
        // Update UI with user status
        break;
      // Handle other message types like 'info' or 'error'
    }
  } catch (error) {
    console.error('Error parsing message from server:', error);
  }
};

socket.onclose = (event: CloseEvent) => {
  console.log('Disconnected from WebSocket server:', event.reason, event.code);
};

socket.onerror = (event: Event) => {
  console.error('WebSocket error:', event);
};

function sendChatMessage(content: string) {
  if (socket.readyState === WebSocket.OPEN) {
    const chatMsg: ChatMessage = {
      type: 'chat',
      sender: 'clientUser', // Should be dynamic
      content,
      timestamp: Date.now(),
    };
    socket.send(JSON.stringify(chatMsg));
  }
}
```

## 4. Server-Sent Events (SSE) with TypeScript <a name="sse-ts"></a>

### Server-Side Implementation <a name="sse-server-ts"></a>
```typescript
// sseServer.ts (using Express for simplicity)
import express, { Request, Response } from 'express';

const app = express();
const PORT = 3000;

interface StockUpdateEvent {
  symbol: string;
  price: number;
  timestamp: number;
}

app.get('/sse/stock-updates', (req: Request, res: Response) => {
  res.setHeader('Content-Type', 'text/event-stream');
  res.setHeader('Cache-Control', 'no-cache');
  res.setHeader('Connection', 'keep-alive');
  res.flushHeaders(); // Flush the headers to establish the connection

  const sendStockUpdate = () => {
    const update: StockUpdateEvent = {
      symbol: 'TSLA',
      price: Math.random() * 100 + 200,
      timestamp: Date.now(),
    };
    // SSE format: data: <json_string>\n\n
    // Can also specify event name: event: <event_name>\ndata: <json_string>\n\n
    res.write(`data: ${JSON.stringify(update)}\n\n`);
  };

  const intervalId = setInterval(sendStockUpdate, 2000);

  req.on('close', () => {
    clearInterval(intervalId);
    res.end();
    console.log('Client disconnected from SSE');
  });
});

app.listen(PORT, () => console.log(`SSE server started on port ${PORT}`));
```

### Client-Side (`EventSource` API) <a name="sse-client-ts"></a>
```typescript
// sseClient.ts
// Assuming StockUpdateEvent type is available

const eventSource = new EventSource('/sse/stock-updates');

eventSource.onopen = () => {
  console.log('Connected to SSE stream');
};

// Default 'message' event
eventSource.onmessage = (event: MessageEvent) => {
  try {
    const stockUpdate: StockUpdateEvent = JSON.parse(event.data as string);
    console.log('Stock Update (default event):', stockUpdate);
    // Update UI
  } catch (error) {
    console.error('Error parsing SSE data:', error);
  }
};

// Listening to custom named events (if server sends them with 'event: <name>')
// eventSource.addEventListener('customEventName', (event: MessageEvent) => {
//   const customData = JSON.parse(event.data as string);
//   console.log('Custom Event:', customData);
// });

eventSource.onerror = (error: Event) => {
  console.error('SSE Error:', error);
  // EventSource will attempt to reconnect automatically on some errors
  // If readyState is EventSource.CLOSED, it won't reconnect.
  if (eventSource.readyState === EventSource.CLOSED) {
    console.log('SSE connection closed permanently.');
  }
};
```

## 5. Popular Libraries for Real-time Communication with TypeScript <a name="real-time-libraries-ts"></a>

### Socket.IO <a name="socketio-ts"></a>
Socket.IO provides features like automatic reconnection, rooms, namespaces, and fallbacks to long polling if WebSockets aren't available.
```typescript
// Socket.IO Server Example (simplified)
// npm install socket.io @types/socket.io
import { Server, Socket } from 'socket.io';

interface ClientToServerEvents {
  chatMessage: (msg: { sender: string; content: string }) => void;
}
interface ServerToClientEvents {
  newChatMessage: (msg: { sender: string; content: string; timestamp: number }) => void;
  userConnected: (userId: string) => void;
}
interface InterServerEvents {}
interface SocketData {
  userId: string;
}

const io = new Server<ClientToServerEvents, ServerToClientEvents, InterServerEvents, SocketData>(3001, {
  cors: { origin: '*' } // Configure CORS as needed
});

io.on('connection', (socket: Socket<ClientToServerEvents, ServerToClientEvents, InterServerEvents, SocketData>) => {
  console.log('Socket.IO client connected:', socket.id);
  socket.data.userId = `user_${socket.id}`;
  socket.emit('userConnected', socket.data.userId);

  socket.on('chatMessage', (msg) => {
    io.emit('newChatMessage', { ...msg, timestamp: Date.now() });
  });

  socket.on('disconnect', () => {
    console.log('Socket.IO client disconnected:', socket.id);
  });
});

// Socket.IO Client Example
// npm install socket.io-client
import { io as ioc, Socket as ClientSocket } from 'socket.io-client';

const clientSocket: ClientSocket<ServerToClientEvents, ClientToServerEvents> = ioc('http://localhost:3001');

clientSocket.on('connect', () => console.log('Connected to Socket.IO server'));
clientSocket.on('newChatMessage', (msg) => console.log('New message:', msg));
clientSocket.on('userConnected', (userId) => console.log('User connected:', userId));

// To send a message:
// clientSocket.emit('chatMessage', { sender: 'ClientUser', content: 'Hello from client!' });
```

### GraphQL Subscriptions <a name="graphql-subscriptions-ts"></a>
Provide real-time updates for GraphQL queries. Typically implemented using WebSockets. See Tutorial #30 (TypeScript with GraphQL) for more on GraphQL.

## 6. Defining a Type-Safe Communication Protocol <a name="type-safe-protocol-ts"></a>
*   Share type definitions (interfaces, enums for event names/message types) between client and server, often via a shared package in a monorepo.
*   This ensures both sides adhere to the same contract, and changes are caught at compile time.

## 7. State Management in Real-time TypeScript Apps <a name="state-management-real-time-ts"></a>
When real-time messages arrive, client-side state needs to be updated. Ensure these updates are type-safe and integrate smoothly with your chosen state management solution (Redux, Zustand, Vuex, etc.).

## 8. Authentication and Authorization <a name="auth-real-time-ts"></a>
*   **Authentication:** WebSocket connections can be authenticated by passing a token (e.g., JWT) during the initial handshake (e.g., in query parameters or custom headers if supported by the server setup).
*   **Authorization:** Once authenticated, the server should authorize actions or messages based on the user's identity and permissions.

## 9. Scaling Real-time Applications <a name="scaling-real-time-ts"></a>
*   When scaling horizontally (multiple server instances), WebSocket connections are stateful. Solutions include:
    *   **Sticky Sessions:** Route a client to the same server instance.
    *   **Backplane:** Use a message broker (e.g., Redis Pub/Sub) to allow servers to broadcast messages to clients connected to other instances.

## 10. Testing Real-time Components with TypeScript <a name="testing-real-time-ts"></a>
*   Use libraries like `ws` (for server testing) or mock WebSocket client/server implementations.
*   For Socket.IO, there are testing utilities or you can mock the client/server behavior.
*   Focus on testing message emission, reception, and the resulting state changes or side effects.

## 11. Best Practices <a name="best-practices-real-time-ts"></a>
*   Define clear, typed contracts for all messages and events.
*   Handle errors gracefully on both client and server (connection issues, invalid messages).
*   Implement robust reconnection logic on the client.
*   Be mindful of message size and frequency to avoid performance bottlenecks.
*   Secure your real-time endpoints properly.

## 12. Key Takeaways <a name="key-takeaways-ts-real-time"></a>
*   TypeScript significantly improves the development of real-time applications by enforcing type safety on messages and event handlers.
*   WebSockets offer bidirectional communication, while SSE is suitable for server-to-client pushes.
*   Libraries like Socket.IO provide additional features and fallbacks over raw WebSockets, with good TypeScript support.
*   Sharing type definitions between client and server is crucial for maintaining a robust communication protocol.

By leveraging TypeScript's strong typing, you can build more reliable, maintainable, and scalable real-time applications.

---

⬅️ [Back to TypeScript with Serverless Architectures](../41-ts-serverless/README.md) | ➡️ [Next: Advanced TypeScript with Deno](../43-ts-deno-advanced/README.md) *(Placeholder)*
