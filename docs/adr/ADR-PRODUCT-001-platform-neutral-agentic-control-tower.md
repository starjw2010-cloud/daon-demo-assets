# ADR-PRODUCT-001 — Platform-Neutral Agentic Control Tower

- Status: Proposed
- Date: 2026-07-14
- Decision gate: PRODUCT-001
- Companion: [Product Foundation Decision Pack](../product/product-foundation-decision-pack.md)

## Context

SemiFlow's foundation must be fixed before governance and orchestration
workstreams (GOV-001, SF-RETIRE0, ORCH-000, CONN-001, ORCH-001) can proceed
from a single authoritative product definition. Prior assets carried a
Salesforce-centric framing. PRODUCT-001 Discovery and the PROGRAM-000
Workstream Orchestration Blueprint are complete and serve as planning inputs
(not permission to broaden scope).

A decision is required on three coupled questions:

1. What is SemiFlow, as a product?
2. What is the role of existing Salesforce assets?
3. How is the orchestration runtime bounded relative to truth-bearing contracts?

This ADR is a docs-only decision gate. It changes no runtime code,
dependencies, Salesforce source, schema, API, UI, or security configuration.

## Decision

### 1. Product identity

SemiFlow is an **independent, platform-neutral, AI-native Agentic Operations
Control Tower** product. Its initial vertical is semiconductor
order-to-delivery, production, supply, shipment, and operational-risk
intelligence, expanding toward complex manufacturing and enterprise operations.

SemiFlow is explicitly **not** an AI chatbot, a Salesforce application, a Slack
application, a Teams application, or a specific LLM-provider product.

### 2. Salesforce is not the foundation

Salesforce is not the product foundation, default connector, required platform,
or roadmap dependency. Existing Salesforce assets may be classified **only** as:

- `RETIRE`
- `GENERALIZE_TO_PROVIDER_NEUTRAL_CONTRACT`
- `ARCHIVE_AS_HISTORICAL_REFERENCE`

Salesforce must not be preserved as a permanent optional/demo shell in the core
product decision. It may be revisited later as a separate plugin/package if a
concrete market requirement appears. This ADR does not delete or move any
Salesforce source; physical handling is deferred to `SF-RETIRE0`.

### 3. LangGraph behind framework-neutral contracts

LangGraph is the **default orchestration runtime**, integrated strictly behind
framework-neutral contracts. `core/domain`, security, audit, and transaction
contracts MUST NOT import or depend on LangGraph.

- `core/orchestration/` holds framework-neutral state, commands/results,
  policies, and ports — with **no** LangGraph imports.
- `orchestration/langgraph/` holds the graph builder, node wiring, routing,
  checkpointer, interrupt/resume, and runtime integration.

LangGraph owns workflow sequencing, parallel investigation, conditional
routing, pause/resume, and orchestration state transitions. It does **not** own
permission decisions, risk-calculation truth, database transactions, human
approval, audit persistence, execution-success truth, or reconciliation — these
remain framework-independent so the runtime can be replaced without touching
truth-bearing logic.

### 4. Staged capability and write gate

Product delivery follows six stages: Observe → Investigate → Recommend →
Decide → Execute → Enterprise. Stages 1–3 prohibit all external writes. The
first agent feature is `ORCH-001 — Read-only Risk Investigation Graph`.

No write agent is activated until persistent append-only audit, transactional
outbox, Action Center lifecycle, human approval, Separation of Duties,
idempotency, stale-state revalidation, read-back, reconciliation, compensation,
and emergency stop are all implemented.

## Consequences

**Positive**

- One authoritative product definition unblocks GOV-001 and downstream work.
- Truth-bearing contracts stay framework-neutral; the orchestration runtime is
  replaceable.
- Salesforce is decoupled from the roadmap, removing a platform dependency.
- The write gate makes execution risk explicit and enforceable.

**Negative / trade-offs**

- Salesforce-derived capability must be re-expressed as provider-neutral
  contracts before reuse (handled by `SF-RETIRE0` / `CONN-001`).
- A framework-neutral boundary adds an indirection layer around LangGraph.

**Neutral**

- This ADR is a decision only; no code, dependency, schema, or governance file
  changes as part of it.

## Scope guard

Out of scope for this decision gate: runtime code, dependency changes,
LangGraph installation, Salesforce source deletion, API/UI/security changes,
database migration, PROJECT_STATUS, AGENTS.md, CLAUDE.md, PR #167 modification,
and issue closure.

## Review

An independent review session performs the final acceptance check against the
exact head of the decision-gate branch. This ADR does not self-approve; it is
opened as a Draft and must remain Draft until independent review completes.
