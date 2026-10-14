# Cloud RAG Drone Fire Detection: Cloud Backend and Azure Infrastructure

**Pranitha N S (23BIT0320)**, BITE412L Cloud Computing, VIT (2025–2026), guided by Priya V

This repository holds my part of the team project *Cloud-Based Retrieval-Augmented Drone Framework for Early Forest Fire Detection and Response Planning*.
The complete integrated system is in the team repository:
**https://github.com/Veladicodes/cloud-rag-drone-fire-detection**

| Team member | Module |
| :--- | :--- |
| Adithya A (23BIT0323) | YOLOv8 edge detection: training and evaluation (`ai/models`, `ai/evaluation`) |
| **Pranitha N S (23BIT0320)** | **FastAPI backend, database, Azure/Terraform infrastructure (this repo)** |
| Hemanth S (23BIT0411) | RAG response-planning pipeline and React dashboard |

Azure provisioning was shared by all three members (report, Section 12). It lives here because it deploys the backend and data layer.

## What is in this repo

```
backend/                FastAPI ingestion service
  api/                  incident, response-plan, telemetry, recipient routes + WebSocket
  core/                 settings (pydantic-settings), DB session, API-key auth
  services/             incident pipeline, two-stage alerting, telemetry + MAVLink ingest
  Dockerfile
database/               SQLAlchemy 2.x models, Alembic migration, seed data
cloud/                  storage dispatcher (local / Azure Blob)
  terraform/            Container Apps, Static Web Apps, SQL Serverless, Blob, Key Vault, Log Analytics, budget
  DEPLOY.md             step-by-step Azure deployment
.github/workflows/      CI tests and backend image build/push to ACR
testing/                backend unit tests, drone simulator, latency benchmark
results/latency/        measured end-to-end latency
docs/adr/               architecture decision records
```

## Highlights (from the Review 2 report)

- Two-stage alerting: the immediate alert fires as soon as the incident is saved, before any LLM call. The enriched alert follows with the response plan.
- One backend image runs against SQLite and local files, or Azure SQL and Blob Storage, switched by `DATABASE_URL` / `STORAGE_MODE`.
- Live on Azure (Korea Central): a POST to the deployed backend wrote a real Azure SQL row and returned a Gemini plan with SOP citations.
- Cost control under the $100 student credit: everything runs on consumption, serverless or free tiers; Container Apps scale to zero and SQL auto-pauses.
- Backend test suite: 9/9 passing.

## Running

The incident pipeline calls the RAG module (`ai.rag`), so the backend runs as part of the full system.
Use the team repository's quick start (`make setup && make index && make seed && make backend`).
