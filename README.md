# 🚗 FlowRide — AI-Powered Ride Sharing Platform

A production-ready ride-sharing platform similar to Uber/Rapido, built with modern technologies and AI-powered features.

![Next.js](https://img.shields.io/badge/Next.js_15-000?style=flat&logo=nextdotjs)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat&logo=typescript&logoColor=white)
![Socket.io](https://img.shields.io/badge/Socket.io-010101?style=flat&logo=socketdotio)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=flat&logo=fastapi&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-336791?style=flat&logo=postgresql&logoColor=white)

## 🧰 Tech Stack

| Layer | Technology |
|-------|-----------|
| **Frontend** | Next.js 15, TypeScript, TailwindCSS, MapLibre GL, Zustand, Socket.io Client |
| **Backend** | Node.js, Express.js, TypeScript, Socket.io, Prisma ORM |
| **Database** | PostgreSQL (Neon) with PostGIS |
| **AI Service** | Python, FastAPI, Scikit-learn |
| **Auth** | JWT (custom implementation) |

## 🏗 Architecture

```
Next.js Frontend (Port 3000)
      │
 REST + WebSocket
      │
Express.js Backend (Port 5000)
      │
PostgreSQL (Neon) ←→ AI Service (FastAPI, Port 8000)

Live Tracking:
Driver → GPS → Socket.io → Backend → Rider
```

## 🔑 API Keys & Secrets

To run FlowRide, you need to configure the following in `backend/.env`:

1.  **`DATABASE_URL`**: Get this from your **Neon.tech** dashboard (Serverless PostgreSQL). It looks like `postgresql://user:pass@host.neon.tech/flowride?sslmode=require`.
2.  **`JWT_SECRET`**: Any long, random string. This is used to sign and verify user session tokens.
3.  **`MAP_STYLE_URL`**: (Optional) If you want a custom map (e.g., from MapTiler or Mapbox). By default, we use a public CartoDB style.

## 📡 Driver & Rider Logic

- **Who chooses?** In this project, the **Rider** initiates a request. Nearby **Drivers** are notified via WebSocket and they **choose** to accept the trip from their dashboard. The first driver to accept is matched.
- **Vehicle Types**: The project supports:
    - **2-Wheelers**: Bike
    - **3-Wheelers**: Auto (Can be disabled if requested)
    - **4-Wheelers**: Sedan, SUV

## 📁 Project Structure

```
FlowRide/
├── frontend/          # Next.js 15 App Router
│   ├── src/app/       # Pages (landing, auth, rider, driver)
│   ├── src/store/     # Zustand stores (auth, booking, tracking)
│   └── src/lib/       # API client, Socket.io helper
│
├── backend/           # Express.js API Server
│   ├── src/routes/    # REST endpoints
│   ├── src/websocket/ # Socket.io events
│   ├── src/middleware/ # Auth, error handling
│   └── prisma/        # Schema, seed data
│
├── ai-service/        # FastAPI ML Service
│   ├── routers/       # Prediction endpoints
│   └── main.py        # App entry
│
└── shared/            # Shared TypeScript types
    └── types/
```

## 🚀 Quick Start

### Prerequisites
- Node.js 18+
- Python 3.10+
- PostgreSQL (or Neon account)

### 1. Clone & Install

```bash
# Install backend deps
cd backend && npm install

# Install frontend deps
cd ../frontend && npm install

#### 3. AI Service (Python)
```bash
cd ai-service
python -m venv venv
.\venv\Scripts\activate  # Windows
source venv/bin/activate  # Linux/Mac
pip install -r requirements.txt
uvicorn main:app --reload --port 8000
```

### 2. Configure Environment

```bash
# Copy env templates
cp backend/.env.example backend/.env
cp frontend/.env.example frontend/.env.local

# Edit backend/.env with your DATABASE_URL
```

### 3. Setup Database

```bash
cd backend
npx prisma db push      # Create tables
npx prisma db seed      # Seed demo data
```

### 4. Run All Services

Open 3 terminals:

```bash
# Terminal 1: Backend
cd backend && npm run dev

# Terminal 2: Frontend
cd frontend && npm run dev

# Terminal 3: AI Service
cd ai-service && uvicorn main:app --reload --port 8000
```

### 5. Open the App

Visit **http://localhost:3000**

**Demo Credentials:**
- Rider: `arjun@flowride.com` / `password123`
- Driver: `vikram@flowride.com` / `password123`

## ⚡ Features

- **Rider Booking Interface** — Interactive MapLibre map, click-to-place origin/destination, vehicle type selection, fare estimation
- **Driver Dashboard** — Online/offline toggle, incoming ride requests, active ride management, earnings stats
- **Real-time GPS Tracking** — Drivers send GPS every 2 seconds via WebSocket, riders see live map updates
- **AI-Powered Predictions** — ETA prediction, dynamic pricing with surge, zone demand prediction
- **Ride Lifecycle** — Request → Match → Accept → In-Progress → Complete → Pay → Rate
- **Payment Simulation** — Simulated card/UPI payments with transaction IDs
- **Star Ratings** — Post-ride rating and review system

## 🗄 Database Tables

| Table | Description |
|-------|------------|
| `users` | All users (riders + drivers) |
| `drivers` | Driver profiles with license, rating |
| `vehicles` | Vehicle details per driver |
| `rides` | Ride records with status, route, fare |
| `ride_requests` | Pending ride requests |
| `driver_locations` | Real-time GPS positions |
| `payments` | Payment transactions |
| `ratings` | Ride ratings and reviews |

## 📡 API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/register` | Register user |
| POST | `/api/auth/login` | Login |
| GET | `/api/auth/me` | Get current user |
| POST | `/api/rides/request` | Request a ride |
| POST | `/api/rides/estimate` | Get fare estimate |
| GET | `/api/rides` | Ride history |
| PATCH | `/api/rides/:id/status` | Update ride status |
| GET | `/api/drivers/nearby` | Find nearby drivers |
| PATCH | `/api/drivers/availability` | Toggle availability |
| POST | `/api/payments` | Process payment |
| POST | `/api/ratings` | Submit rating |

## 📡 WebSocket Events

| Event | Direction | Description |
|-------|-----------|-------------|
| `driver-location` | Driver → Server | GPS coordinates |
| `location-update` | Server → Rider | Driver position broadcast |
| `ride-request` | Server → Driver | New ride request |
| `ride-accepted` | Bidirectional | Driver accepted ride |
| `ride-started` | Bidirectional | Ride in progress |
| `ride-completed` | Bidirectional | Ride finished |

## 🌍 Deployment

| Service | Platform | Notes |
|---------|----------|-------|
| Frontend | **Vercel** | Native Next.js support, auto CI/CD |
| Backend | **Render** | Free Node.js hosting, WebSocket support |
| AI Service | **Render** | Python/FastAPI deployment |
| Database | **Neon** | Serverless PostgreSQL, 3GB free |

## 📄 License

MIT
