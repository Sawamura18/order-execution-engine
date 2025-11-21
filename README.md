🚀 Order Execution Engine














A real-time order execution engine with DEX routing, WebSocket streaming, Redis queueing, and PostgreSQL persistence.
Built to simulate real Solana DEX routing pipelines (Raydium & Meteora) using mock execution.

📑 Table of Contents

✨ Features

🎯 Why Market Orders?

🔁 Order Lifecycle

📡 API

🔌 WebSocket Events

🧪 Tests

🛠 Tech Stack

🏗 Project Structure

🔧 Environment Variables

🚀 Deployment

📽 Demo Requirements

🏁 Conclusion

✨ Features
✅ Market Order Execution

Submit an order via REST

Receive orderId

WebSocket streams real-time updates

🛰 Mock DEX Router (Raydium & Meteora)

Simulated quote fetching

Random realistic price variations (2–5%)

Router chooses best venue

Executes mock swap with delay (2–3 sec)

Returns mock txHash + executed price

🔁 WebSocket Status Lifecycle
pending → routing → building → submitted → confirmed → (or failed)

🔥 BullMQ Queueing (Redis)

Up to 10 concurrent orders

Handles 100 orders/minute

Exponential backoff (3 retries)

Logs failures for post-mortem

🗄 PostgreSQL Persistence

Order storage

Execution details

Failure reasons

🎯 Why Market Orders?

✔ Simple to model deterministically
✔ Best suited for real-time streaming
✔ Clean fit for DEX routing logic

Extending to Other Order Types
Order Type	How to Support
Limit Order	Background price polling, execute when conditions meet
Sniper Order	Trigger on pool creation/token launch events

The underlying architecture supports both with minimal changes.

🔁 Order Lifecycle
graph TD
A[POST /api/orders/execute] --> B[Order saved & queued]
B --> C[WebSocket connection established]
C --> D{Queue Worker}
D -->|1| E[pending]
D -->|2| F[routing - comparing DEX prices]
D -->|3| G[building transaction]
D -->|4| H[submitted]
D -->|5| I[confirmed + txHash]
D -->|Error| J[failed + reason]

📡 API
POST /api/orders/execute

Submit a market order.

Request
{
  "tokenIn": "SOL",
  "tokenOut": "USDC",
  "amount": 1
}

Response
{
  "orderId": "cde1291af"
}


After receiving orderId, client switches to WebSocket.

🔌 WebSocket Events
Connect:
wss://<your-app>.onrender.com/ws?orderId=123

Sample Events
{ "status": "pending" }
{ "status": "routing", "bestVenue": "Raydium" }
{ "status": "building" }
{ "status": "submitted" }
{ "status": "confirmed", "txHash": "0xabc123" }

🧪 Tests

Includes or expects tests for:

DEX routing logic

Queue behavior

Retry attempts

WebSocket event order

Failure handling

Run tests:

npm test

🛠 Tech Stack
Component	Technology
Runtime	Node.js
Framework	Fastify
Language	TypeScript
Queue	BullMQ + Redis
Database	PostgreSQL
Realtime	WebSockets
Deployment	Render
🏗 Project Structure
/src
 ├── server.ts          # Fastify API + WebSockets
 ├── router/            # Mock DEX router (Raydium/Meteora)
 ├── queue/             # BullMQ worker logic
 ├── services/          # Order processing
 ├── db/                # Prisma/Postgres models
 ├── utils/             # Helpers (delay, txHash generator)

🔧 Environment Variables

Create a .env:

PORT=3000
DATABASE_URL=postgres://user:pass@host/db?sslmode=require
REDIS_URL=rediss://default:password@host:port
NODE_ENV=production

🚀 Deployment (Render)
Build
npm install

Start
npm run start

Remember:

Render WebSockets use:

wss://your-app.onrender.com

📽 Demo Requirements (for recruiters/interview)

Your video should show:

✔ Submit 3–5 orders
✔ Show all status updates (pending → confirmed)
✔ DEX routing choosing best price
✔ Queue processing multiple orders together
✔ Retry on failures
✔ Final txHash printed

🏁 Conclusion

This project demonstrates a production-ready backend architecture featuring:

Real-time WebSockets

Queue-based concurrent processing

DEX routing logic

Robust retry/error handling

Database persistence

The same infrastructure can easily be extended to real-world Solana execution using Raydium/Meteora SDKs.
