# ADR-071: Runtime and integration boundary for Santi v1

- **Status:** Proposed boundary; provider feasibility remains pending
- **Date:** 2026-10-12
- **Decision owner:** User
- **Related:** `docs/superpowers/specs/2026-10-12-santi-architecture-design.md`, `docs/superpowers/plans/santi-capability-matrix.md`

## Context

Santi v1 needs enforceable authorization, controlled tool execution, safe recovery, cost visibility, audit, and a small software-development workflow. The current `danihal1224/santi-dev-workflow` repository is a workflow starter and must not be converted into the application. The architecture keeps ChatGPT as the primary human interface and GitHub/Notion as sources of truth for their respective data.

A critical boundary remains unverified: a tool connected inside ChatGPT must not be assumed callable from an independent Python process. Provider APIs, account permissions, authentication, costs, cancellation, revocation, and actual remote-state verification need independent checks.

## Decision

1. **Keep the existing workflow repository unchanged as the starter.** The Santi application belongs in a separate repository.
2. **Propose a small Python service** for the application core, using explicit ports/protocols and provider-specific adapters. The existing `uv` / Pytest / Ruff workflow is a candidate baseline, not a compatibility guarantee.
3. **Keep ChatGPT as the primary human interface in v1.** Do not build a custom dashboard. Do not treat ChatGPT connector availability as a standalone API contract.
4. **Use only supported provider APIs with separately authorized credentials.** Begin with read-only GitHub and Notion operations. Add writes only after sandbox contract tests verify authorization, permissions, idempotency/status reconciliation, and failure behavior.
5. **Keep the core fail-closed.** Every side-effecting call must pass a current authorization check immediately before dispatch. External content and tool outputs are untrusted data, never a source of permissions.
6. **Do not create the application repository or connect a new provider as part of this ADR.** Repository ownership/visibility and any new account, paid service, credentials, or permissions require the explicit approvals specified by the implementation plan.
7. **Treat the runtime decision as provisional until Task 1's capability matrix is reviewed.** If the required API/authentication path, cost controls, revocation, audit, or stop behavior cannot be verified, keep the affected integration disabled and revise the boundary before implementation depends on it.

## Alternatives considered

### A. Convert the existing workflow starter into the application

**Rejected.** It would mix the Santi runtime with the engineering starter, violate the plan's repository boundary, and risk disturbing the existing sample package, CI, or branch protections.

### B. Depend directly on ChatGPT-connected tools from standalone Python

**Rejected.** No verified public contract or independent authentication path has been established for that assumption. Treating an in-chat connector as a callable application API would create an unsupported critical dependency.

### C. Create a separate Python service with explicit provider adapters

**Selected as the proposed boundary.** It supports independently testable policy enforcement and clear integration boundaries while preserving ChatGPT as the user interface. It remains conditional on the capability checks and a separately approved repository.

### D. Build a custom dashboard and a larger distributed service immediately

**Deferred.** It adds operational and security surface before the supervised pilot demonstrates a need. The v1 architecture explicitly avoids a custom panel.

## Consequences

### Positive

- Existing workflow starter and its protections remain isolated.
- Authorization and verification can be tested independently of model instructions.
- Provider-specific failures can be contained behind adapters.
- Read-only access can be validated before any integration gains write capability.

### Costs and risks

- The service needs its own runtime, credential handling, monitoring, storage and recovery plan.
- API access, provider billing, permissions and terms must be verified separately from ChatGPT access.
- Some desired capabilities may be unavailable or lack hard guarantees; those functions must remain disabled or use a clearly documented safe fallback.
- The Python baseline may change if the compatibility spike finds a concrete blocker.

## Evidence and unresolved questions

The initial provider matrix is in `docs/superpowers/plans/santi-capability-matrix.md`. Official documentation establishes that provider APIs exist, but it does not establish this user's account access, approved scopes, hard spend limits, cancellation guarantees, revocation latency, retention settings, or restore capability. Those remain pending until tested in the intended environment.

## Approval and implementation gate

This ADR records a proposed technical boundary. It does **not** itself authorize:
- creating the separate application repository;
- creating accounts or paid services;
- creating, storing, or granting credentials and permissions;
- connecting new providers;
- accessing production systems or real customer data;
- launching or deploying Santi.

Before Task 2, confirm the separate repository's owner and visibility and obtain any required explicit approval. No production code should depend on an unverified critical capability.
