# IMS PRO - Inventory & Order Management System

A production-ready Inventory & Order Management System built with a FastAPI backend, React (Vite + TS) frontend, and PostgreSQL database.

---

## Features
- **Product Management (CRUD)**: Stock levels, SKU validation, price constraints, pagination, search, sorting, and price/stock filters.
- **Customer Management (CRUD)**: Client register, unique email validation, and client record updates.
- **Order Management**: Multi-item orders, atomic database transactions (rollback on item error), real-time stock availability check, automatic stock reduction.
- **Interactive Dashboard**: Total revenue, order logs, low stock indicators, and registered metric counters.
- **Dockerized Architecture**: Containers for frontend (Nginx), backend (FastAPI), and database (Postgres).

---

## Directory Structure

```text
inventory-management/
├── backend/
│   ├── app/
│   │   ├── api/             # API Router & Endpoints
│   │   ├── core/            # Configuration & Settings
│   │   ├── db/              # Session Management & Migrations
│   │   ├── models/          # SQLAlchemy Models
│   │   ├── repositories/    # Database Repository Layer
│   │   ├── schemas/         # Pydantic Schemas
│   │   ├── services/        # Business Logic & Workflows
│   │   └── main.py          # App Entrance & Handlers
│   ├── tests/               # Pytest Unit & Integration Tests
│   ├── Dockerfile
│   ├── render.yaml          # Render Infrastructure Blueprint
│   └── requirements.txt
├── frontend/
│   ├── src/
│   │   ├── components/      # Reusable UI shells (Sidebar, Modal, Card)
│   │   ├── pages/           # App Pages (Dashboard, Products, Customers, Orders)
│   │   ├── services/        # API calls (Axios + React Query)
│   │   ├── types/           # TS Interfaces
│   │   ├── App.tsx          # Router mapping
│   │   └── main.tsx
│   ├── Dockerfile
│   ├── nginx.conf           # SPA Routing Config
│   ├── tailwind.config.js
│   └── vercel.json          # Vercel Deployment Config
└── docker-compose.yml
```

---

## Getting Started: Docker Compose (Recommended)

To run the entire system (Frontend, Backend, and Database) with a single command:

1. **Pre-requisites**: Ensure you have [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed.
2. **Build and Start**:
   ```bash
   docker compose up --build
   ```
3. **Access the Apps**:
   - **Frontend**: [http://localhost:3000](http://localhost:3000)
   - **Backend API Docs**: [http://localhost:8000/docs](http://localhost:8000/docs)
   - **Database**: Port `5432` on `localhost`

---

## Local Development Setup

If you prefer to run services locally without Docker:

### 1. Database Setup
Create a PostgreSQL database named `inventory_db`. You can use a local PostgreSQL server or a cloud database like Neon.

### 2. Backend Setup
1. Navigate to the backend directory:
   ```bash
   cd backend
   ```
2. Create and activate a virtual environment:
   ```bash
   python -m venv .venv
   # On Windows:
   .venv\Scripts\activate
   # On macOS/Linux:
   source .venv/bin/activate
   ```
3. Install dependencies:
   ```bash
   pip install -r requirements.txt aiosqlite
   ```
4. Copy `.env.example` to `.env` and fill in details:
   ```bash
   cp .env.example .env
   ```
5. Run migrations:
   ```bash
   alembic upgrade head
   ```
6. Start the server:
   ```bash
   uvicorn app.main:app --reload
   ```
   API will be running on [http://localhost:8000](http://localhost:8000).

### 3. Frontend Setup
1. Navigate to the frontend directory:
   ```bash
   cd ../frontend
   ```
2. Install npm dependencies:
   ```bash
   npm install
   ```
3. Copy `.env.example` to `.env`:
   ```bash
   cp .env.example .env
   ```
4. Start Vite development server:
   ```bash
   npm run dev
   ```
   The UI will be running on [http://localhost:5173](http://localhost:5173).

---

## Running Automated Tests

To run the backend business logic unit tests:
```bash
cd backend
python -m pytest tests/
```

---

## API Documentation

### Products (`/api/v1/products`)
- `GET /products`: List products (supports `search`, `min_price`, `max_price`, `min_stock`, `max_stock`, `sort_by`, `sort_order`, `skip`, `limit`).
- `GET /products/{id}`: Inspect individual product.
- `POST /products`: Create product.
- `PUT /products/{id}`: Update product.
- `DELETE /products/{id}`: Delete product.

### Customers (`/api/v1/customers`)
- `GET /customers`: List customers (supports `search`, `sort_by`, `sort_order`, `skip`, `limit`).
- `GET /customers/{id}`: Inspect individual customer.
- `POST /customers`: Register customer.
- `PUT /customers/{id}`: Update customer profile.
- `DELETE /customers/{id}`: Delete customer.

### Orders (`/api/v1/orders`)
- `GET /orders`: List order logs.
- `GET /orders/{id}`: Inspect order invoice details.
- `POST /orders`: Place multi-item order (decrements stock, rolls back on validation failures).

### Dashboard (`/api/v1/dashboard`)
- `GET /dashboard/stats`: Returns KPI summaries and low stock alerts.

---

## Production Deployment

### 1. Database (Neon PostgreSQL)
1. Sign up on [Neon.tech](https://neon.tech) and create a PostgreSQL instance.
2. Copy the Connection String (use the pooler version if using serverless platforms).

### 2. Backend (Render)
1. Sign up on [Render](https://render.com).
2. Connect your Git repository.
3. Import the `render.yaml` configuration or create a new Web Service using the Docker environment.
4. Set the `DATABASE_URL` environment variable pointing to your Neon database instance (substitute `postgresql://` with `postgresql+asyncpg://` for Python async compatibility).

### 3. Frontend (Vercel)
1. Sign up on [Vercel](https://vercel.com).
2. Connect your Git repository and set the Root Directory to `frontend`.
3. Set the `VITE_API_URL` environment variable to point to your live Render backend API URL (e.g. `https://ims-backend.onrender.com/api/v1`).
4. Click **Deploy**. Vercel will build the Vite assets and use the `vercel.json` config to support client-side routing.
