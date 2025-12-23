# Monarch Nexus v2.0 - Carbon Credit Platform

**Owner:** Garrett Carroll  
**Status:** 🚀 MVP Development (Weeks 1-4)  
**Tech Stack:** FastAPI + LangChain AI + CADT Connector + PostgreSQL + AWS ECS

---

## 🎯 What Is Monarch Nexus?

Monarch Nexus v2.0 is a production-grade carbon credit platform combining:

- **FastAPI Gateway** – REST API for project and credit management
- **LangChain AI Agent** – Intelligent analysis, anomaly detection, report generation
- **CADT Connector** – Blockchain integration for carbon unit tracking (CAD Trust)
- **Biomass Calculator** – Carbon emission calculations (VM0017/GS4GG methodology)
- **PostgreSQL RDS** – Project, credit, and transaction storage
- **AWS ECS Fargate** – Serverless container orchestration

**Philosophy:** MVP-first with production patterns. Ship fast, monitor obsessively, scale reliably.

---

## 📊 Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│                  Users & Customers                       │
└────────┬────────────────────────────────────┬───────────┘
         │                                    │
         ▼                                    ▼
┌──────────────────────┐         ┌──────────────────────┐
│   React Dashboard    │         │    FastAPI Gateway   │
│  (Web + Mobile)      │◄───────►│  /carbon/projects    │
└──────────────────────┘         │  /carbon/credits     │
                                 │  /ai/analyze         │
                                 └──────┬───────────────┘
                                        │
                    ┌───────────────────┼───────────────────┐
                    ▼                   ▼                   ▼
              ┌──────────┐      ┌──────────────┐    ┌───────────┐
              │ LangChain │      │ Biomass      │    │ CADT      │
              │ AI Agent │      │ Calculator   │    │ Connector │
              │ (Smart   │      │ (VM0017/     │    │ (Chia SDK)│
              │ Analysis)│      │  GS4GG)      │    │           │
              └────┬─────┘      └──────┬───────┘    └─────┬──────┘
                   │                   │                  │
                   └───────────────────┼──────────────────┘
                                       ▼
                              ┌──────────────────┐
                              │  PostgreSQL RDS  │
                              │  (Projects,      │
                              │   Credits,       │
                              │   Transactions)  │
                              └──────────────────┘
                                       ▲
                                       │
                              ┌────────┴────────┐
                              ▼                 ▼
                        ┌──────────┐      ┌──────────┐
                        │ Redis    │      │ CloudWatch
                        │ (Cache + │      │ (Logs +
                        │  Queue)  │      │  Metrics)
                        └──────────┘      └──────────┘
```

---

## 🔧 Development Setup

### Prerequisites
- Python 3.11+
- PostgreSQL 15+
- Redis 7+
- Docker & Docker Compose
- AWS CLI (for ECS deployment)

### Local Development

```bash
# Clone repo
git clone https://github.com/Garrettc123/monarch-nexus-v2.git
cd monarch-nexus-v2

# Setup Python environment
python -m venv venv
source venv/bin/activate  # or `venv\Scripts\activate` on Windows
pip install -r requirements.txt

# Start services
docker-compose up -d postgres redis

# Run migrations
psql -U postgres -h localhost -f migrations/001_init.sql

# Start API
cd services/api
uvicorn main:app --reload --port 8000

# Start AI Agent (separate terminal)
cd services/ai-agent
python agent.py

# Start CADT Connector (separate terminal)
cd services/cadt-connector
python connector.py
```

API will be at `http://localhost:8000`  
API Docs at `http://localhost:8000/docs`

---

## 🏗️ Architecture & Enforcement

Every change to Monarch Nexus is **automatically validated** against:

### Security
- ✅ HTTPS-only (no hardcoded HTTP)
- ✅ No secrets in code (TruffleHog + regex)
- ✅ No high/critical CVEs (Trivy)

### Reliability
- ✅ CADT circuit breaker (protects Chia node failures)
- ✅ Exponential backoff retries (1s, 2s, 4s, 8s, 16s)
- ✅ Health endpoints on all services

### Performance
- ✅ API P95 latency < 500ms
- ✅ Database connection pooling
- ✅ LLM response caching

### Cost
- ✅ AI agent spend < $100/month
- ✅ ECS task right-sizing
- ✅ RDS monitoring for runaway queries

### Observability
- ✅ CloudWatch alarms (error rate, sync lag)
- ✅ LangSmith tracing (all AI calls)
- ✅ Structured logging (JSON format)

**See:** `.github/WELL_ARCHITECTED_CHECKLIST.json` for full compliance matrix.

---

## 🔄 Unified Workflow: GitHub → Linear → Notion

Architecture, work, and documentation stay **perfectly synchronized**:

### GitHub (Source of Truth)
- All code changes
- Enforced compliance gates
- CI/CD pipelines
- Architecture checklist

### Linear (Execution Engine)
- Issue tracking for all work
- Automatic GitHub PR ↔ issue linking
- Roadmap and prioritization
- Tech debt backlog

### Notion (Knowledge Layer)
- Architecture Registry (system docs)
- Runbooks & Operations (incident response)
- ADRs (decision records)

**How It Works:**
1. Create Linear issue: "Add CADT mainnet support"
2. Linear auto-creates branch: `feat/cadt-mainnet`
3. Write code, open PR with "Linear: CADT-123"
4. GitHub checks run → pass → merge → issue auto-closes
5. Notion Architecture Registry updates with deployment date

**See:** `docs/INTEGRATION_PLAN.md` for full workflow.

---

## 📈 Deployment Timeline

### Week 1: Core API + Database
- FastAPI gateway (CRUD for projects/credits)
- PostgreSQL setup (schema + migrations)
- Basic health checks
- Docker images

### Week 2: CADT Integration + Basic UI
- CAD Trust connector (project sync)
- Chia node health monitoring
- React dashboard (minimal)
- Unit tracking API

### Week 3: AI Agent + Monitoring
- LangChain agent (analysis, anomaly detection)
- LangSmith tracing
- CloudWatch alarms
- Cost tracking dashboard

### Week 4: Production Deployment + Pilot
- ECS Fargate deployment
- First pilot customer
- Documentation complete
- Runbook drills

---

## 💰 Cost Breakdown (MVP)

| Service | Monthly Cost | Notes |
|---------|--------------|-------|
| **AWS ECS Fargate** | $200 | 2-10 tasks, auto-scaling |
| **AWS RDS PostgreSQL** | $150 | db.t3.micro, multi-AZ backup |
| **Redis (ElastiCache)** | $50 | cache + task queue |
| **CloudWatch** | $50 | logs, metrics, alarms |
| **OpenAI LLM** | $50 | ~1,000 agent calls/month |
| **Domain + CDN** | $20 | Route 53 + CloudFront |
| **Misc (Secrets Manager, S3)** | $30 | |
| **TOTAL** | ~$550/month | |

**88% cheaper than v1.0** (was $10K/month).

---

## 🚨 Critical Alerts

Monarch Nexus will immediately alert on:

1. **CADT Sync Lag > 5 minutes** – See `docs/RUNBOOKS.md`
2. **AI Agent Latency > 5s** – Check OpenAI status
3. **RDS Connections > 90%** – Scale up immediately
4. **Error Rate > 1%** – Check CloudWatch logs
5. **Cost Spike > 2x baseline** – Review usage

---

## 📚 Documentation

- **[Integration Plan](./docs/INTEGRATION_PLAN.md)** – GitHub/Linear/Notion workflow
- **[Well-Architected Checklist](./.github/WELL_ARCHITECTED_CHECKLIST.json)** – 12 compliance gates
- **[Runbooks](./docs/RUNBOOKS.md)** – Incident response procedures
- **[ADRs](./docs/ADR/)** – Architecture decisions
- **[API Docs](http://localhost:8000/docs)** – Interactive Swagger UI

---

## 🤝 Contributing

1. **Create a Linear issue** (API, CADT, or INFRA team)
2. **Branch name:** Linear auto-creates it (e.g., `feat/api-123`)
3. **Commit message:** Reference the issue (e.g., "API-123: Add project validation")
4. **Open PR:** Reference issue in description
5. **Pass checks:** Architecture gate + tests must pass
6. **Merge:** Review approved, all checks green
7. **Deploy:** ECS updates automatically

**Code owners:** @Garrettc123 (all areas)

---

## 📞 Support

- **GitHub Issues:** Bug reports and feature requests
- **Linear Issues:** Work tracking and prioritization
- **Notion:** Runbooks for incident response
- **Slack:** (Internal team channel)

---

## 📄 License

MIT License © 2025 Garrett Carroll

---

## 🎉 Quick Links

- 🔗 **GitHub:** https://github.com/Garrettc123/monarch-nexus-v2
- 📋 **Linear:** [Monarch Nexus v2.0 Workspace](https://linear.app)
- 📖 **Notion:** [Engineering Docs](https://notion.so)
- 🚀 **API:** http://localhost:8000 (dev) | https://api.monarch-nexus.com (prod)
- 💻 **Dashboard:** http://localhost:3000 (dev) | https://dashboard.monarch-nexus.com (prod)

---

**Built with ❤️ by Garrett Carroll**
