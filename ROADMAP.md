# Software 4 All — Roadmap

## Overview

Phased plan from initial setup to a functioning AI software factory. Each phase has clear goals, outputs, and exit criteria. Phases are sequential — do not skip ahead.

---

## Phase 0: SSOT Foundation

**Goal:** Establish the canonical documentation and governance structure so all future work has a reference point.

**Outputs:**
- This repo created and populated
- All canonical docs written with real content
- Decision log initialized
- Agent roles defined
- Delivery pipeline documented

**Exit criteria:**
- [ ] All 11 required docs exist with substantive content
- [ ] CEO has reviewed and approved the operating model
- [ ] Repo is committed and pushed

---

## Phase 1: Security and Machine Baseline

**Goal:** Lock down the machine, close known gaps, establish security standards as enforceable practice.

**Outputs:**
- Firewall configured (UFW) — localhost-only for dev services
- Ports 3000/3002 exposure resolved
- Remote access consolidated to one method
- SSH key policy enforced
- Secrets management baseline established
- CURRENT_STATE.md updated with confirmed security state

**Exit criteria:**
- [ ] UFW active with explicit allow-list
- [ ] No unexpected ports exposed to network
- [ ] Single remote access method chosen and documented
- [ ] Secrets management approach decided and logged in DECISIONS_LOG.md

---

## Phase 2: Runtime Completion

**Goal:** Install all missing approved tools so the factory has a complete runtime.

**Outputs:**
- Docker installed and verified
- uv installed and verified
- CUDA toolkit upgraded to match driver (12.x → 13.0-compatible)
- cuDNN installed
- oh-my-claudecode installed and verified
- GPU routing rules documented
- CURRENT_STATE.md and INSTALL_BASELINE.md updated

**Exit criteria:**
- [ ] Every tool in INSTALL_BASELINE.md "required next" section is installed
- [ ] `docker run hello-world` succeeds
- [ ] `uv --version` succeeds
- [ ] CUDA/cuDNN versions match driver capability
- [ ] All agents in AGENT_ROLE_MATRIX.md have working runtimes

---

## Phase 3: Standardization

**Goal:** Ensure consistent project structure, repo conventions, and workspace organization.

**Outputs:**
- Standard project repo template created
- Workspace directory structure documented
- Duplicate repos identified and consolidated (e.g., akior)
- pnpm/bun path standardization resolved
- Git branching and commit conventions documented
- CI/lint baseline defined (even if not yet automated)

**Exit criteria:**
- [ ] Project template exists and is usable
- [ ] No duplicate repos for same project
- [ ] Package manager paths are clean and unambiguous
- [ ] Conventions documented in this repo

---

## Phase 4: Pilot Project Flow

**Goal:** Run one real project through the full delivery pipeline end to end. Validate the factory works.

**Outputs:**
- One project taken from intake to deployed artifact
- Pipeline bottlenecks identified
- Agent handoff quality assessed
- Token cost measured
- Time-to-delivery measured
- Lessons learned documented

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
