# Monarch Nexus v2.0 - Unified Integration Plan

**Owner:** Garrett Carroll  
**Date:** December 23, 2025  
**Status:** 🚀 Live

---

## Executive Summary

This document defines how **GitHub, Linear, and Notion** work together as a single enforced system for Monarch Nexus v2.0:

- **GitHub** = Source of truth (code, policy, architecture)
- **Linear** = Execution engine (all work tracked and prioritized)
- **Notion** = Knowledge layer (docs, runbooks, decisions)

**Result:** Every change is automatically validated against architecture, tracked in work queue, and documented for operations.

---

## Architecture Overview

### GitHub: Enforced Truth

**Primary Role:** Single source of truth with automatic architecture compliance gates.

**What Lives Here:**
- All app/infra code (Monarch Nexus v2.0)
- `.github/WELL_ARCHITECTED_CHECKLIST.json` – 12 compliance gates
- `.github/workflows/*.yml` – CI/CD enforcement
- `.github/CODEOWNERS` – Team ownership rules

**Enforcement:**
- Required status checks (must pass before PR merge)
  - `architecture-compliance` – Security + HTTPS + secrets scanning
  - `ai-agent-validation` – LangChain, fallback logic, cost controls
  - `cadt-integration-tests` – Circuit breaker, retry logic, sync monitoring
- Branch protection (no direct pushes to `main`)
- Code owners approval

**Outcome:** No change lands without passing security, architecture, and integration gates.

---

### Linear: Execution Engine

**Primary Role:** All work tracked, prioritized, and linked to code changes.

**Teams:**
1. **API & AI** (API)
   - FastAPI gateway
   - LangChain AI agent
   - Biomass calculator

2. **CADT & Carbon** (CADT)
   - CAD Trust blockchain integration
   - Unit tracking
   - ITMO verification

3. **Infrastructure & Security** (INFRA)
   - AWS ECS, RDS, CloudWatch
   - Secrets management
   - Cost optimization

**Integration with GitHub:**
- Auto-create branches from Linear issues
- Link PRs ↔ issues automatically
- Update issue status on PR state change
- Close issues when PRs merge

**Core Epics:**
1. Well-Architected Framework Compliance (INFRA)
2. CADT Production Readiness (CADT)
3. AI Agent Reliability (API)

**Outcome:** Every feature request and bug becomes a tracked work item, preventing lost context.

---

### Notion: Knowledge Layer

**Primary Role:** Architecture documentation, operational runbooks, and decision records.

**Three Core Databases:**

#### 1. Architecture Registry
- **Purpose:** Single source of truth for system design
- **Pages:**
  - FastAPI Gateway – API tier, entry point
  - LangChain AI Agent – Intelligence tier, async task orchestration
  - CADT Connector – Carbon tier, blockchain sync
  - Biomass Calculator – Carbon tier, emissions calculations
  - PostgreSQL RDS – Data tier, project/credit storage
  - AWS ECS Fargate – Infrastructure, serverless container orchestration

- **Properties:**
  - System name (title)
  - Status (Stable, In Progress, Planned, Deprecated)
  - Team ownership (API, CADT, INFRA)
  - GitHub repo link
  - Linear epic link
  - Tech stack (Python, FastAPI, LangChain, Chia SDK, PostgreSQL, etc.)
  - Last deployed date

#### 2. Runbooks & Operations
- **Purpose:** "How do we respond when X fails?"
- **Pages:**
  - CADT Sync Failure – Detect, immediate actions, recovery steps
  - AI Agent High Latency – Root causes, fallback triggers, cost impacts
  - RDS Connection Pool Exhausted – Detection, scaling, prevention

- **Properties:**
  - Incident name (title)
  - Affected system
  - Severity (Critical, High, Medium, Low)
  - Detection method (metric, alert, log pattern)
  - CloudWatch dashboard link
  - Related Linear issues
  - Step-by-step recovery procedure

#### 3. Architecture Decision Records (ADRs)
- **Purpose:** "Why did we choose X over Y?"
- **Pages:**
  - Use ECS Fargate over Kubernetes – Rationale, trade-offs, alternatives considered
  - LangChain for AI orchestration – Tooling, fallback strategy, observability
  - PostgreSQL on RDS for primary database – ACID guarantees, backup strategy

- **Properties:**
  - Decision title
  - Date decided
  - Status (Proposed, Accepted, Deprecated)
  - Team
  - GitHub PR link
  - Linear epic link
  - Context, decision, rationale, consequences

**Outcome:** New team members can self-onboard; anyone can understand system design without reading code.

---

## Integration Flows

### Flow 1: New Feature → Code → Deployment

1. **Notion ADR:** Document architectural decision (e.g., "Add CADT mainnet support")
2. **Linear Epic:** Create epic with child issues
3. **GitHub Branch:** Linear auto-creates `feat/cadt-mainnet` branch
4. **Development:** Write code, push commits
5. **GitHub Actions:**
   - Run `architecture-gate.yml` – Verify HTTPS, secrets, checklist compliance
   - Run `cadt-integration-tests.yml` – Test circuit breaker, retry logic
   - Post results to PR
6. **PR Review:** Code owner approves (branch protection enforced)
7. **Merge:** PR merges to `main`
8. **Deployment:** `deploy-ecs.yml` runs
9. **Linear:** Issue auto-closes
10. **Notion:** Architecture Registry updated with deployment date

### Flow 2: Production Incident → Runbook → Fix → Close

1. **CloudWatch Alert:** "CADT sync lag > 5 minutes"
2. **Notion Runbook:** Engineer opens "CADT Sync Failure" page
3. **Immediate Actions:** Follow detection + recovery steps
4. **Linear Issue:** Create "Debug: CADT sync lag spike" issue
5. **GitHub Fix:** Branch + PR with root cause fix
6. **Merge:** Deploy fix
7. **Post-Incident:** Update runbook with new insights

### Flow 3: Tech Debt → Architecture Decision → Tracking

1. **Notion ADR:** Propose "Migrate to read replicas for analytics queries"
2. **Linear Epic:** Create epic "Database optimization"
3. **GitHub:**
   - Create branch `infra/db-replicas`
   - Implement with architecture checks enabled
   - Link PR to Linear epic
4. **Deploy:** Roll out read replicas
5. **Notion:** Update Architecture Registry, mark as "Stable"

---

## Compliance Gates

### Architecture Gate (`.github/workflows/architecture-gate.yml`)

**Runs on:** Every PR to `main`

**Checks:**
1. ✅ PR references Linear issue (e.g., "Linear: API-123")
2. ✅ No hardcoded HTTP endpoints (HTTPS only)
3. ✅ No secrets leaked (TruffleHog + regex patterns)
4. ✅ No high/critical CVEs (Trivy scan)
5. ✅ Well-architected checklist compliant

**Failure = PR cannot merge**

### AI Agent Tests (`.github/workflows/ai-agent-tests.yml`)

**Runs on:** Changes to `services/ai-agent/`

**Checks:**
1. ✅ LangChain imports present
2. ✅ Fallback to Anthropic Claude implemented
3. ✅ LangSmith tracing enabled
4. ✅ Token limits configured (cost control)
5. ✅ Unit tests pass

### CADT Integration Tests (`.github/workflows/cadt-integration-tests.yml`)

**Runs on:** Changes to `services/cadt-connector/`

**Checks:**
1. ✅ Circuit breaker pattern implemented
2. ✅ Exponential backoff retry logic (1s, 2s, 4s, 8s, 16s)
3. ✅ Redis queue for failed syncs
4. ✅ Health endpoint for Chia node
5. ✅ Sync lag monitoring configured

---

## Setup Instructions

### GitHub Setup (15 minutes)

1. Repo already created: `Garrettc123/monarch-nexus-v2`
2. Branch protection enabled on `main`
3. Workflows live in `.github/workflows/`
4. Secrets configured in repo settings

### Linear Setup (30 minutes)

1. Go to Linear.app → Settings → Integrations
2. Connect GitHub and select `monarch-nexus-v2` repo
3. Create three teams: API, CADT, INFRA
4. Create core epics as listed above
5. Enable auto-branch creation from issues

### Notion Setup (45 minutes)

1. Create workspace: "Monarch Nexus Engineering"
2. Create three databases: Architecture Registry, Runbooks, ADRs
3. Add pages as documented
4. Share workspace with Notion bot for future automation

---

## Success Criteria

Integration is working when:

- ✅ Every PR must reference a Linear issue
- ✅ Every PR must pass architecture gate
- ✅ Linear issues auto-link to GitHub PRs
- ✅ On merge, Linear issues auto-close
- ✅ Notion Architecture Registry shows latest deployment dates
- ✅ Runbooks are up-to-date after incidents
- ✅ ADRs link to implemented PRs

---

## System Health Dashboard

**Check these weekly:**

- GitHub Actions: All workflows passing
- Linear: Tech debt backlog < 50 items
- Notion: Runbook updates recent (< 2 weeks old)

---

## Next Steps

1. ✅ GitHub workflows deployed
2. ⏳ Wire Linear integration (30 min)
3. ⏳ Build Notion databases (45 min)
4. ⏳ Run first test PR (5 min)
5. ⏳ Deploy infrastructure (AWS ECS)

**Expected Timeline:** 2-3 hours to full integration
