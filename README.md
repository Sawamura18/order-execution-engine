# 🚀 Order Execution Engine  
![Node](https://img.shields.io/badge/Node.js-18+-green)  
![Fastify](https://img.shields.io/badge/Fastify-Framework-black)  
![TypeScript](https://img.shields.io/badge/TypeScript-✓-blue)  
![BullMQ](https://img.shields.io/badge/BullMQ-Queue-red)  
![Redis](https://img.shields.io/badge/Redis-Cache-orange)  
![PostgreSQL](https://img.shields.io/badge/Postgres-Database-blue)  
![WebSockets](https://img.shields.io/badge/WebSockets-RealTime-purple)

A real-time **order execution engine** with DEX routing, WebSocket streaming, Redis queueing, and PostgreSQL persistence.  
Built to simulate real Solana DEX routing pipelines (Raydium & Meteora) using mock execution.
Hosted at: https://order-execution-engine-1bnm.onrender.com/

---

## 📑 Table of Contents
- [✨ Features](#-features)
- [🎯 Why Market Orders?](#-why-market-orders)
- [🔁 Order Lifecycle](#-order-lifecycle)
- [📡 API](#-api)
- [🔌 WebSocket Events](#-websocket-events)
- [🧪 Tests](#-tests)
- [🛠 Tech Stack](#-tech-stack)
- [🏗 Project Structure](#-project-structure)
- [🔧 Environment Variables](#-environment-variables)
- [🚀 Deployment](#-deployment)
- [📽 Demo Requirements](#-demo-requirements)
- [🏁 Conclusion](#-conclusion)

---

## ✨ Features

### ✅ Market Order Execution
- Submit an order via REST  
- Receive `orderId`  
- WebSocket streams real-time updates

### 🛰 Mock DEX Router (Raydium & Meteora)
- Simulated quote fetching  
- Random realistic price variations (2–5%)  
- Router chooses best venue  
- Executes mock swap with delay (2–3 sec)  
- Returns mock txHash + executed price

### 🔁 WebSocket Status Lifecycle

```
pending → routing → building → submitted → confirmed → (or failed)
```

### 🔥 BullMQ Queueing (Redis)
- Up to **10 concurrent orders**  
- Handles **100 orders/minute**  
- **Exponential backoff** (3 retries)  
- Logs failures for post-mortem

### 🗄 PostgreSQL Persistence
- Order storage  
- Execution details  
- Failure reasons  

---

## 🎯 Why Market Orders?

✔ Simple to model deterministically  
✔ Best suited for real-time streaming  
✔ Clean fit for DEX routing logic  

### Extending to Other Order Types
| Order Type | How to Support |
|-----------|----------------|
| **Limit Order** | Background price polling, execute when conditions meet |
| **Sniper Order** | Trigger on pool creation/token launch events |

The underlying architecture supports both with minimal changes.

---

## 🔁 Order Lifecycle

```mermaid
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
```

---

## 📡 API

### **POST /api/orders/execute**
Submit a market order.

#### Request
```json
{
  "tokenIn": "SOL",
  "tokenOut": "USDC",
  "amount": 1
}
```

#### Response
```json
{
  "orderId": "cde1291af"
}
```

After receiving `orderId`, client switches to WebSocket.

---

## 🔌 WebSocket Events

### Connect:
```
wss://<your-app>.onrender.com/ws?orderId=123
```

### Sample Events
```json
{ "status": "pending" }
{ "status": "routing", "bestVenue": "Raydium" }
{ "status": "building" }
{ "status": "submitted" }
{ "status": "confirmed", "txHash": "0xabc123" }
```

---

## 🧪 Tests
Includes or expects tests for:
- DEX routing logic  
- Queue behavior  
- Retry attempts  
- WebSocket event order  
- Failure handling  

Run tests:
```bash
npm test
```

---

## 🛠 Tech Stack

| Component | Technology |
|----------|------------|
| Runtime | Node.js |
| Framework | Fastify |
| Language | TypeScript |
| Queue | BullMQ + Redis |
| Database | PostgreSQL |
| Realtime | WebSockets |
| Deployment | Render |

---

## 🏗 Project Structure

```
/src
 ├── server.ts          # Fastify API + WebSockets
 ├── router/            # Mock DEX router (Raydium/Meteora)
 ├── queue/             # BullMQ worker logic
 ├── services/          # Order processing
 ├── db/                # Prisma/Postgres models
 ├── utils/             # Helpers (delay, txHash generator)
```

---

## 🔧 Environment Variables

Create a `.env`:

```env
PORT=3000
DATABASE_URL=postgres://user:pass@neon-host/db?sslmode=require
REDIS_URL=rediss://default:password@redis-host:port
NODE_ENV=production
```

---

## 🚀 Deployment (Render)

### Build
```bash
npm install
```

### Start
```bash
npm run start
```

### Remember:
Render WebSockets use:
```
wss://your-app.onrender.com
```

---

## 📽 Demo Requirements

Your video should show:

✔ Submit 3–5 orders  
✔ Show all status updates (`pending → confirmed`)  
✔ DEX routing logs  
✔ Queue processing many orders  
✔ Retry logic  
✔ Returned txHash  

---

## 🏁 Conclusion

This project demonstrates a production-ready backend architecture featuring:

- Real-time WebSockets  
- Queue-based concurrent processing  
- DEX routing  
- Robust retry/error handling  
- Database persistence  

Ideal foundation for extending to **real Solana devnet** routing.

