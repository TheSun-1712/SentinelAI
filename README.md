# SentinelAI 

A full-stack cyber defense simulation platform with:
- A **FastAPI backend** for live traffic simulation, threat classification, admin workflows, and metrics
- A **React + Vite frontend** with separate Client and Admin portals
- Optional **LLM/RAG assistant** support via Ollama (`llama3`)

## Architecture

- `frontend/`: React TypeScript UI (Client + Admin portals)
- `server/`: Main FastAPI app, model inference loop, database models, AI routes
- `Model/`: Standalone Flask + RAG prototype module
- `bootstrap.ps1`: Windows setup helper for backend/frontend dependencies

## Features

- Simulated real-time traffic stream from `large_simulation_log.csv`
- Multi-class IDS predictions with XGBoost artifacts
- Dynamic auto-block thresholding and queue-aware automation control
- Manual review pipeline and decision tracking
- Admin authentication endpoints (`/api/auth/admin/*`)
- Aggregated SOC metrics for dashboards (`/api/metrics/*`, `/api/system/*`)
- Streaming AI assistant endpoint (`/api/ai/chat`) with DB-aware context

## Prerequisites

- Python 3.10+
- Node.js 18+
- npm
- PostgreSQL (local or remote)
- Optional: Ollama for AI assistant routes

## Quick Start (Windows)

From project root:

```powershell
cd "e:\Pixel Pioneers\Pixel_Pioneers-main\Pixel_Pioneers-main"
.\bootstrap.ps1
```

The bootstrap script:
- Creates `server/.venv`
- Installs `server/requirements.txt`
- Installs `frontend` npm dependencies
- Copies `large_simulation_log.csv` into `server/` when found

## Manual Setup

### 1. Backend

```powershell
cd server
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 2. Frontend

```powershell
cd frontend
npm install
```

### 3. Database

Backend uses PostgreSQL via SQLAlchemy.

Set these environment variables before starting backend:

- `DB_USER` (default: `postgres`)
- `DB_PASSWORD` (default in code: `Aish@2003`)
- `DB_HOST` (default: `localhost`)
- `DB_PORT` (default: `5432`)
- `DB_NAME` (default: `idsdashboard`)

Example (PowerShell):

```powershell
$env:DB_USER="postgres"
$env:DB_PASSWORD="your_password"
$env:DB_HOST="localhost"
$env:DB_PORT="5432"
$env:DB_NAME="idsdashboard"
```

## Running the Project

### Start backend

```powershell
cd server
.\.venv\Scripts\Activate.ps1
uvicorn main:app --reload --port 8000
```

### Start frontend (new terminal)

```powershell
cd frontend
npm run dev
```

Frontend expects backend at `http://localhost:8000/api`.

## AI Assistant (Optional)

The server AI module streams from Ollama:
- URL: `http://localhost:11434/api/generate`
- Model: `llama3`

Install/pull model:

```powershell
ollama pull llama3
```

If Ollama is not running, core dashboards still work, but AI chat routes may fail.

## Model Training and Artifacts

Training script:

```powershell
cd server
.\.venv\Scripts\Activate.ps1
python train_model.py
```

Generated/updated artifacts include:
- `multiclass_xgboost_ids.joblib`
- `xgboost_explainer.joblib`
- `label_encoder.joblib`
- `scaler.joblib`
- `model_metadata.json`
- `feature_baseline.json`
- `model_benchmark.json`

## Key Environment Variables

- `RESET_DB_ON_START`: `true` to drop/recreate DB schema at backend startup
- `ADMIN_USERNAME`: admin login username (default: `admin`)
- `ADMIN_PASSWORD`: admin login password (default: `admin123`)
- `ADMIN_TOKEN_TTL_SECONDS`: token lifetime in seconds (default: `28800`)
- `ADMIN_AUTH_SECRET`: signing secret for admin token generation

## Main API Groups

- Auth: `/api/auth/admin/login`, `/api/auth/admin/me`
- System health: `/api/system/health`
- Metrics: `/api/metrics/overview` and related dashboard endpoints
- AI assistant: `/api/ai/chat`, `/api/ai/history`

## Frontend Routes

- Landing: `/`
- Client portal: `/client/*`
- Admin login: `/admin/login`
- Admin portal: `/admin/*`

## Testing

There is a helper script in project root:

```powershell
python test_ai_context.py
```

Run it after backend dependencies are installed and services are up as required by the test.

## Notes

- CORS is open (`allow_origins=["*"]`) in backend for local development.
- Replace default credentials/secrets for any non-local environment.
- Keep model artifacts in `server/` aligned with training data and feature schema.
