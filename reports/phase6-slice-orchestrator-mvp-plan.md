# Phase 6 MVP Plan — s4a-slice-orchestrator

**Date:** 2026-04-09
**Project:** s4a-slice-orchestrator
**Purpose:** Enforce delivery slice discipline as executable runtime logic

---

## 1. State machine

### Primary states (happy path)

```
DRAFT → READY → BUILDING → REVIEWING → DONE
```

| State | Meaning | Who owns it |
|-------|---------|-------------|
| DRAFT | Slice defined but not yet scoped or approved | Paperclip / CTO |
| READY | Scope approved, assigned to builder | Paperclip |
| BUILDING | Builder agent actively working | OpenCode or Claude Code |
| REVIEWING | Code complete, under review | Codex |
| DONE | Review passed, tests green, evidence captured | System |

### Auxiliary states

| State | Meaning | Entry condition |
|-------|---------|-----------------|
| FAILED | Unrecoverable failure after retry budget exhausted | 3 failed attempts in BUILDING or REVIEWING |
| BLOCKED | Waiting on external input (CEO decision, missing dependency) | Policy check returns "needs_human_input" |
| ROLLBACK | Actively reverting changes from a failed slice | Entered from FAILED or by CEO command |
| APPROVAL_WAIT | Paused for human approval on a risky transition | Cedar policy flags transition as "requires_approval" |

### Transition table

| From | To | Guard (Cedar policy) | Evidence required |
|------|----|---------------------|-------------------|
| DRAFT | READY | scope_approved = true | Intake doc reference, CEO approval timestamp |
| READY | BUILDING | builder_assigned = true, no_active_slice_conflict = true | Builder agent ID, task assignment |
| BUILDING | REVIEWING | tests_pass = true, commit_exists = true | Test results, commit hash, diff stats |
| BUILDING | FAILED | retry_budget_exhausted = true | Failure log, attempt count, last error |
| BUILDING | BLOCKED | needs_human_input = true | Blocker description, question for CEO |
| REVIEWING | DONE | review_approved = true, all_tests_pass = true | Review verdict, final test results |
| REVIEWING | BUILDING | review_rejected = true | Review comments, fix instructions |
| REVIEWING | FAILED | retry_budget_exhausted = true | Failure log, review history |
| ANY | ROLLBACK | ceo_command = true OR auto_rollback_triggered = true | Rollback reason, affected files |
| ROLLBACK | DONE | rollback_complete = true | Rollback evidence, restored state |
| BLOCKED | READY | blocker_resolved = true | Resolution description, CEO response |
| FAILED | DRAFT | ceo_restart = true | Restart reason, scope adjustment if any |

### Invariants (checked by TLA+ / Apalache)

1. **No skipped review:** A slice cannot reach DONE without passing through REVIEWING.
2. **No infinite retry:** Every retry-capable state has a bounded retry count (max 3).
3. **No orphaned slices:** Every state has at least one outbound transition (no deadlocks).
4. **Evidence completeness:** Every transition produces an evidence packet (no silent state changes).
5. **Approval required for risky transitions:** Transitions flagged by Cedar policy must pass through APPROVAL_WAIT.

---

## 2. Evidence packet schema

Every state transition emits a structured evidence packet.

```json
{
  "sliceId": "string — unique slice identifier",
  "transitionId": "string — unique transition identifier (UUID)",
  "timestamp": "string — ISO 8601",
  "fromState": "string — previous state",
  "toState": "string — new state",
  "actor": "string — who/what triggered the transition (agent ID, CEO, system)",
  "policyChecks": [
    {
      "policyId": "string — Cedar policy identifier",
      "result": "ALLOW | DENY",
      "reason": "string — human-readable explanation"
    }
  ],
  "evidence": {
    "type": "string — evidence category (test_result, review_verdict, commit, approval, failure_log)",
    "data": "object — type-specific evidence payload"
  },
  "metadata": {
    "retryCount": "number — current attempt number",
    "retryBudget": "number — max allowed attempts",
    "durationMs": "number — time spent in previous state"
  }
}
```

Evidence packets are append-only. They are never modified after emission. They form the audit trail for every slice.

---

## 3. Implementation slices (4 slices, build order)

### Slice 1: TLA+ formal spec + Apalache verification

**Goal:** Prove the state machine is correct before writing any code.
**Output:** `spec/slice_machine.tla` — TLA+ specification, checked by Apalache with zero violations.
**What it checks:**
- All 5 invariants hold across all reachable states
- No deadlocks
- No invalid transitions
- Retry budget is respected
- Every path through BUILDING reaches either DONE, FAILED, or BLOCKED (no starvation)

**Verification command:** `apalache-mc check --inv=AllInvariants spec/slice_machine.tla`
**Done when:** Apalache returns 0 violations on all invariants.

### Slice 2: Core Temporal workflow + state machine

**Goal:** Implement the verified state machine as a Temporal workflow.
**Output:** TypeScript Temporal workflow that drives slice lifecycle.
**Includes:**
- Temporal dev server running in Docker (localhost)
- Workflow definition: `sliceWorkflow(sliceId, intake)` → runs the full lifecycle
- State stored as workflow state (Temporal handles durability)
- Transition function: validates current state, applies guard, emits evidence, moves to next state
- Activities: `checkPolicy()`, `emitEvidence()`, `assignBuilder()`, `waitForReview()`
- Retry configuration: max 3 attempts per building/reviewing cycle
- Signal handlers: `approveTransition`, `rejectTransition`, `rollback`, `unblock`

**Done when:** Happy path (DRAFT → READY → BUILDING → REVIEWING → DONE) executes end-to-end in local Temporal. Evidence packets emitted at each transition.

### Slice 3: Cedar policy evaluation

**Goal:** Transition guards are declarative Cedar policies, not hardcoded if-statements.
**Output:** Cedar policy files + evaluation activity.
**Includes:**
- Cedar policy set: at least 3 policies:
  1. `require_tests_before_review` — BUILDING → REVIEWING requires `tests_pass = true`
  2. `require_approval_before_deploy` — any transition flagged "risky" requires `ceo_approved = true`
  3. `require_evidence_for_all_transitions` — every transition must include a non-empty evidence packet
- Cedar evaluation as a Temporal activity: workflow calls `checkPolicy(transition)` before every state change
- DENY result blocks the transition and moves slice to BLOCKED or APPROVAL_WAIT
- Policy files stored in `policies/` directory, human-readable

**Done when:** All 3 policies are enforced. A transition that violates policy is blocked. Policy check results appear in evidence packets.

### Slice 4: Human approval gate + failure/rollback tests

**Goal:** Risky transitions pause for CEO approval. Failure paths tested.
**Output:** Approval gate mechanism + failure scenario tests.
**Includes:**
- Temporal signal-based approval: workflow waits for `approveTransition` signal on risky transitions
- Timeout on approval wait (configurable, default 24h) → moves to BLOCKED with notification
- Failure scenario tests:
  - Builder fails 3 times → slice moves to FAILED
  - Review rejects → slice returns to BUILDING (up to retry budget)
  - CEO commands rollback → slice moves to ROLLBACK → DONE
  - Blocked slice → CEO unblocks → slice resumes from READY
- Rollback activity: produces rollback evidence packet

**Done when:** Approval gate works end-to-end. All 4 failure scenarios pass integration tests.

---

## 4. Build-now vs defer

### Build now (MVP)

| Component | Why now |
|-----------|---------|
| TLA+ state machine spec | Non-negotiable — prove correctness before coding |
| Temporal workflow (core lifecycle) | The execution backbone — everything else depends on it |
| Cedar policy evaluation (3 policies) | Transition guards are the security boundary — must exist from day 1 |
| Evidence packet emission | Audit trail is the proof mechanism — required for every transition |
| Human approval gate | CEO involvement doctrine requires this for risky transitions |
| Retry/rollback paths | Factory stop conditions require bounded retries and safe failure |
| Integration tests | Verification doctrine requires proof, not intent |

### Defer (not in MVP)

| Component | Why defer | When to build |
|-----------|-----------|---------------|
| Dafny proof validators | Mathematical proof is valuable but not blocking. TLA+ model checking provides sufficient assurance for MVP. | After MVP — apply to safety-critical guards where formal proof adds value beyond model checking |
| Paperclip integration | The orchestrator must work standalone first. Integration is a separate scope. | After MVP is proven, Phase 6B or Phase 7 |
| Dashboard/UI for slice status | Operators can read evidence packets and Temporal UI for now | After MVP, potentially extend s4a-factory-dashboard |
| Multi-slice concurrency | MVP handles one slice at a time. Concurrent execution adds scheduling complexity. | After single-slice path is solid |
| Persistent evidence store | MVP evidence packets go to stdout/file. A durable store (SQLite, etc.) is future work. | After MVP, when audit trail querying is needed |
| Advanced Cedar policies | 3 policies for MVP. More granular policies (per-agent limits, per-project rules) come later. | As real policy needs emerge from factory operation |

---

## 5. Subsystem stack detail

### Temporal

- **Role:** Durable workflow execution. Handles retry, replay, resume, timeouts, signals.
- **Local setup:** `temporal server start-dev` via Docker. Uses SQLite internally. No cloud dependency.
- **SDK:** `@temporalio/client`, `@temporalio/worker`, `@temporalio/workflow`, `@temporalio/activity`
- **Why Temporal:** The slice lifecycle is inherently a long-running, stateful, failure-prone process. Temporal was designed for exactly this. It provides durable execution (survives process crashes), built-in retry with configurable policies, signal-based human interaction, and replay for debugging.

### Cedar

- **Role:** Declarative policy evaluation for transition guards.
- **SDK:** `@cedar-policy/cedar-wasm` (WebAssembly Cedar engine for Node.js)
- **Why Cedar:** Policies should be declarative and auditable, not buried in if-statements. Cedar policies are human-readable, version-controlled, and evaluable without code changes. Cedar is fast (sub-millisecond), deterministic, and designed for authorization decisions.

### TLA+ + Apalache

- **Role:** Formal specification and model checking of the state machine BEFORE implementation.
- **Tool:** Apalache model checker (runs locally, no cloud)
- **Why TLA+:** The state machine has 8+ states, 12+ transitions, and 5 invariants. Manual reasoning about all possible interleavings is error-prone. TLA+ lets us specify the machine formally and have Apalache exhaustively check that invariants hold in all reachable states. This catches deadlocks, starvation, and invalid transitions that tests alone cannot.

### Dafny (deferred)

- **Role:** Mathematical proof of correctness for safety-critical transition guards.
- **When:** After MVP. Applied selectively to guards where formal proof adds value beyond model checking (e.g., "a slice can never reach DONE without evidence" proved as a mathematical theorem, not just checked by exhaustive search).
- **Why defer:** TLA+ model checking is sufficient for MVP assurance. Dafny requires learning a new language and proof methodology. The cost/benefit ratio improves after the state machine is stable and critical paths are identified through real usage.

---

## 6. Alignment with existing factory

| Factory component | Relationship to s4a-slice-orchestrator |
|-------------------|---------------------------------------|
| SSOT (this repo) | Doctrine source. The orchestrator enforces rules defined here. It does not define new rules. |
| Paperclip | Control plane. The orchestrator is a subsystem that Paperclip can invoke. It does not replace Paperclip. |
| OpenCode | Default builder. The orchestrator assigns slices to OpenCode first, per DELIVERY_PIPELINE.md. |
| Claude Code | Escalation builder. The orchestrator escalates to Claude Code after 2 OpenCode failures, per DELIVERY_PIPELINE.md. |
| Codex | Reviewer. The orchestrator routes completed slices to Codex for review, per DELIVERY_PIPELINE.md. |
| CEO | Approval authority. The orchestrator pauses for CEO approval on risky transitions, per AGENT_ROLE_MATRIX.md. |
| s4a-factory-dashboard | Status display. The dashboard could display slice status in a future integration. |
