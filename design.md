# Design Document — Collaborative Document Management Backend

## Overview
Prototype backend to allow users to upload, download, share, and version documents.
Built as a modular FastAPI service with SQLAlchemy models and pluggable storage.

## Core Requirements Mapping
1. Upload/Download: HTTP endpoints to upload files (multipart) and download by version or latest.
2. Metadata: `Document` model contains title, author (user id), tags (comma-separated), uploaded_date, file_size.
3. Sharing & Permissions: `DocumentShare` model maps users to permissions (READ, EDIT, ADMIN).
4. Versioning: Each upload creates a `DocumentVersion` row; versions store file path & changelog.
5. Search & Filters: SQLite FTS could be used; prototype uses SQL queries with indexes on title/author; tags are searchable.

## Tech Choices & Trade-offs
- **FastAPI**: Fast development, async-ready, automatic OpenAPI.
- **SQLite for prototype; Postgres for production**: SQLite is simple for demos. Use Postgres for concurrency, larger datasets, and FTS support.
- **File storage**: Local disk for prototype; S3 (or equivalent) recommended for scale.
- **Versioning**: Immutable file storage per version; storing diffs is complex but more storage-efficient — trade complexity vs speed.
- **Search**: For scale, use Elasticsearch or PostgreSQL full-text search. Prototype uses simple queries and indexes.

## Data Model (simplified)
- `User` (id, username, email)
- `Document` (id, title, owner_id, created_at)
- `DocumentVersion` (id, doc_id, version_number, file_path, uploaded_at, size, changelog)
- `DocumentShare` (id, doc_id, user_id, permission)
- `Tag` (id, name) and `document_tags` association (optional)

## APIs (selected)
- `POST /upload` — upload new document or new version
- `GET /documents/{id}` — get metadata (latest)
- `GET /documents/{id}/download?version=` — download
- `POST /documents/{id}/share` — share with user and permission
- `GET /search?q=&tags=&author=&min_size=&max_size=&from_date=&to_date=`

## Scalability & Performance
- Move storage to S3, metadata in a horizontally scalable DB (Postgres/Aurora).
- Use CDN for serving large files.
- Use read replicas and caching (Redis) for metadata and permissions.
- Implement pagination and cursors for listing large datasets.
- Use background workers (Celery/RQ) for heavy tasks (thumbnailing, OCR, virus-scan).

## Security & Authorization
- JWT-based auth for APIs (not implemented in prototype).
- File access presigned URLs when using S3.
- Validate uploads (size limits, MIME checks), virus-scanning integration.
- Audit logs for access and changes.

## Future Enhancements
1. Real-time collaborative editing (operational transforms / CRDTs) with WebSocket or WebRTC backend.
2. Commenting system tied to document ranges; implement permissions, moderation, and notifications.
3. Full-text search with Elasticsearch or Postgres FTS.
4. Delta storage (store diffs) to reduce storage for frequent small edits.

