# Leasy — Architecture Design & Gaps Analysis

## Summary
Leasy is a property rental platform implemented as a React SPA frontend and a Node/Express backend with MongoDB. The repo includes local image storage with optional S3 support and realtime messaging components. This document outlines the current architecture, design goals, missing pieces, and a prioritized action plan to improve performance, security, and maintainability.

## Current stack (implemented)
- Frontend: React SPA (frontend/)
  - Contexts for auth/alerts/notifications
  - Image components with lazy-loading and optimization helpers
  - Build optimizations via craco
- Backend: Node.js + Express (backend/)
  - REST API, JWT auth, file storage abstraction (local/S3)
  - Socket.IO usage for realtime messaging (single-instance)
  - MongoDB for primary data store
- Storage: Local uploads with code paths for S3
- Dev docs: docs/ (local preview, deployment, S3, Atlas guides)

## Design goals
- Robustness: stateless API, HA DB, durable object storage
- Performance: CDN delivery, caching for hot reads, background processing
- Security: least-privilege secrets, hardened HTTP surface, secure uploads
- Maintainability: CI, tests, structured logging, observability

## Proposed logical architecture
1. Client (React SPA)
   - Host on CDN (Netlify/Vercel/CloudFront)
   - Pre-compressed assets (gzip/brotli)
2. CDN / Edge
   - CloudFront / CDN in front of static assets and images (origin: S3)
3. API Layer (stateless)
   - Express behind load balancer (ALB/managed platform)
   - TLS at edge, internal only traffic to app servers
   - Socket.IO scaled with Redis adapter
4. Data & Storage
   - MongoDB Atlas (replica set) for HA and backups
   - S3 for images; CloudFront for delivery; use presigned uploads
5. Cache & Queue
   - Redis for caching, Socket.IO adapter, and job queue (BullMQ)
6. Background Workers
   - Separate worker processes for image processing, emails, cleanup
7. Observability & Ops
   - Structured logs (pino/winston) → log aggregation
   - Tracing/Errors (Sentry), metrics (Prometheus/Grafana)
   - Health/readiness endpoints, backup & restore processes
8. CI/CD & Infra
   - GitHub Actions for test/build/deploy
   - Docker images, IaC (Terraform) or deployment manifests

## Key gaps & recommendations

Performance
- Missing distributed caching (Redis) for frequently read endpoints (listings, counts).
- No job queue: move heavy tasks (image processing, emails) to BullMQ/Redis workers.
- Socket.IO is single-instance; add socket.io-redis adapter for horizontal scaling.
- Use S3 + CloudFront and image resizing/format conversion (Lambda/Cloudinary) for optimal delivery.

Security
- Move secrets from .env to a secrets manager (AWS Secrets Manager, Vault).
- Implement refresh-token rotation and short-lived access tokens.
- Harden CORS, Cookie settings, CSP, HSTS. Restrict Socket.IO origins in production.
- Add server-side upload scanning and stricter validation for file types/sizes.
- Rate limiting should be distributed (Redis) and endpoint-aware.

Data & Indexing
- Verify and align DB indexes with schema field names (e.g., messages ↔ conversation id fields).
- Add indexes for common query patterns: listings.owner, messages.conversation, conversations.participants, timestamps for ordering.

Maintainability & QA
- No automated tests visible. Add:
  - Backend: Jest + supertest
  - Frontend: React Testing Library
  - E2E: Cypress
- Add linting (ESLint), formatting (Prettier), and pre-commit hooks.
- Add OpenAPI/Swagger for API discoverability and versioning (/api/v1).

Observability & Recovery
- Replace only-morgan logging with structured logger and add Sentry.
- Add health and readiness endpoints for orchestration and autoscaling.
- Document backup, restore, and disaster recovery runbooks.

Infra & Deployment
- Missing Dockerfiles, deployment manifests, and IaC.
- Create Dockerfile for backend and worker, GitHub Actions for CI, and deployment manifests (K8s or platform-specific).

## Prioritized action plan

Immediate (0–2 weeks)
- Restrict Socket.IO CORS origins and prepare Redis plan for adapter.
- Fix any db index/schema mismatches (run scripts after verification).
- Move production secrets to a secrets manager.

Short term (2–8 weeks)
- Add Redis for caching, Socket.IO adapter, and job queue (BullMQ).
- Implement presigned uploads to S3 and CloudFront distribution.
- Add structured logging and Sentry.

Mid term (8–16 weeks)
- Dockerize backend and workers; add CI pipelines (GitHub Actions) and automated deployments.
- Add unit/integration tests and E2E tests; publish OpenAPI spec.

Long term
- Full infra-as-code (Terraform) and automated observability dashboards, autoscaling policies, and regular security reviews/pen tests.

## Next-step suggestions (pick one)
- Add docs/infra-checklist.md
- Add Redis + Socket.IO integration snippet (backend/server.js)
- Add Dockerfile(s) for backend and worker
- Create GitHub Actions CI pipeline

Include your chosen next step and I will generate the files/snippets and where to place them.