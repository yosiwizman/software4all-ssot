# Project intake: s4a-slice-orchestrator

_Copied from PROJECT_INTAKE_TEMPLATE.md — filled for the Phase 6 slice orchestration backend._

---

## Project name

s4a-slice-orchestrator

## Business goal

Enforce the factory's delivery discipline programmatically. Today the SSOT defines slice rules, retry policy, stop conditions, and claim classification as written doctrine. This project turns that doctrine into executable runtime logic — a state machine with transition guards, policy checks, evidence packets, and human approval gates. The result: every delivery slice flows through a verified, auditable, durable execution path. No slice skips review. No transition happens without evidence. No risky action proceeds without CEO approval.

## User / problem

- **Target user:** Factory execution layer (Paperclip, builder agents, CEO)
- **Problem:** Delivery discipline is currently enforced by convention. Agents can skip steps, miss reviews, or proceed without evidence. There is no runtime enforcement, no audit trail, and no automatic retry/rollback mechanism.
- **Current workaround:** Claude Code follows written rules in CLAUDE.md and SSOT.md. Compliance depends on the agent reading and following the rules correctly every time. There is no programmatic guard.

## Success criteria

- [ ] Slice state machine formally specified in TLA+ and model-checked by Apalache (no deadlocks, no invalid transitions)
- [ ] Core Temporal workflow implements the slice lifecycle: DRAFT → READY → BUILDING → REVIEWING → DONE
- [ ] Auxiliary states handled: FAILED, BLOCKED, ROLLBACK
- [ ] Cedar policies enforce transition guards (at least 3: "must have tests before REVIEWING", "must have CEO approval before DEPLOY", "must have evidence packet before any transition")
- [ ] Evidence packet emitted and stored at each state transition
- [ ] Human approval gate functional for at least one risky transition
- [ ] Retry/rollback paths tested with at least 2 failure scenarios
- [ ] Runs locally, no cloud dependencies
- [ ] Integrated or integratable with existing delivery pipeline (documented handoff points)

## Stack choice

| Decision | Choice | Reason |
|----------|--------|--------|
| Language | TypeScript | Approved stack, Temporal SDK available, type safety |
| Workflow runtime | Temporal | Durable execution, built-in retry/replay/resume, local dev server available |
| Policy engine | Cedar | Declarative, fast, auditable. Authored by AWS, open source. Fits transition guard pattern. |
| Formal verification | TLA+ + Apalache | Model-check state machine before implementation. Catch deadlocks, invalid transitions, starvation. |
| Proof validators (deferred) | Dafny | Mathematical proof for safety-critical guards. Not in MVP — build later when critical paths are identified. |
| Package manager | pnpm | Standard per DEC-006 |
| Container | Docker (for Temporal dev server) | Temporal dev server runs in Docker. Application code runs directly on Node.js. |
| Builder agent | Claude Code | Pre-flagged as complex — state machine, formal verification, policy engine integration. Direct Claude Code per DELIVERY_PIPELINE.md. |
| Database | SQLite (via Temporal) | Temporal dev server uses SQLite internally. No separate DB needed for MVP. |

## Repo plan

- **Repo name:** github.com/yosiwizman/s4a-slice-orchestrator
- **New repo or existing:** New
- **Branch strategy:** main only (direct push, same as SSOT — see DEC-013)
- **Monorepo or standalone:** Standalone — in `~/projects/s4a-slice-orchestrator`

## Security needs

- [ ] Handles user data (PII)? **No**
- [ ] Requires authentication? **No** (localhost only, internal factory tool)
- [ ] Requires secrets/API keys? **No**
- [x] Exposes network ports? **Yes — Temporal dev server ports (localhost only)**
- [ ] Needs HTTPS? **No** (localhost)
- [x] Other security concerns: Cedar policies must not be bypassable. Transition guards are security-critical — a skipped policy check means a slice could proceed without review.

## Build path

1. Write TLA+ formal specification of the slice state machine. Model-check with Apalache.
2. Scaffold repo from `templates/project-repo/`. Install Temporal SDK and Cedar SDK.
3. Implement core Temporal workflow: slice lifecycle state machine (DRAFT → READY → BUILDING → REVIEWING → DONE + failure states).
4. Implement Cedar policy evaluation as Temporal activity: transition guards checked before each state change.
5. Implement evidence packet emission: structured JSON artifact logged at each transition.
6. Implement human approval gate: Temporal signal-based wait for CEO approval on risky transitions.
7. Integration tests: full slice lifecycle, failure/retry scenarios, rollback.

## Test / review path

- **Unit tests:** Yes — Node.js built-in test runner. Test state transitions, policy evaluation, evidence packet structure.
- **Integration tests:** Yes — Full Temporal workflow execution with mock activities. Test happy path, failure, retry, rollback.
- **Formal verification:** TLA+ spec checked by Apalache before any implementation code is written.
- **Review agent:** Codex
- **Browser testing:** N/A (no UI in MVP)

## Deployment target

- **Where:** Local only — Temporal dev server via Docker, workflow code via Node.js
- **Domain:** N/A (localhost)
- **CI/CD:** None for MVP (manual start)

## Approval gates

| Gate | Approver | When |
|------|----------|------|
| Scope approval | CEO | Before build starts — **PENDING** |
| TLA+ spec review | CEO + Codex | After formal spec, before implementation |
| Code review | Codex | After each build slice |
| Deploy approval | CEO | Before running in factory |
| Integration approval | CEO | Before connecting to Paperclip/pipeline |

## Notes

- This is the first complex backend project in the factory. It is pre-flagged for Claude Code as primary builder.
- The TLA+ spec must be written and model-checked BEFORE any implementation code. This is non-negotiable — the state machine must be proven correct before it is coded.
- Dafny proof validators are explicitly deferred. They are approved for later use on safety-critical transition guards, but not required for MVP.
- The orchestrator sits between Paperclip and builder agents. It does not replace Paperclip. It adds an enforcement layer that Paperclip can invoke to manage slice execution.
- NASA Power of Ten rules are implementation law for all code in this project. See ROADMAP.md Phase 6 for the full list.
- Port assignments TBD — Temporal dev server uses its own ports. These must be added to CURRENT_STATE.md port ownership table when determined.
