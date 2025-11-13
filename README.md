# Collaborative Document Management Backend (Prototype)

Minimal prototype backend for a collaborative document management system.
- FastAPI backend
- SQLAlchemy + SQLite (swap for Postgres in production)
- File storage on disk (swap for S3 in production)
- Versioning, sharing permissions, metadata, and search endpoints

## Run locally (development)
```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload
```

## Docker
```bash
docker build -t collab-doc-backend .
docker run -p 8000:8000 collab-doc-backend
```

## What is included
- `app/` : FastAPI application
- `tests/` : pytest tests (unit-level)
- `design.md` : architecture and decisions
- CI config and Dockerfile, docker-compose
