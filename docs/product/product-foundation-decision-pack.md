# PRODUCT-001 — Product Foundation Decision Pack

Status: Proposed (Decision Gate)
Owner: Product Foundation
Decision type: Docs-only authoritative product decision
Companion ADR: [ADR-PRODUCT-001](../adr/ADR-PRODUCT-001-platform-neutral-agentic-control-tower.md)

> This is a decision-gate document. It changes no runtime code, dependencies,
> Salesforce source, database schema, API, UI, or security configuration. Its
> purpose is to fix the product foundation so that downstream workstreams
> (GOV-001, SF-RETIRE0, ORCH-000, CONN-001, ORCH-001, WEB-RISK1) can proceed
> from a single authoritative product definition.

---

## 1. Product Decision (Authoritative)

SemiFlow is an independent, platform-neutral, AI-native **Agentic Operations
Control Tower** product.

**Initial vertical:** semiconductor order-to-delivery, production, supply,
shipment, and operational-risk intelligence.

**Expansion boundary:** complex manufacturing and enterprise operations.

### 1.1 SemiFlow is NOT

- an AI chatbot
- a Salesforce application
- a Slack application
- a Teams application
- a specific LLM-provider product

### 1.2 Definition (한국어)

> SemiFlow는 기업 운영 데이터, AI 에이전트, 사람의 의사결정, 통제된 실행을
> 연결하는 플랫폼 중립형 Agentic Operations Control Tower 제품입니다.

SemiFlow connects enterprise operations data, AI agents, human decision-making,
and controlled execution as a platform-neutral Agentic Operations Control Tower
product.

---

## 2. Salesforce Classification Decision

Salesforce is **not** the product foundation, default connector, required
platform, or roadmap dependency. Existing Salesforce assets may only be
classified under one of the following:

| Classification | Meaning |
|---|---|
| `RETIRE` | Asset is removed from the core product path; no longer maintained as product surface. |
| `GENERALIZE_TO_PROVIDER_NEUTRAL_CONTRACT` | Asset's capability is preserved only after being lifted into a provider-neutral contract with no Salesforce coupling. |
| `ARCHIVE_AS_HISTORICAL_REFERENCE` | Asset is retained read-only for historical/reference purposes, outside the core product. |

### 2.1 Salesforce MUST NOT be described as

- default connector
- product foundation
- required platform
- core roadmap dependency
- permanent demo shell

Salesforce may be reconsidered later as a **separate plugin or package** if a
concrete market requirement emerges, but it is not part of the current product
default configuration.

### 2.2 Scope guard for this decision

This decision does **not** delete, move, or modify any Salesforce source file.
The classification above is a product-direction decision only. Physical asset
handling (retirement/generalization/archival) is executed by the downstream
`SF-RETIRE0` workstream after PRODUCT-001 and GOV-001 are accepted.

---

## 3. Orchestration Runtime Boundary (LangGraph)

LangGraph is the **default orchestration runtime**, used strictly behind
framework-neutral contracts. `core/domain`, security, audit, and transaction
contracts MUST NOT import or depend on LangGraph.

### 3.1 Module boundary

```
core/orchestration/
- framework-neutral state
- commands / results
- policies
- ports
- NO LangGraph imports

orchestration/langgraph/
- graph builder
- node wiring
- routing
- checkpointer
- interrupt / resume
- LangGraph runtime integration
```

### 3.2 LangGraph IS responsible for

- workflow sequencing
- parallel investigation
- conditional routing
- pause / resume
- orchestration state transitions

### 3.3 LangGraph is NOT responsible for

- permission decisions
- risk calculation truth
- database transactions
- human approval
- audit persistence
- execution-success truth
- reconciliation

The items in 3.3 are owned by domain, security, audit, and transaction
contracts and remain independent of any orchestration framework so the runtime
can be replaced without touching truth-bearing logic.

---

## 4. Product Stages

| Stage | Name | Constraint |
|---|---|---|
| 1 | Observe | No external writes |
| 2 | Investigate | No external writes |
| 3 | Recommend | No external writes |
| 4 | Decide | Human decision surface |
| 5 | Execute | Controlled execution (requires §6 prerequisites) |
| 6 | Enterprise | Scale / expansion |

Stages 1–3 prohibit **all** external writes.

**First agent feature:** `ORCH-001 — Read-only Risk Investigation Graph`
(a Stage 1–3 capability with no external write surface).

---

## 5. Required Product Modules

Each module is specified against seven dimensions: **Purpose, Users, Current
State, Target Capability, Permissions, Audit, Dependencies.**

| Module | Purpose | Users | Current State | Target Capability | Permissions | Audit | Dependencies |
|---|---|---|---|---|---|---|---|
| Dashboard | Operational overview and entry point | Operators, managers | Read surface | Aggregated KPIs, risk/exception summary | Read | View events | Read-only APIs |
| Orders | Order-to-delivery visibility | Ops, planners | WEB-ORD1 in progress (PR #167) | Order list/detail, status, due-soon/overdue | Read (Stage 1–3) | View events | Orders/risk/timeline APIs |
| Accounts | Account/customer context | Ops, account owners | Read surface | Account view with linked orders/risk | Read | View events | Account APIs |
| Risks | Operational-risk intelligence | Risk analysts, ops | Read surface | Risk normalization, investigation feed | Read | View + investigation trail | Risk API, ORCH-001 |
| Operations | Production/supply signals | Ops | Read surface | Operational state, exceptions | Read | View events | Ops APIs |
| Shipments | Shipment tracking | Logistics, ops | Read surface | Shipment status, ETA, exceptions | Read | View events | Shipment APIs |
| AI Workspace | Agent investigation surface | Analysts | Not started | Read-only agent investigation (ORCH-001) | Read | Full investigation audit | ORCH-001, RAG |
| Action Center | Human approval + action lifecycle | Approvers, ops | Not started | Action lifecycle, approval, SoD | Scoped write (gated) | Full lifecycle audit | §6 prerequisites |
| Audit & Compliance | Truth-of-record | Compliance, auditors | Not started | Append-only audit, query, export | Read (write via system only) | Self-auditing | Persistent audit store |
| Knowledge & RAG | Retrieval-grounded context | Analysts, agents | Not started | Curated knowledge + retrieval | Read | Retrieval provenance | RAG store |
| Integrations | Provider-neutral connectors | Admins | Salesforce-legacy present | Provider-neutral connector contracts | Admin | Config-change audit | CONN-001 |
| Administration | Tenant/user/policy config | Admins | Basic | RBAC, policy, SoD config | Admin | Config-change audit | Security contracts |
| Observability | System health/telemetry | Ops, SRE | Basic | Metrics, traces, orchestration state view | Read | Access audit | Telemetry pipeline |

---

## 6. Execution Prerequisites (Write-Agent Gate)

No **write** agent is activated until **all** of the following are implemented:

- Persistent append-only audit
- Transactional outbox
- Action Center lifecycle
- Human approval
- Separation of Duties (SoD)
- Idempotency
- Stale-state revalidation
- Read-back
- Reconciliation
- Compensation
- Emergency stop

Until this gate is satisfied, agent capability is limited to read-only
Stage 1–3 features (starting with `ORCH-001`).

---

## 7. Prohibited in This Decision Gate

This PRODUCT-001 change is docs-only. The following are explicitly out of scope:

- No runtime code
- No dependency changes
- No LangGraph installation
- No Salesforce source deletion
- No API / UI / security changes
- No database migration
- No PROJECT_STATUS update
- No AGENTS.md / CLAUDE.md update
- No PR #167 modification
- No issue closure

---

## 8. Downstream Sequence (Informative)

```
PRODUCT-001 (this pack) — accepted
  → GOV-001 governance revision
  → SF-RETIRE0 + ORCH-000 (parallel)
  → CONN-001
  → ORCH-001 (Read-only Risk Investigation Graph)
  → WEB-RISK1 (AI Workspace wiring)
```

This sequence is informative context, not permission to broaden the scope of
this decision gate.

---

## 9. Acceptance

This pack is accepted when an independent review confirms:

- Salesforce foundation is rejected (only RETIRE / GENERALIZE / ARCHIVE allowed).
- LangGraph boundary is defined and truth-bearing contracts stay framework-neutral.
- No runtime, dependency, Salesforce source, or governance file was modified.
- Exactly the two decision-gate documents were added.
