# Santi v1 — provider and integration capability matrix

**Assessment date:** 2026-10-12  
**Status:** Initial desk review; account-specific and runtime tests are not yet performed.  
**Purpose:** Prevent implementation from depending on capabilities that are only assumed.

## Status meanings

- **confirmed (documented):** official documentation describes the capability. This does not prove the user's account, token, plan, or target environment can use it.
- **pending:** account-specific access, behavior, cost, or operational testing is still required.
- **unavailable:** evidence shows the capability cannot be provided in the intended environment. No critical capability is currently classified this way without a target-environment test.

## Capability matrix

| Capability | Documented evidence / current finding | Status | Required verification before relying on it | Safe fallback |
|---|---|---|---|---|
| Independent authorization policy | Can be implemented in Santi's own runtime; no existing Santi runtime has been created or tested yet. | pending | Tests must prove deny-by-default, scope/environment checks, expiry, revocation, and authorization re-check immediately before each side effect. | Do not dispatch side-effecting tools. |
| Tool catalog and execution gateway | A local allowlist and gateway are design choices, not an existing verified capability. | pending | Contract tests for unknown tools, stale permissions, prompt injection, timeouts, idempotency, and result verification. | Disable the affected tool. |
| GitHub API read access | Official REST API supports scoped authentication; exact endpoint permissions vary by token type and endpoint. [GitHub authentication](https://docs.github.com/en/rest/authentication/authenticating-to-the-rest-api) and [fine-grained token permissions](https://docs.github.com/en/rest/authentication/permissions-required-for-fine-grained-personal-access-tokens). | documented; account access pending | Verify repository allowlist, authentication method, minimum permissions, read operations, rate limits, and access-denied behavior in a sandbox. | Manual review; no adapter writes. |
| GitHub branch/PR writes | REST API exposes branch and pull-request operations, with endpoint-specific write permissions. [Pull request API](https://docs.github.com/en/rest/pulls/pulls). | documented; account access and controls pending | In a dedicated test repository, verify branch creation, PR creation, protected-branch behavior, and reconciliation after timeouts. Do not change existing repository protections. | Read-only GitHub adapter. |
| Notion API reads/writes | The public API documents bearer authentication, versioned requests, and access failures when a connection cannot access a resource. [Notion API reference](https://developers.notion.com/reference). | documented; workspace access pending | Verify connection identity, exact approved pages/data sources, minimum access, version, rate limits, and read/write behavior in a test space. | Read-only or disabled Notion adapter. |
| Model API access | OpenAI publishes API reference material; the user's project-specific API access, key availability, project limits, retention and billing have not been inspected. [OpenAI API usage and costs](https://platform.openai.com/docs/api-reference/usage). | pending | Confirm approved provider/model, project access, authentication, applicable terms, data handling, rate limits, cost visibility and error behavior. Never use ChatGPT session credentials as API credentials. | No autonomous model calls; keep ChatGPT as the manual interface. |
| Hard spend limits | Usage/cost visibility is documented, but visibility is not proof of a provider-enforced hard cap. | pending | Verify whether the selected provider/account enforces a hard limit and what happens to in-flight requests. Distinguish hard blocks from alerts. | Local conservative budget reservations and pause before new paid calls; disclose any remaining exposure. |
| Cancellation / stop | No target runtime or provider cancellation behavior has been tested. A client timeout does not prove a remote side effect was cancelled. | pending | Test cancellation before dispatch, during execution, after timeout, and after remote side effect; verify actual remote state. | Stop issuing new operations, reconcile state, and mark uncertain outcomes `pending_verification`. |
| Revocation | GitHub/Notion access is controlled by provider permissions, while Santi also needs its own live authorization registry. Neither end-to-end revocation nor registry durability is implemented. | pending | Verify provider token revocation and Santi registry revocation independently, including concurrent requests and resumed tasks. | Fail closed for affected actions. |
| Audit and redaction | Can be implemented in the application, but no store, retention setting, access policy or redaction behavior has been tested. | pending | Test nested secrets/PII, append failures, access control, retention and reconstruction of significant actions. | Do not perform sensitive actions without required audit guarantees. |
| Notifications and delivery confirmation | No approved external notification channel or delivery receipt has been verified. | pending | Verify channel approval, authorization, deduplication, delivery receipt and unavailable-channel behavior. | Report status in the active ChatGPT conversation only; never claim external delivery. |
| Backup and restore | No runtime store exists, so no backup or restore capability can yet be confirmed. | pending | Select a store only after the runtime spike; test integrity, unauthorized restore, isolated restoration, retention and deletion. | Do not persist critical state until a recoverable store is validated. |
| Isolated test environment | No dedicated Santi application repository or sandbox has been created. | pending | Establish a dedicated repository and isolated test configuration, with no production credentials or live side effects. | Contract tests with fakes only; no real external writes. |
| ChatGPT connectors callable from standalone Python | No evidence establishes that connectors available inside ChatGPT are callable by an independent Python process. | pending; must not be assumed | Identify a supported public API and independently authorized authentication path for each required integration. | Use explicit provider APIs/adapters or manual handoff; never reuse ChatGPT session credentials. |
| Python/uv/Pytest/Ruff baseline | The existing workflow repository uses a Python starter, but this does not prove compatibility of a future Santi app or its dependencies. | documented in starter; new app compatibility pending | Run a compatibility spike and reproducible install/test/lint/build in the new app repository after its creation is authorized. | Keep the existing workflow starter unchanged; adjust the new app baseline only with evidence. |

## Initial runtime boundary

1. Keep `danihal1224/santi-dev-workflow` as an engineering-workflow starter; do not turn it into the Santi application.
2. The proposed application boundary is a separate Python service/repository with explicit provider adapters and a policy-enforcing core. This is a design decision, not a claim that the service exists.
3. ChatGPT remains the primary human interface in v1. Do not assume ChatGPT's connected tools can be called from the standalone service.
4. Use GitHub and Notion adapters only through their supported APIs and independently authorized credentials. Start with read-only operations.
5. Do not create a repository, external account, paid service, credential, or new provider connection as part of this assessment.
6. No adapter may perform a side effect until its account access, minimum permissions, status verification, revocation path and failure behavior have been tested.
7. Unknown budget enforcement, cancellation, revocation, audit durability, backup/restore, and notification delivery remain launch blockers until evidence resolves them.

## Test record

| Check | Result |
|---|---|
| Public documentation review | Partial desk review completed on 2026-10-12; references above are primary provider documentation. |
| Access to user's actual GitHub/Notion/model-provider account settings | Not verified by this assessment. |
| Sandbox read/write contract tests | Not run; no dedicated Santi application sandbox exists yet. |
| Cost cap, cancellation, revocation and restore tests | Not run. |
| Critical assumptions represented as confirmed without evidence | None intentionally classified as confirmed. |

## Exit criteria

Task 1 is ready for review only when every critical row has evidence appropriate to the claim, or remains explicitly pending with a safe fallback and a clear blocker. A documented API endpoint alone does not satisfy account-specific verification.
