# REAL ESTATE LEAD BOT

## PrimeHomes Realty

> An AI-powered real estate lead management system that receives customer enquiries, understands their requirements, qualifies leads, stores customer information, and helps the sales team follow up efficiently.

---

# 1. Project Overview

The **Real Estate Lead Bot** is a digital receptionist and lead qualification system for **PrimeHomes Realty**.

The system is designed to handle incoming customer enquiries automatically instead of requiring a salesperson to manually process every message.

A customer can simply send a message such as:

> "Hi, I'm looking for a 3-bedroom apartment around Lekki. My budget is around ₦80 million."

The system should understand the message, extract the useful information, determine whether additional information is required, qualify the lead, store the information, respond to the customer, and notify the sales team when necessary.

---

# 2. Technology Stack (Development)

| Layer       | Technology              | Notes                                      |
|-------------|-------------------------|--------------------------------------------|
| Frontend    | React + Vite + TypeScript | Run with `npm`                             |
| Backend     | FastAPI (Python)        | Run with `uvicorn`                         |
| Database    | **MySQL** (local)       | Installed on your PC – **no Docker needed**|
| Automation  | n8n                     | Local instance                             |
| AI          | LLM (any provider)      | Via API key                                |
| Reporting   | Google Sheets           | Optional                                   |

**Important:**  
For local development we **do not use Docker**.  
Everything runs natively against the MySQL instance already installed on your machine.

Docker files remain in the repository only for future deployment.

---

# 3. Quick Start – Local Development (Recommended)

### Prerequisites

- MySQL running on your PC (port 3306)
- Python 3.11+
- Node.js 18+
- (Optional) n8n

### 1. Clone & configure

```bash
git clone https://github.com/Meenah007/real-estate-lead-bot.git
cd real-estate-lead-bot

cp .env.example .env
```

Edit `.env` and set your real MySQL password:

```env
DATABASE_URL=mysql+aiomysql://root:YOUR_MYSQL_PASSWORD@localhost:3306/real_estate_leads
DATABASE_URL_SYNC=mysql+pymysql://root:YOUR_MYSQL_PASSWORD@localhost:3306/real_estate_leads
```

### 2. Create the database (once)

```sql
CREATE DATABASE real_estate_leads
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci;
```

### 3. Backend

```bash
cd backend
python -m venv .venv

# Windows
.venv\Scripts\activate
# macOS / Linux
source .venv/bin/activate

pip install -r requirements.txt

# Create tables
python -m scripts.create_tables

# Start API
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

- API: http://localhost:8000  
- Swagger: http://localhost:8000/docs

### 4. Frontend

```bash
cd frontend
npm install
npm run dev
```

- Frontend: http://localhost:5173

### 5. n8n (optional for full AI flow)

```bash
npx n8n
# or
n8n start
```

- n8n: http://localhost:5678

---

# 4. Project Structure

```text
real-estate-lead-bot/
├── frontend/                 # React + Vite
├── backend/                  # FastAPI
├── n8n/                      # Workflows
├── database/                 # Seeds & notes
├── tests/
├── docs/                     # All specifications
├── .env.example
├── docker-compose.yml        # Optional – deployment only
└── README.md
```

---

# 5. Documentation

All detailed specifications live in the `docs/` folder:

- PRD
- System Architecture Document
- Database & Data Model Specification
- API Specification
- AI Specification
- n8n Workflow Specification
- UI/UX Specification
- Lead Qualification Spec
- Testing Spec
- Deployment Spec
- DEVELOPMENT_SETUP.md  ← **start here for local setup**
- IMPLEMENTATION.md / TASK.md

---

# 6. Development Principle

> Use the simplest technology that correctly solves the problem.

- FastAPI → application logic & APIs  
- MySQL → system of record  
- n8n → workflow orchestration  
- React → user interface  
- AI → natural language understanding only

---

# 7. Next Steps After Local Setup

1. Verify `/api/v1/health` returns 200
2. Open the chat UI and send a test message
3. Confirm the message is stored in MySQL
4. Configure the n8n webhook to process the message
5. Wire AI extraction → lead update → bot reply

---

**Happy building!**  
If you hit any issues while setting up local MySQL, just share the error and we’ll fix it immediately.
