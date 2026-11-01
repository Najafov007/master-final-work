# Security Threat Simulation & Monitoring Dashboard

Master's thesis project (MSc, Information Security of Telecommunication Systems, Azerbaijan Technical University, 2025).

A web platform that simulates common network and application attacks, records them as security events and shows them on a live threat dashboard. The whole stack — app, data stores and monitoring — starts with one `docker compose up`.

Built by **Nijat Najafov** together with **Murad Huseynli** ([@Shin778](https://github.com/Shin778)).

## What it does

- **Attack simulations:** interactive DDoS, man-in-the-middle (MITM), SQL injection and malware file-upload scenarios.
- **Threat intelligence dashboard:** threat counts, severity breakdown and trends (Recharts).
- **Security logs:** security event log backed by PostgreSQL.
- **Monitoring:** Prometheus scrapes the host, Redis and the app; Grafana visualises it.

## Architecture

```
Browser ──► Frontend (React 19, Tailwind, served by Nginx)  :3000
                │
                ▼
           Backend API (Node.js / Express)                  :8000
             │                     │
             ▼                     ▼
   PostgreSQL 15 (threats, logs)   Redis 7 (cache, demo store)
             ▲                     ▲
             │                     │
   pgAdmin :5050         Redis Commander :8081

Prometheus :9090 ◄── node-exporter :9100, redis-exporter :9121
     │
     ▼
Grafana :3001
```

| Layer | Tech |
|---|---|
| Frontend | React 19, React Router 7, Tailwind CSS, Framer Motion, Recharts |
| Backend | Node.js, Express, `pg`, `redis` |
| Data | PostgreSQL 15 (schemas `security`, `logs`), Redis 7 (AOF persistence) |
| Containers | Multi-stage Dockerfile (Node build → Nginx), Docker Compose with health checks |
| Monitoring | Prometheus, Grafana (provisioned datasources), node-exporter, redis-exporter |
| CI | GitHub Actions (Node 18 / 20 matrix: install, lint, build), Snyk dependency checks |

## API

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/health` | service, database and Redis health |
| GET / POST | `/api/threats` | list or record threats |
| GET | `/api/threats/stats` | aggregated threat statistics |
| GET | `/api/logs` | security event log |
| GET | `/api/dashboard/metrics` | dashboard metrics |
| GET / POST / DELETE | `/api/redis/*` | Redis demo: stats, set, get, keys, delete |

## Run locally

```bash
docker compose up -d --build
```

| Service | URL |
|---|---|
| App | http://localhost:3000 |
| API | http://localhost:8000/health |
| Grafana | http://localhost:3001 |
| Prometheus | http://localhost:9090 |
| pgAdmin | http://localhost:5050 |
| Redis Commander | http://localhost:8081 |

Credentials in `docker-compose.yml` are demo values for local use only.

Frontend only, without Docker:

```bash
npm ci
npm start
```
