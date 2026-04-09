# Software 4 All — Roadmap

## Overview

Phased plan from initial setup to a functioning AI software factory. Each phase has clear goals, outputs, and exit criteria. Phases are sequential — do not skip ahead.

---

## Phase 0: SSOT Foundation — COMPLETE

**Goal:** Establish the canonical documentation and governance structure so all future work has a reference point.

**Outputs:**
- This repo created and populated
- All canonical docs written with real content
- Decision log initialized
- Agent roles defined
- Delivery pipeline documented

**Exit criteria:**
- [x] All 11 required docs exist with substantive content
- [ ] CEO has reviewed and approved the operating model
- [x] Repo is committed and pushed

---

## Phase 1: Security and Machine Baseline — COMPLETE

**Goal:** Lock down the machine, close known gaps, establish security standards as enforceable practice.

**Outputs:**
- UFW firewall active (deny incoming, allow outgoing, enabled on startup)
- Ports 3000/3002 exposure resolved
- Remote access consolidated to RustDesk (DEC-014)
- Secrets management policy baseline established (DEC-015)
- Runtime port ownership formalized — S4A on 3100, Jarvis on 3000-3002 (DEC-016)
- CURRENT_STATE.md updated with confirmed security state

**Exit criteria:**
- [x] UFW active with default-deny incoming
- [x] No unexpected ports exposed to network
- [x] Single remote access method chosen and documented
- [x] Secrets management approach decided and logged in DECISIONS_LOG.md

---

## Phase 2: Runtime Completion — COMPLETE

**Goal:** Install all missing approved tools so the factory has a complete runtime.

**Outputs:**
- Docker installed and verified
- uv installed and verified
- CUDA/cuDNN audit completed and upgrade path documented
- GPU routing rules documented
- CURRENT_STATE.md and INSTALL_BASELINE.md updated

**Progress (2026-04-09):**
- uv v0.11.6 installed and verified
- CUDA/cuDNN audit completed — CUDA 12.0 toolkit functional, upgrade to 13.x deferred (not trivial, no project currently requires it). Ollama bundles its own CUDA 13 + cuDNN 9.20 internally.
- Docker 29.1.3 installed by CEO, verified (`hello-world` passed)
- GPU routing rules documented (DEC-018) — GPU0 primary compute, GPU1 overflow, GPU2 display-reserved

**Exit criteria:**
- [x] Docker installed and `docker run hello-world` succeeds (v29.1.3)
- [x] `uv --version` succeeds (v0.11.6)
- [x] CUDA/cuDNN status audited and documented (upgrade deferred by recommendation, see CURRENT_STATE.md)
- [x] All agents in AGENT_ROLE_MATRIX.md have working runtimes (spot-checked 2026-04-09)
- [x] GPU routing rules documented (DEC-018, see CURRENT_STATE.md)

---

## Phase 3: Standardization — COMPLETE

**Goal:** Ensure consistent project structure, repo conventions, and workspace organization.

**Outputs:**
- Standard project repo template created (`templates/project-repo/`)
- Workspace directory structure documented (CURRENT_STATE.md, STACK_STANDARD.md)
- AKIOR repos verified as distinct (governance vs application) — not duplicates
- pnpm/bun path standardization resolved and documented
- Workspace directory conventions established (STACK_STANDARD.md)
- Paperclip runtime/CLI naming clarified

**Phase 3A (2026-04-09):**
- Workspace audit completed — all repos catalogued in CURRENT_STATE.md
- AKIOR "duplicate" repos investigated — found to be two distinct repos (governance vs application), not duplicates. Risk entry corrected.
- pnpm install path documented (npm global, not corepack). Bun confirmed runtime-only (no global installs).
- Paperclip runtime/CLI naming clarified — no global binary, runs via `pnpm dev`, CLI via `pnpm paperclipai`.
- Undocumented port 13100 (Paperclip secondary listener) added to port ownership table.
- Workspace directory conventions added to STACK_STANDARD.md.

**Phase 3B (2026-04-09):**
- Standard project repo template created at `templates/project-repo/`
- Template includes: README.md, CLAUDE.md, .gitignore, docs/, TEMPLATE_USAGE.md
- Stack-agnostic scaffold — builder agents add language/framework files during build
- Usage guide documents the full workflow: intake → scaffold → build
- SSOT.md and README.md updated to cross-reference the template

**Exit criteria:**
- [x] Project template exists and is usable (`templates/project-repo/` with usage guide)
- [x] No duplicate repos for same project (verified — ~/akior and ~/projects/akior are distinct repos with different remotes and purposes)
- [x] Package manager paths are clean and unambiguous (pnpm via npm global, bun runtime-only, no conflicts)
- [x] Conventions documented in this repo (workspace conventions in STACK_STANDARD.md, workspace structure in CURRENT_STATE.md)

---

## Phase 4: Pilot Project Flow — COMPLETE

**Goal:** Run one real project through the full delivery pipeline end to end. Validate the factory works.

**Outputs:**
- One project taken from intake to deployed artifact
- Pipeline bottlenecks identified
- Agent handoff quality assessed
- Token cost measured
- Time-to-delivery measured
- Lessons learned documented

**Pilot selected (Phase 4A — 2026-04-09):**
- **Project:** s4a-factory-dashboard — local status dashboard for the CEO
- **Selection report:** `reports/phase4-pilot-selection.md`
- **Intake form:** `reports/phase4-pilot-intake.md`
- **Repo:** `~/projects/s4a-factory-dashboard`

**Pilot built (Phase 4B — 2026-04-09):**
- CEO scope approval granted
- Repo scaffolded from `templates/project-repo/`, placeholders filled
- server.js built: single-file Node.js dashboard on localhost:3200
- HTML dashboard + JSON API (`/api/status`)
- Live service checks: Paperclip (UP), Ollama (UP), OpenClaw (UP)
- Machine vitals: hostname, uptime, load, RAM, 3x GPU (nvidia-smi)
- SSOT phases: parsed from ROADMAP.md (5 phases shown correctly)
- 6/6 smoke tests pass (node --test)
- Live verification: HTML serves, JSON valid, 404 correct, port 3200 confirmed
- Zero external dependencies — Node.js built-in modules only
- Pipeline report: `reports/phase4-pilot-report.md`

**Exit criteria:**
- [x] Pilot project delivered and working (localhost:3200, 6/6 tests pass)
- [x] Pipeline report written in `reports/` (`reports/phase4-pilot-report.md`)
- [x] At least 3 improvement items identified (6 items documented in pilot report)
- [x] DELIVERY_PIPELINE.md updated with lessons learned

---

## Phase 5: Production Hardening — COMPLETE

**Goal:** Make the factory reliable enough for repeated use with confidence.

**Outputs:**
- Automated testing in pipeline
- Observability basics (logs, error tracking)
- Backup and rollback procedures tested
- GPU scheduling if multi-project concurrency needed
- Cost tracking per project
- Runbook for common failure modes

**Phase 5A — Hardening pack 1 (2026-04-09):**
- Pilot repo cleanup: removed TEMPLATE_USAGE.md, fixed SSOT template copy instructions
- Added package.json with start/test/test:browser scripts (zero runtime deps)
- Added RUNBOOK.md — CEO-readable operator guide (start, stop, test, verify, troubleshoot, rollback)
- Added Playwright browser smoke tests: 2 tests (page sections + JSON API) — 2/2 pass
- Ran Codex review (codex-cli 0.118.0, gpt-5.4): 2 findings, both fixed:
  - P1: Hardened smoke test before() hook (rejects on server crash instead of masking)
  - P2: Added HTML escaping for all dynamic content (XSS prevention)
- Total test coverage: 8/8 (6 node + 2 Playwright)
- Hardening report: `reports/phase5a-hardening-report.md`

**Phase 5B — Hardening pack 2 (2026-04-09):**
- PORT configurable via env var (default 3200, localhost-only)
- ops/dashboard.sh: start/stop/status/restart with PID tracking
- ops/s4a-dashboard.service: systemd user service — installed, enabled, verified
- Auto-starts on user login via `loginctl enable-linger`
- Backup/restore drill passed: cloned from GitHub → tests pass (6/6) → dashboard serves correctly → cleanup complete
- RUNBOOK.md expanded: systemd commands, restore-from-GitHub procedure, custom port, troubleshooting
- Hardening report: `reports/phase5b-deployment-restore-report.md`

**Phase 5C — Pipeline repeatability + CEO trigger (2026-04-09):**
- Restore proof re-run: latest pilot (8d317f9) cloned from GitHub → 6/6 tests pass on port 3201 → HTML + JSON serve correctly → 127.0.0.1 only → cleaned up
- CEO trigger artifact: "How to start a new project" quickstart added to DELIVERY_PIPELINE.md (5 steps, ~5 min CEO involvement)
- Second project delivered end-to-end: s4a-service-snapshot-cli
  - Scaffolded from `templates/project-repo/` (TEMPLATE_USAGE.md correctly excluded)
  - CLI built: `node snapshot.js` (human-readable) / `node snapshot.js --json` (machine-readable)
  - Checks: Paperclip, Ollama, OpenClaw reachability, Docker availability, SSOT phase summary
  - Zero external dependencies
  - 5/5 tests pass
  - Pushed to github.com/yosiwizman/s4a-service-snapshot-cli
- Closeout report: `reports/phase5c-closeout-report.md`

**Exit criteria:**
- [x] Can start a new project and have reasonable confidence it will complete (second project s4a-service-snapshot-cli delivered end-to-end — template → intake → scaffold → build → test → push)
- [x] Failure modes documented with recovery steps (RUNBOOK.md in pilot repo)
- [x] Backup/restore tested at least once (GitHub clone → test → serve on port 3201 → verified)
- [x] CEO can trigger a project build with minimal involvement (CEO quickstart in DELIVERY_PIPELINE.md — 5 steps, ~5 min)

---

## Phase 6: Slice Orchestration Backend

**Goal:** Build s4a-slice-orchestrator — the backend execution and verification layer that enforces tiny, durable, policy-checked delivery slices with an explicit state machine, transition guards, evidence packets, and human approval gates.

**Why a new phase (not Phase 5C):** Phase 5 hardens the existing factory. Phase 6 adds a new factory subsystem — a structured execution backend that operationalizes existing SSOT doctrine (slice rules, retry policy, stop conditions, claim classification) into enforceable runtime logic. It does not replace doctrine — it enforces it programmatically.

**Relationship to existing factory:**
- s4a-slice-orchestrator is factory-owned backend logic
- It is NOT the SSOT (governance stays here)
- It is NOT a Paperclip fork (Paperclip remains the upstream control plane)
- It sits between Paperclip and builder agents, enforcing slice discipline at the execution layer

**Subsystem stack:**
- **Workflow runtime:** Temporal (durable execution, retry, replay, resume)
- **Policy/authorization:** Cedar (declarative policy checks before state transitions)
- **Verification/model checking:** TLA+ + Apalache (formal spec of state machine, checked before implementation)
- **Proof-only critical validators (deferred):** Dafny (mathematical proof for safety-critical transition guards — build later, not in MVP)

**Implementation law — NASA Power of Ten:**
1. Simple control flow — no recursion, no goto
2. Bounded loops — all loops have a fixed upper bound
3. No dynamic memory after initialization
4. Small functions — no function longer than one printed page (~60 lines)
5. Assertions — at least 2 assertions per function
6. Minimal scope — declare variables at the smallest possible scope
7. Checked returns — every return value checked, every error handled
8. Zero-warning discipline — compiler/linter warnings are errors
9. Tool-verifiable rules first — if a tool can check it, the tool must check it
10. No hidden complexity — every side effect is visible in the function signature

**Intake:** `reports/phase6-slice-orchestrator-intake.md`
**MVP plan:** `reports/phase6-slice-orchestrator-mvp-plan.md`
**Repo:** `~/projects/s4a-slice-orchestrator` (created, pushed to github.com/yosiwizman/s4a-slice-orchestrator)

**Phase 6A — Formal specification (2026-04-09):**
- CEO scope approval granted for Phase 6A (spec-first only)
- Repo scaffolded from `templates/project-repo/` (TEMPLATE_USAGE.md excluded)
- TLA+ formal state machine specification written: `spec/tla/SliceMachine.tla`
  - 12 states modeled (expanded from original 8 to include SCOPED, VERIFYING, AWAITING_APPROVAL, APPROVED, RETRY_PENDING)
  - 16 transitions with explicit guards
  - 6 checked invariants + structural invariants
  - Model-checking config: `spec/tla/MC.tla`, `spec/tla/MC.cfg`
  - Operator docs: `spec/tla/README.md`
- No runtime code, no infrastructure changes, no package installs

**Phase 6A — Verification completion (2026-04-09):**
- CEO approved local verification tooling install
- OpenJDK upgraded from 8 to 17.0.18 (Ubuntu repos, required by Apalache)
- TLC 2.19 installed at `~/tools/tlaplus/tla2tools.jar`
- Apalache 0.56.1 installed at `~/tools/apalache/`
- Spec fixes for model checking: MC.tla syntax, Apalache type annotations, state constraint for bounded checking, terminal state stuttering
- TLC result: 4,886 states generated, 2,061 distinct, depth 37, **zero violations**
- Apalache result: bounded model checking to depth 10, **EXITCODE: OK, NoError**
- Both tools independently confirm all 6 invariants hold
- Repo visibility corrected from PUBLIC to PRIVATE (DEC-023)

**Phase 6B — Durable execution spine (2026-04-09):**
- CEO approved Phase 6B (Codex review + minimal Temporal spine)
- Codex review of TLA+ spec: 1 P0, 3 P1, 4 P2 findings
  - P0 fixed: removed dead `rollbackComplete` variable
  - P1 fixed: DRAFT/SCOPED added to rollback set
  - P1 fixed: retryCount reset on BlockedToScoped
  - P1 deferred: testsPass invariant for DEPLOYED (vacuously true)
  - P2s deferred (VERIFYING failure path, approval rejection, coupled test/commit, comment-only invariants)
  - Re-verified: TLC 5,849 states / 2,321 distinct / zero violations; Apalache OK
- Temporal CLI 1.6.2 installed (`~/.temporalio/bin/temporal`)
- TypeScript Temporal workflow implemented: `sliceWorkflow` (DRAFT → SCOPED → BUILDING)
  - Signal-based pause at SCOPED (assignBuilder signal)
  - Query handlers: getState, getHistory
  - @temporalio/workflow 1.15.0
- **Durability proven:** workflow started → paused at SCOPED → worker killed → worker restarted → state preserved (SCOPED) → signal sent → advanced to BUILDING → workflow completed with full transition history
- Stale lifecycle wording fixed in project README.md and ROADMAP.md exit criteria

**Phase 6C — Evidence packets, Cedar guards, approval gate (2026-04-09):**
- CEO approved Phase 6C (evidence + Cedar + approval)
- Evidence packets: JSON files persisted to `evidence/<sliceId>/` at every transition
  - 9 required fields: sliceId, timestamp, fromState, toState, actor, decision, checksRun, result, notes
  - 6 packets emitted in proof run, all persisted on disk
- Cedar policy guards: `@cedar-policy/cedar-wasm` 4.9.1 (local WASM, no services)
  - 5 policies in `policies/guards.cedar` guarding 5 transitions
  - Cedar schema in `policies/schema.cedarschema`
  - Evaluated as Temporal activity before each guarded transition
- Human approval gate: workflow pauses at AWAITING_APPROVAL until explicit `approve` signal
  - Cedar policy `approval_required` enforces `approvalGranted=true` before APPROVED
  - Proven: workflow blocked at AWAITING_APPROVAL, advanced only after signal
- Workflow extended: DRAFT → SCOPED → BUILDING → REVIEWING → VERIFYING → AWAITING_APPROVAL → APPROVED
- New signals: reportTests, reportReview, approve

**Phase 6D — Failure paths, DEPLOYED, pipeline integration (2026-04-09):**
- CEO approved Phase 6D (DENY/retry/rollback, DEPLOYED, pipeline doc)
- Cedar DENY path proven: BUILDING→REVIEWING denied when tests fail → BLOCKED
  - DENY evidence packet emitted with `decision: "DENY"`
- Retry path proven: BLOCKED→SCOPED via retry signal (retryCount reset per TLA+ T14)
- Rollback path proven: BUILDING→ROLLED_BACK via rollback signal (per TLA+ T16)
- DEPLOYED transition proven: APPROVED→DEPLOYED via deploy signal (per TLA+ T13)
- Full 12-state type implemented in runtime (all states in SliceState union)
- Proof script (src/proof.ts): 4 proofs, ALL PASS, 21 evidence packets across 3 workflows
- Delivery pipeline integration documented in DELIVERY_PIPELINE.md
  - Pipeline stage → orchestrator state mapping
  - Failure handling → orchestrator state mapping
  - Usage guide and future integration points

**Exit criteria:**
- [x] State machine formally specified in TLA+ and checked by Apalache
- [x] Core Temporal workflow running locally with slice lifecycle — **all 12 states implemented, happy path reaches DEPLOYED, failure paths reach BLOCKED/ROLLED_BACK**
- [x] Cedar policies enforce at least 3 transition guards — **5 policies guard 5 transitions, DENY proven**
- [x] Evidence packet emitted at each state transition — **21 packets in proof run (7 happy + 11 deny-retry + 3 rollback)**
- [x] Human approval gate verified for at least one risky transition — **AWAITING_APPROVAL → APPROVED**
- [x] Retry/rollback paths tested — **BLOCKED→SCOPED retry proven, ANY→ROLLED_BACK proven**
- [x] Integration with existing delivery pipeline documented — **DELIVERY_PIPELINE.md updated**

---

## Phase 7: Control-Plane Integration

**Goal:** Connect the proven slice orchestrator to the factory control plane safely, starting with a local adapter shim that external orchestrators (eventually Paperclip) can call.

**Why a new phase:** Phase 6 built and proved the orchestrator in isolation. Phase 7 makes it callable by external systems without modifying those systems directly.

**Approach — adapter-shim-first (DEC-026):**
- Build a local CLI adapter that accepts structured JSON commands and returns structured JSON responses
- The adapter wraps the existing Temporal client/signal paths — no new runtime logic
- Paperclip (or any control plane) can invoke the adapter as a subprocess
- No direct Paperclip repo modifications — the adapter is the integration boundary
- No HTTP server unless explicitly needed later

**Relationship to existing factory:**
- The adapter sits between the control plane and the orchestrator
- It does not replace Paperclip — it provides a contract Paperclip can call
- Builder agents report via the same adapter contract (signals routed through it)

**Repo:** `~/projects/s4a-slice-orchestrator` (same repo — adapter is part of the orchestrator)

**Phase 7A — Adapter shim implementation (2026-04-09):**
- CEO approved Phase 7A (adapter shim only)
- `src/adapter.ts`: single CLI entrypoint, 10 commands, JSON in/out
- `docs/ADAPTER_CONTRACT.md`: full contract spec for external callers
- Contract: `node dist/adapter.js <command> '<json>'` → JSON stdout
- Proof: full lifecycle (DRAFT → DEPLOYED) driven entirely through adapter
  - 7 commands, 7 evidence packets, all `ok: true`
  - No manual signal.js or client.js used
- Builder-agent reporting path documented (reportTests/reportReview via adapter)
- No external repos modified (DEC-026 constraint satisfied)

**Exit criteria:**
- [x] Local adapter CLI accepts all orchestrator commands via structured JSON — **10 commands implemented**
- [x] Adapter returns machine-friendly JSON output for every command — **{ok, command, data} format**
- [x] At least one end-to-end lifecycle driven entirely through the adapter — **DRAFT → DEPLOYED proven**
- [x] Adapter contract documented for external callers — **docs/ADAPTER_CONTRACT.md**
- [x] Builder-agent reporting path documented (signal contract) — **reportTests/reportReview in contract**
- [x] No external repos modified — **confirmed**
