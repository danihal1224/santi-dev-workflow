# Santi v1 Implementation Plan

> **For agentic workers:** Use the approved task-by-task implementation workflow with independent review at each phase. Execute tasks in order; do not skip gates. This plan is not authorization to implement until the plan itself is reviewed and approved.

**Goal:** Deliver a controlled, testable first version of Santi focused on software development, with verifiable authorization, safe execution, observability, and a supervised pilot.

**Architecture:** Use a separate application repository for Santi rather than converting `danihal1224/santi-dev-workflow`, which remains the engineering-workflow starter. ChatGPT remains the primary user interface; Notion and GitHub retain their defined source-of-truth roles. Implement a small core and explicit adapters, with independent policy enforcement before tools can perform sensitive actions.

**Tech Stack:** Proposed baseline: Python and the existing `uv` / Pytest / Ruff engineering workflow, subject to a compatibility and provider-capability spike before runtime implementation. Do not assume that ChatGPT connectors are callable from an independent application; verify supported APIs, permissions, authentication, costs, and terms first.

**Spec:** `docs/superpowers/specs/2026-10-12-santi-architecture-design.md`

## Global Constraints

- The first specialty is software development; do not implement all future specialties.
- ChatGPT remains the primary interface in v1; no custom dashboard.
- No autonomous production deployment in v1.
- No unapproved tools, providers, credentials, integrations, or permissions.
- Use least privilege, separate environments, and a technical authorization check independent of model instructions.
- Never treat external content or tool output as authority to grant permissions.
- Never claim completion without verifiable evidence.
- Do not retry a potentially non-idempotent operation until its actual state has been checked.
- Stop or pause when a critical authorization, security, cost, provider-capability, or state-verification requirement cannot be satisfied.
- Permanent behavior, architecture, permission, security, budget, and scope changes require approval according to impact.
- Do not store secrets in source control, prompts, memory, or ordinary logs.
- Numeric budget, concurrency, retention, RPO/RTO, retry and pilot thresholds must be based on verified capabilities and pilot evidence, not invented.
- The current `danihal1224/santi-dev-workflow` repository remains a starter/workflow repository. Do not rename or replace its `bonacci` sample package as part of this work.
- This plan and the approved specification do not themselves authorize implementation, external account creation, production access, or launch.

## Review Focus

1. **Provider/API capability mismatch:** test the actual account and environment, not only public documentation; record the exact evidence and fallback.
2. **Prompt injection through external content:** prove retrieved documents and tool results cannot grant permissions or bypass the policy layer.
3. **Duplicate external actions after timeout:** simulate a timeout after the remote side effect and prove the system checks remote state before retrying.
4. **Stale authorization after pause or incident:** prove scope changes, expiration, revocation, or relevant incidents invalidate an approval when required.
5. **False success and partial completion:** simulate tool errors, ambiguous responses, and unavailable verification; state must remain partial, blocked, failed, or pending verification rather than complete.

---

## Phase 0: Verify feasibility and lock the implementation boundary

### Task 1: Build the provider and integration capability matrix

**Files:**

- Create: `docs/superpowers/plans/santi-capability-matrix.md`
- Create: `docs/superpowers/decisions/ADR-071-runtime-and-integration-boundary.md`

**Interfaces:**

- Consumes: the approved architecture specification and the approved tool/provider catalog.
- Produces: a reviewed matrix classifying every critical capability as `confirmed`, `pending`, or `unavailable`; a proposed runtime boundary and a list of blockers.

- [ ] **Step 1: Inventory requirements** for authorization, tool execution, GitHub, Notion, model access, budget controls, cancellation, notifications, audit, backup and restore.
- [ ] **Step 2: Gather primary-source evidence** for each provider, including account/plan requirements, authentication, permissions, limits, cost model, retention, cancellation, revocation and status verification.
- [ ] **Step 3: Mark unverified items as pending**; do not infer that a ChatGPT-connected tool is callable from a standalone Python process.
- [ ] **Step 4: Test only approved, non-sensitive capabilities** in a sandbox; record date, environment, expected behavior and observed behavior.
- [ ] **Step 5: Decide the runtime and adapter boundary** using evidence. If a critical requirement cannot be met, record a safe alternative or block it.
- [ ] **Step 6: Review the matrix** and confirm that no critical capability remains assumed.

**Acceptance:** no critical runtime dependency is classified as confirmed without evidence appropriate to that capability. No new provider connection, paid service, or external account is created as part of this task.

### Task 2: Establish a dedicated application repository and engineering baseline

**Files:**

- Create in the new application repository: `AGENTS.md`, `README.md`, `pyproject.toml`, `uv.lock`, `.python-version`, `.github/workflows/ci.yaml`, PR template, and minimal package/test layout.
- Keep unchanged: `danihal1224/santi-dev-workflow` sample source, CI and branch protection.

**Interfaces:**

- Consumes: Task 1's verified runtime decision and the existing engineering guardrails.
- Produces: a dedicated, protected repository with reproducible setup, CI, lint, tests, build, secret scanning where available, and a documented local workflow.

- [ ] **Step 1: Define repository ownership and visibility** and obtain any required explicit approval before creating it.
- [ ] **Step 2: Bootstrap from a suitable maintained starter** only after confirming Python/runtime compatibility and licensing.
- [ ] **Step 3: Add least-privilege CI permissions**, branch protection, PR template, and repository instructions.
- [ ] **Step 4: Add a minimal health/version module and one passing test**; do not add integrations yet.
- [ ] **Step 5: Run the full test, lint/format and build commands** in CI and verify branch protections.
- [ ] **Step 6: Review the repository for secrets, excessive permissions, unnecessary dependencies and unintended sample code.**

**Acceptance:** the new repository is separate from the workflow starter, reproducible from its lockfile, protected by required checks, and contains no credentials or unreviewed provider integration.

## Phase 1: Build the safety-critical core

### Task 3: Define task, result and authorization contracts

**Files:**

- Create: `src/santi_core/models.py`
- Create: `src/santi_core/authorization.py`
- Test: `tests/santi_core/test_models.py`
- Test: `tests/santi_core/test_authorization.py`

**Interfaces:**

- `AuthorizationRequest`: actor, action, resource, environment, scope, requested limits and context identifier.
- `AuthorizationDecision`: allow/deny, reason code, authorization identifier and applicable limits; no secret material.
- `TaskStatus`: `pending`, `running`, `completed`, `partial`, `blocked`, `failed`, `pending_verification`, `cancelled`.
- `authorize(request, registry, now) -> AuthorizationDecision`.

- [ ] **Step 1: Write tests** for missing permission, expired permission, revoked permission, scope mismatch, wrong environment, budget limit, valid scoped permission and invalid timestamps.
- [ ] **Step 2: Run tests and verify they fail** for the expected missing behavior.
- [ ] **Step 3: Implement immutable typed models and deterministic policy evaluation**; default to deny.
- [ ] **Step 4: Run focused tests**, including boundary times and malformed input.
- [ ] **Step 5: Run all tests and static checks** and review the diff.
- [ ] **Step 6: Commit the completed contract and policy evaluator** on a feature branch.

**Acceptance:** no ambiguous or missing authorization can yield allow; external text cannot directly construct a trusted authorization record.

### Task 4: Add the authorization registry and revocation boundary

**Files:**

- Create: `src/santi_core/authorization_store.py`
- Create: `src/santi_core/revocation.py`
- Test: `tests/santi_core/test_authorization_store.py`
- Test: `tests/santi_core/test_revocation.py`

**Interfaces:**

- `get_authorization(authorization_id) -> AuthorizationRecord | None`.
- `revoke_authorization(authorization_id, reason, actor) -> RevocationResult`.
- `is_revoked(authorization_id) -> bool`.

- [ ] **Step 1: Test lookup, revocation, repeated revocation, unknown identifiers and storage errors.**
- [ ] **Step 2: Implement a storage adapter using the storage option approved in Task 1**; do not invent a provider or expose a public unauthenticated endpoint.
- [ ] **Step 3: Ensure every sensitive execution path rechecks current authorization**, not a stale cached allow decision.
- [ ] **Step 4: Test revocation during pause, task resumption and concurrent requests.**
- [ ] **Step 5: Run security tests and document storage durability and limitations.**
- [ ] **Step 6: Commit after reviewing the diff.**

**Acceptance:** revocation blocks subsequent sensitive actions; if registry availability or integrity cannot be established, affected actions fail closed.

### Task 5: Implement task state, checkpoints and safe resumption

**Files:**

- Create: `src/santi_core/task_state.py`
- Create: `src/santi_core/checkpoints.py`
- Test: `tests/santi_core/test_task_state.py`
- Test: `tests/santi_core/test_checkpoints.py`

**Interfaces:**

- `transition_task(task_id, expected_status, new_status, evidence) -> TaskRecord`.
- `save_checkpoint(task_id, checkpoint) -> CheckpointRecord`.
- `load_checkpoint(task_id) -> CheckpointRecord | None`.

- [ ] **Step 1: Test allowed and forbidden transitions**, including terminal states, partial outcomes and pending verification.
- [ ] **Step 2: Test restart recovery, stale checkpoints, corrupted records and concurrent updates.**
- [ ] **Step 3: Implement explicit transitions and checkpoint versioning.**
- [ ] **Step 4: Verify resumption rechecks live state and current authorization before any side effect.**
- [ ] **Step 5: Run regression tests and commit.**

**Acceptance:** interrupted tasks resume only from a validated checkpoint; no checkpoint alone is treated as proof that a remote operation completed.

## Phase 2: Controlled tools and truthful results

### Task 6: Create the tool catalog and execution gateway

**Files:**

- Create: `src/santi_core/tool_catalog.py`
- Create: `src/santi_core/execution_gateway.py`
- Create: `src/santi_core/tool_protocols.py`
- Test: `tests/santi_core/test_tool_catalog.py`
- Test: `tests/santi_core/test_execution_gateway.py`

**Interfaces:**

- `ToolCapability`: name, supported actions, scopes, required permissions, cost metadata, verification method and capability status.
- `execute_tool(tool_name, action, request, authorization_id) -> ToolExecutionResult`.
- `ToolExecutionResult`: operation identifier, execution status, external reference when safe, verification status and redacted error.

- [ ] **Step 1: Test unknown tool, unapproved capability, scope mismatch, revoked permission, cost cap and tool exception.**
- [ ] **Step 2: Implement an allowlisted registry and authorization check immediately before dispatch.**
- [ ] **Step 3: Add timeouts and bounded retries only for operations classified as safe to retry.**
- [ ] **Step 4: Require a status-verification method for side-effecting operations; mark unverifiable results as pending verification.**
- [ ] **Step 5: Test prompt-injection payloads in tool inputs and results; ensure they cannot alter permissions.**
- [ ] **Step 6: Commit after tests and review.**

**Acceptance:** tools outside the approved catalog cannot execute; no tool result or retrieved instruction can grant access; uncertain side effects are not blindly retried.

### Task 7: Implement budget and concurrency guards

**Files:**

- Create: `src/santi_core/budgets.py`
- Create: `src/santi_core/concurrency.py`
- Test: `tests/santi_core/test_budgets.py`
- Test: `tests/santi_core/test_concurrency.py`

**Interfaces:**

- `check_budget(project_id, estimated_cost) -> BudgetDecision`.
- `reserve_budget(project_id, operation_id, estimated_cost) -> ReservationResult`.
- `release_or_reconcile_budget(operation_id, actual_cost) -> BudgetRecord`.
- `acquire_execution_slot(project_id, tool_name) -> SlotResult`.

- [ ] **Step 1: Test below-limit, near-limit, at-limit, unknown cost, duplicate reservation and failed reconciliation.**
- [ ] **Step 2: Test per-project and per-tool concurrency, resource exhaustion and slot release after failure.**
- [ ] **Step 3: Implement conservative reservations and pause before new cost-incurring actions at the configured cap.**
- [ ] **Step 4: Ensure alerts are never represented as hard caps unless the underlying control enforces them.**
- [ ] **Step 5: Run concurrent stress tests and commit.**

**Acceptance:** limits are enforced by code where possible; where a provider lacks hard caps, the limitation is explicit and the system applies the approved conservative fallback.

### Task 8: Add structured audit events and redaction

**Files:**

- Create: `src/santi_core/audit.py`
- Create: `src/santi_core/redaction.py`
- Test: `tests/santi_core/test_audit.py`
- Test: `tests/santi_core/test_redaction.py`

**Interfaces:**

- `AuditEvent`: event ID, task/project IDs, timestamp, action, tool, authorization reference, outcome, verification and redacted metadata.
- `record_event(event) -> AuditRecordResult`.
- `redact_sensitive(value) -> RedactedValue`.

- [ ] **Step 1: Test required event fields, append failure, access-denied cases and retention metadata.**
- [ ] **Step 2: Test secrets, tokens, personal data and nested tool errors are redacted.**
- [ ] **Step 3: Implement append-oriented structured records using the approved store from Task 1.**
- [ ] **Step 4: Ensure audit logs are not imported into personal memory automatically.**
- [ ] **Step 5: Test that important actions can be reconstructed without revealing secrets; commit.**

**Acceptance:** important operations are traceable, secrets are not written to ordinary logs, and logging failures on critical operations trigger the defined safe behavior.

## Phase 3: The first useful software-development workflow

### Task 9: Implement the software-development module

**Files:**

- Create: `src/santi_modules/software_development/workflow.py`
- Create: `src/santi_modules/software_development/acceptance.py`
- Test: `tests/santi_modules/software_development/test_workflow.py`
- Test: `tests/santi_modules/software_development/test_acceptance.py`

**Interfaces:**

- `plan_change(request, repository_context) -> WorkPlan`.
- `validate_change(plan, evidence) -> AcceptanceReport`.
- `WorkPlan` contains requirements, affected areas, tasks, dependencies, risks and approval gates.
- `AcceptanceReport` contains passed, failed and unverified criteria with evidence references.

- [ ] **Step 1: Test ambiguous requirements, no repository context, protected branches and changes requiring design approval.**
- [ ] **Step 2: Implement a workflow that proposes a plan before edits and requires approval for architectural changes.**
- [ ] **Step 3: Require test, lint, security and build evidence appropriate to the change.**
- [ ] **Step 4: Test false-success scenarios and ensure unrun checks are marked pending.**
- [ ] **Step 5: Commit the module without yet granting it production or deployment capabilities.**

**Acceptance:** the module can prepare and verify a bounded software change but cannot bypass repository protections or claim unrun checks passed.

### Task 10: Add GitHub and Notion adapters only where capability checks passed

**Files:**

- Create: `src/santi_adapters/github_adapter.py`
- Create: `src/santi_adapters/notion_adapter.py`
- Test: `tests/santi_adapters/test_github_adapter.py`
- Test: `tests/santi_adapters/test_notion_adapter.py`

**Interfaces:**

- Each adapter implements the protocols from Task 6 and exposes only explicitly allowlisted actions.
- Adapter responses are normalized into typed results with remote identifiers and verification state.

- [ ] **Step 1: Write adapter contract tests using fakes before connecting to a real account.**
- [ ] **Step 2: Verify the approved authentication method, minimum scopes and rate limits from Task 1.**
- [ ] **Step 3: Implement read-only repository/context operations first.**
- [ ] **Step 4: Add branch and PR operations only after tests verify authorization and remote-state reconciliation.**
- [ ] **Step 5: Add Notion reads/writes only to the approved project space and document types.**
- [ ] **Step 6: Test timeouts, rate limits, permission denial, duplicate requests and provider schema changes.**
- [ ] **Step 7: Run contract tests and commit.**

**Acceptance:** only approved resources and actions are accessible; adapters never silently substitute a less-safe provider or scope.

## Phase 4: Continuity, notifications and backup

### Task 11: Add alert routing and incident containment

**Files:**

- Create: `src/santi_core/notifications.py`
- Create: `src/santi_core/incidents.py`
- Test: `tests/santi_core/test_notifications.py`
- Test: `tests/santi_core/test_incidents.py`

**Interfaces:**

- `classify_alert(event) -> AlertPriority`.
- `dispatch_alert(alert, approved_channel) -> DeliveryResult`.
- `contain_incident(incident) -> ContainmentResult`.

- [ ] **Step 1: Test critical, blocker, routine and duplicate alerts.**
- [ ] **Step 2: Test unavailable channels, unknown delivery status and unauthorized channels.**
- [ ] **Step 3: Implement priority-based routing using only verified approved channels.**
- [ ] **Step 4: Implement bounded containment of affected tasks/tools, preserving evidence and allowing independent tasks only when verified unaffected.**
- [ ] **Step 5: Test stop/revoke behavior and commit.**

**Acceptance:** critical alerts are prioritized; delivery is not claimed without confirmation; incident containment does not expand permissions.

### Task 12: Define and test backup/restore

**Files:**

- Create: `docs/operations/backup-and-restore.md`
- Create: `src/santi_core/backup.py`
- Test: `tests/santi_core/test_backup.py`

**Interfaces:**

- `create_backup(backup_scope) -> BackupResult`.
- `verify_backup(backup_id) -> BackupVerification`.
- `restore_backup(backup_id, target) -> RestoreResult`.

- [ ] **Step 1: Define backup scope, retention, access and deletion policy based on the approved store and data classification.**
- [ ] **Step 2: Test corrupted, incomplete and unauthorized backup/restore attempts.**
- [ ] **Step 3: Implement a provider-specific backup adapter only if Task 1 confirms the capability.**
- [ ] **Step 4: Perform a restore test in an isolated environment and record evidence.**
- [ ] **Step 5: Set RPO/RTO only after observed restoration and recovery tests; commit.**

**Acceptance:** a backup is not considered valid until integrity is checked; a recovery claim requires a successful restore test.

## Phase 5: Integration, supervised pilot and launch decision

### Task 13: Add end-to-end and adversarial tests

**Files:**

- Create: `tests/integration/test_authorized_workflow.py`
- Create: `tests/integration/test_failure_recovery.py`
- Create: `tests/security/test_prompt_injection.py`
- Create: `tests/security/test_permission_bypass.py`
- Create: `tests/security/test_false_completion.py`

- [ ] **Step 1: Test a normal bounded software-development task from request through verified result.**
- [ ] **Step 2: Inject expired/revoked permission, prompt injection, cost limit, provider timeout and unavailable status verification.**
- [ ] **Step 3: Simulate a timeout after a remote side effect and prove the system reconciles state before retrying.**
- [ ] **Step 4: Simulate audit store failure, notification failure and partial provider outage.**
- [ ] **Step 5: Verify that critical failures block the affected action and cannot be overridden by model-generated text.**
- [ ] **Step 6: Run the full CI suite and review every failed or skipped check.**

**Acceptance:** no unauthorized action, critical false completion, unsafe duplicate, or bypass of stop/revocation controls passes the release gate.

### Task 14: Run the isolated pilot and collect evidence

**Files:**

- Create: `docs/operations/pilot-protocol.md`
- Create: `docs/operations/pilot-results.md`
- Create: `docs/operations/residual-risks.md`

- [ ] **Step 1: Choose one bounded non-production software project and synthetic or appropriately minimized data.**
- [ ] **Step 2: Record baseline quality, task duration, manual interventions, failures and costs.**
- [ ] **Step 3: Run supervised tasks covering normal behavior, ambiguous requests, denied permissions, provider failure and recovery.**
- [ ] **Step 4: Record actual costs, quotas, concurrency, retention and restoration measurements.**
- [ ] **Step 5: Review every incident, false completion, authorization denial and manual intervention.**
- [ ] **Step 6: Set evidence-based numeric thresholds and document residual risks.**
- [ ] **Step 7: Stop the pilot if a launch blocker appears; do not expand scope to compensate.**

**Acceptance:** the pilot report includes evidence and limitations; unmeasured metrics remain explicitly unknown.

### Task 15: Prepare launch evidence, do not auto-launch

**Files:**

- Create: `docs/operations/release-readiness.md`

- [ ] **Step 1: Map every critical spec requirement to an implementation component and test/evidence reference.**
- [ ] **Step 2: Verify all provider capabilities used by the release and list unavailable or pending ones.**
- [ ] **Step 3: Verify stop/revocation, cost limits, backup restore, audit reconstruction and safe recovery.**
- [ ] **Step 4: Confirm no critical security or authorization failures remain open.**
- [ ] **Step 5: Prepare a release recommendation with costs, limitations and residual risks.**
- [ ] **Step 6: Obtain explicit human approval for the exact version, environment and scope before launch.**

**Acceptance:** release readiness is a reviewed evidence package, not an automatic deployment action. No production deployment occurs as part of this plan's v1.

## Self-review against the approved specification

- **Architecture and module boundaries:** Tasks 1–2 lock the runtime boundary and separate the product repo; Tasks 3–10 build core policy, state, adapters and the initial software module.
- **Authorization, revocation and instruction hierarchy:** Tasks 3–6 and 13 test default-deny, scoped grants, revocation and hostile external content.
- **Quality, truthful completion and recovery:** Tasks 5–6, 9 and 13 cover task states, remote status reconciliation and false-success prevention.
- **Costs, concurrency and audit:** Tasks 7–8 test limits, concurrent use, redaction and reconstructable records.
- **Privacy, backups and continuity:** Tasks 8, 10–12 define data handling, incident response, and tested restoration; numeric retention/RPO/RTO remain evidence-based.
- **Provider verification and portability:** Task 1 is a blocking prerequisite; Tasks 6, 10 and 12 cannot assume unavailable capabilities.
- **Communication and human oversight:** Tasks 11, 14 and 15 provide priority alerts, supervised validation and explicit launch approval.
- **Regression and security:** Tasks 3–13 include unit, integration, adversarial and regression tests; critical failures block progression.
- **Known boundary:** this plan does not implement a custom UI, additional specialties, autonomous production deployment, self-modifying policies, or unapproved provider connections.

## Execution gates

- **Gate A:** Task 1 reviewed; no critical capability is assumed.
- **Gate B:** dedicated repository, branch protections and CI verified.
- **Gate C:** authorization, revocation, task-state and safe-execution tests pass before live tool adapters are enabled.
- **Gate D:** end-to-end and adversarial tests pass before a supervised pilot.
- **Gate E:** pilot evidence, residual risks and explicit approval exist before launch.

**Current status:** plan prepared for human review. No application implementation has been started by this plan document.
