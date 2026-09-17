# DEVELOPMENT_SETUP.md

# Real Estate Lead Bot — Development Setup & Implementation Guide

## 1. Purpose

This document explains how the Real Estate Lead Bot should be set up and developed.

The goal is to provide a simple implementation guide that developers and AI coding agents can follow without introducing unnecessary complexity.

The project should be developed in small, testable stages.

---

# 2. Core Technology Stack

The initial stack is:

```text
Frontend
→ React (Vite + TypeScript)

Backend
→ Python + FastAPI

Database
→ MySQL (local installation on the developer’s machine)

Automation
→ n8n (local instance)

AI
→ LLM through the chosen AI provider

Reporting / Operations
→ Google Sheets (optional)
```

**Important:** Docker is **not required** for day-to-day development.  
We run everything natively (MySQL on your PC, backend with uvicorn, frontend with npm, n8n locally).

Docker is reserved for later deployment.

---

# 3. Development Philosophy

The project should follow these principles:

### Keep it simple
Do not introduce technologies that are not required.

### Build incrementally
Do not attempt to build the entire system at once.

### Test as we go
Each major feature should work before moving to the next one.

### Keep responsibilities clear
Each technology should do what it is best suited for.

### Avoid unnecessary abstraction
Do not create complicated architectures for simple functionality.

---

# 4. Initial Project Structure

```text
real-estate-lead-bot/
│
├── frontend/
├── backend/
├── n8n/
├── database/
├── tests/
├── docs/
│
├── .env.example
├── .gitignore
├── docker-compose.yml          # Optional – only for later deployment
└── README.md
```

---

# 5. Frontend Structure

The React application is organized around features.

```text
frontend/
│
├── src/
│   ├── components/
│   ├── pages/
│   ├── services/
│   ├── hooks/
│   ├── types/
│   ├── utils/
│   └── app/
│
├── package.json
└── README.md
```

---

# 6. Backend Structure

```text
backend/
│
├── app/
│   ├── main.py
│   ├── api/v1/
│   ├── models/
│   ├── schemas/
│   ├── services/
│   ├── repositories/
│   ├── core/
│   └── db/
│
├── tests/
├── requirements.txt
└── README.md
```

---

# 7. Backend Responsibilities

FastAPI handles:

- API endpoints
- Request validation
- Authentication / Authorization
- Database operations
- Lead, conversation and message management
- Business rules and lead qualification

Large automation workflows belong in n8n.

---

# 8. Database Setup (Local MySQL)

**Requirement:** MySQL must be installed and running on your PC.

### 8.1 Create the database

Open MySQL (Workbench, CLI, or any client) and run:

```sql
CREATE DATABASE real_estate_leads
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci;
```

### 8.2 Configure environment variables

Copy the example file:

```bash
cp .env.example .env
```

Edit `.env` and set your real MySQL password:

```env
DATABASE_URL=mysql+aiomysql://root:YOUR_MYSQL_PASSWORD@localhost:3306/real_estate_leads
DATABASE_URL_SYNC=mysql+pymysql://root:YOUR_MYSQL_PASSWORD@localhost:3306/real_estate_leads
```

Replace `YOUR_MYSQL_PASSWORD` with the actual password of your MySQL user.

---

# 9. Database Migration / Table Creation

From the `backend` folder (with virtual environment activated):

```bash
cd backend

# Create tables (simple helper)
python -m scripts.create_tables

# Or use Alembic (preferred once you start changing schema)
alembic revision --autogenerate -m "initial schema"
alembic upgrade head
```

---

# 10. Environment Variables

Always work with a local `.env` file (never commit it).

The template is `.env.example`.

---

# 11. How to Run the Project Locally (No Docker)

### Terminal 1 – Backend

```bash
cd backend
python -m venv .venv
# Windows
.venv\Scripts\activate
# macOS / Linux
source .venv/bin/activate

pip install -r requirements.txt

uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

Backend will be available at: http://localhost:8000  
Swagger docs: http://localhost:8000/docs

### Terminal 2 – Frontend

```bash
cd frontend
npm install
npm run dev
```

Frontend will be available at: http://localhost:5173 (or the port Vite shows)

### Terminal 3 – n8n (optional for full flow)

Install n8n globally or use npx:

```bash
npx n8n
```

or if installed globally:

```bash
n8n start
```

n8n UI: http://localhost:5678

---

# 12. API Development Order

Build / verify in this order:

1. Health endpoint
2. Authentication (later)
3. Leads CRUD
4. Conversations
5. Messages
6. Qualification
7. Follow-ups

---

# 13. Customer Message Flow

```text
Customer → React → POST /messages → FastAPI
                                    ↓
                              Store message
                                    ↓
                              Trigger n8n
                                    ↓
                              AI + Qualification
                                    ↓
                              Update lead + bot reply
```

---

# 14. n8n Setup

Create workflows under `n8n/workflows/`.

Primary workflow to build first:

```text
PRH-LEAD-PROCESS-MESSAGE
```

---

# 15. AI Implementation

AI is responsible for:

- Intent detection
- Entity extraction
- Missing-field detection
- Response generation

AI must **never** write directly to the database.

---

# 16. Lead Qualification

Deterministic scoring lives in the FastAPI service layer.

AI only extracts structured data.

---

# 17. Google Sheets

Google Sheets is secondary.  
Primary source of truth is always **MySQL**.

---

# 18. Local Development Ports

```text
Frontend   → http://localhost:5173
Backend    → http://localhost:8000
MySQL      → localhost:3306
n8n        → http://localhost:5678
```

---

# 19. Docker

Docker is **optional** and reserved for later deployment.

For normal development you should **not** need Docker.

---

# 20. Recommended Development Sequence

```text
1. Local MySQL database ready
2. Backend health + models
3. Core APIs (leads, conversations, messages)
4. Frontend chat + leads list
5. n8n webhook trigger
6. AI extraction inside n8n
7. Full qualification + notifications
8. Sales dashboard polish
9. Testing
10. Deployment (Docker later)
```

---

# 21. Final Principle

```text
Simplicity + Reliability + Clear Responsibilities + Good UX
```

Prefer the simplest solution that works reliably on a developer’s machine with local MySQL.
