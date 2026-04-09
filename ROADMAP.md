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

**Exit criteria:**
- [x] State machine formally specified in TLA+ and checked by Apalache
- [ ] Core Temporal workflow running locally with slice lifecycle (DRAFT → READY → BUILDING → REVIEWING → DONE)
- [ ] Cedar policies enforce at least 3 transition guards
- [ ] Evidence packet emitted at each state transition
- [ ] Human approval gate verified for at least one risky transition
- [ ] Retry/rollback paths tested
- [ ] Integration with existing delivery pipeline documented
