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

## Phase 4: Pilot Project Flow — IN PROGRESS

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
- **Repo (planned):** `~/projects/s4a-factory-dashboard`
- **Status:** Awaiting CEO scope approval before build begins

**Exit criteria:**
- [ ] Pilot project delivered and working
- [ ] Pipeline report written in `reports/`
- [ ] At least 3 improvement items identified
- [ ] DELIVERY_PIPELINE.md updated with lessons learned

---

## Phase 5: Production Hardening

**Goal:** Make the factory reliable enough for repeated use with confidence.

**Outputs:**
- Automated testing in pipeline
- Observability basics (logs, error tracking)
- Backup and rollback procedures tested
- GPU scheduling if multi-project concurrency needed
- Cost tracking per project
- Runbook for common failure modes

**Exit criteria:**
- [ ] Can start a new project and have reasonable confidence it will complete
- [ ] Failure modes documented with recovery steps
- [ ] Backup/restore tested at least once
- [ ] CEO can trigger a project build with minimal involvement
