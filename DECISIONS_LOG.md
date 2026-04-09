# Software 4 All — Decisions Log

Append-only record of major decisions. Never remove or silently edit entries. Add new decisions at the bottom.

Format: `### DEC-NNN: Title` with date, decision, rationale, and authorship.

**Authorship convention:**
- `CEO-directed` = CEO stated the decision directly
- `CTO-recommended, CEO-approved` = CTO proposed, CEO accepted
- `CTO-executed` = CTO made an operational call within approved scope

---

### DEC-001: SSOT repo is separate from Paperclip
**Date:** 2026-04-09
**Decision:** The canonical business operating system lives in `software4all-ssot`, not inside the Paperclip repo.
**Rationale:** Paperclip is an upstream runtime dependency that may update independently. Business standards, architecture decisions, and company policies must persist regardless of Paperclip version changes. Mixing them risks drift and coupling.
**Authorship:** CEO-directed

### DEC-002: No Paperclip fork — treat as upstream dependency
**Date:** 2026-04-09
**Decision:** Do not fork Paperclip. Use it as-is from upstream. Customization happens through configuration and plugins, not source modification.
**Rationale:** Forking creates maintenance burden and divergence risk. The control plane should be upgradeable without merge conflicts against custom patches.
**Authorship:** CEO-directed

### DEC-003: OpenCode default builder, Claude Code premium escalation
**Date:** 2026-04-09
**Decision:** OpenCode is the first-choice builder for all tasks. Claude Code is used only when OpenCode fails or the task is known to exceed its capability.
**Rationale:** Token cost control. OpenCode handles routine work well at low cost. Claude Code's higher capability justifies its cost only for complex tasks.
**Authorship:** CEO-directed

### DEC-004: Codex as secondary reviewer/fixer, not primary builder
**Date:** 2026-04-09
**Decision:** Codex reviews and fixes code produced by other agents. It does not serve as a primary builder.
**Rationale:** Specialization. Having a dedicated reviewer improves quality. Codex's strengths align with review and correction rather than greenfield generation.
**Authorship:** CEO-directed

### DEC-005: Local-first, low token cost strategy
**Date:** 2026-04-09
**Decision:** Use local models and tools by default. Cloud APIs only when local capability is insufficient. Minimize token spend across all operations.
**Rationale:** Cost control and independence. Local hardware is already paid for. Cloud API costs scale with usage and create vendor dependency.
**Authorship:** CEO-directed

### DEC-006: pnpm as standard JS package manager
**Date:** 2026-04-09
**Decision:** All JavaScript/TypeScript projects use pnpm. Not npm, not yarn, not bun (bun allowed as runtime only).
**Rationale:** pnpm is fast, disk-efficient, and strict about dependency resolution. Standardizing on one package manager avoids lockfile conflicts and confusion.
**Authorship:** CEO-directed

### DEC-007: uv as standard Python package manager (once installed)
**Date:** 2026-04-09
**Decision:** All new Python projects use uv for dependency and project management. pip remains only for legacy installs.
**Rationale:** uv is significantly faster than pip, handles virtual environments natively, and provides reproducible builds. Modern replacement for pip+venv+poetry.
**Authorship:** CEO-directed

### DEC-008: Docker as standard container runtime (once installed)
**Date:** 2026-04-09
**Decision:** Docker is the only approved container runtime. Podman is not approved.
**Rationale:** Docker has the widest ecosystem support, most documentation, and best agent compatibility. One container runtime avoids confusion.
**Authorship:** CEO-directed

### DEC-009: VS Code as standard IDE
**Date:** 2026-04-09
**Decision:** VS Code is the standard IDE for all development. Cursor and Windsurf are not approved.
**Rationale:** VS Code has the best extension ecosystem, all agent tools integrate with it, and it's free. No reason to fragment across IDEs.
**Authorship:** CEO-directed

### DEC-010: Tools not approved for main workflow
**Date:** 2026-04-09
**Decision:** Podman, Cursor, Windsurf, Poetry, and extra inference servers are not approved unless a future decision overrides this.
**Rationale:** Each has an approved equivalent in the stack. Adding alternatives creates drift, confusion, and maintenance overhead.
**Authorship:** CEO-directed

### DEC-011: Authorship convention for decisions log
**Date:** 2026-04-09
**Decision:** Adopt three-tier authorship labels: `CEO-directed`, `CTO-recommended, CEO-approved`, and `CTO-executed`. DEC-001 through DEC-010 are retroactively labeled CEO-directed — they originated from CEO instructions during initial SSOT creation.
**Rationale:** "Decided by: CEO" was ambiguous — it didn't distinguish between CEO-originated decisions and CTO recommendations that the CEO approved. Clear attribution prevents future confusion about decision provenance.
**Authorship:** CTO-executed

### DEC-012: oh-my-claudecode installed as approved stack component
**Date:** 2026-04-09
**Decision:** oh-my-claudecode v4.11.4 installed via npm (`oh-my-claude-sisyphus`) and activated via `omc setup`. Adds 19 agent definitions and 37 skills to Claude Code. Existing Claude Code hooks preserved — OMC did not overwrite them.
**Rationale:** Part of the approved target stack (see STACK_STANDARD.md). Provides multi-agent orchestration, token efficiency, and enhanced skills for the premium builder tier.
**Authorship:** CTO-recommended, CEO-approved

### DEC-013: SSOT repo branch policy
**Date:** 2026-04-09
**Decision:** The `software4all-ssot` repo allows direct pushes to `main` by approved operator flow. Feature branches and PRs are not required for SSOT updates.
**Rationale:** This repo is the canonical operating system and documentation repo for Software 4 All, not a product engineering repo requiring PR-heavy workflow. Requiring feature branches/PR flow for every SSOT change adds friction and increases drift risk for a CEO-run system.
**Enforcement note:** The global Claude Code branch-guard hook (`~/.claude/hooks/branch-guard.sh`) blocks pushes to `main` by default. For this repo, pushes use `CC_PROTECT_BRANCHES="none"` to override. This does not weaken protection for other repos — the override is applied per-push, not globally.
**Authorship:** CTO-recommended, CEO-approved

### DEC-014: RustDesk as standard remote access method
**Date:** 2026-04-09
**Decision:** RustDesk v1.3.8 is the standard remote access tool for ai-desktop. GNOME Remote Desktop is not actively used (RDP disabled, no credentials configured) and may remain installed but is not the standard path.
**Rationale:** RustDesk is already active, encrypted, authenticated, and functional. GNOME Remote Desktop's system service is running but RDP is disabled — it poses no security risk but also provides no value. Standardizing on one tool simplifies security review and access control.
**Authorship:** CTO-recommended, CEO-approved

### DEC-015: Secrets management baseline — policy now, sops+age later
**Date:** 2026-04-09
**Decision:** Immediate baseline is policy-only: no secrets in git, use gitignored `.env` and credential files only. The recommended future tool is `sops + age` for encrypted secrets at rest, to be installed when a project requires centralized credential management.
**Rationale:** No project currently requires a secrets manager tool. Installing one now would be premature. The policy baseline (gitignore enforcement, no secrets in checked-in files) is sufficient for current risk level. sops+age is lightweight, offline-capable, and fits the local-first model.
**Authorship:** CTO-recommended, CEO-approved

### DEC-016: Runtime port ownership and project separation
**Date:** 2026-04-09
**Decision:** `localhost:3100` is the canonical Software 4 All / Paperclip local control-plane endpoint. Jarvis / AKIOR local dev services may use ports such as `3000/3001/3002`, but these are project-specific and must not be treated as Software 4 All control-plane endpoints. All project-specific services must remain localhost-only unless separately approved.
**Rationale:** The ai-desktop machine hosts multiple local projects simultaneously. Explicit port ownership prevents cross-project confusion, accidental process interference, and incorrect operational assumptions.
**Authorship:** CTO-recommended, CEO-approved

### DEC-017: Defer CUDA toolkit upgrade and system cuDNN install
**Date:** 2026-04-09
**Decision:** CUDA toolkit upgrade (12.0 → 13.x) and system-level cuDNN installation are deferred until a specific project requires them. The current CUDA 12.0 toolkit remains functional. Ollama bundles its own CUDA 13 + cuDNN 9.20.0 runtime internally, so local inference is not affected.
**Rationale:** The installed CUDA 12.0 came from Ubuntu's `nvidia-cuda-toolkit` package. Upgrading to 13.x requires adding NVIDIA's official apt repo, which may conflict with Ubuntu's packages and risk destabilizing the driver (580.126.09). No current project requires CUDA 13.x features. The risk/reward ratio does not justify the upgrade now.
**Authorship:** CTO-executed

### DEC-018: GPU routing rules — conservative baseline
**Date:** 2026-04-09
**Decision:** Establish conservative GPU role assignments based on verified hardware state:
- **GPU0 (RTX PRO 6000 Blackwell, 96 GB):** Primary compute and inference target. Default for Ollama and any ML workloads. No display attached.
- **GPU1 (RTX 3090, 24 GB):** Secondary compute. Used for overflow when GPU0 is full or for parallel workloads. No display attached.
- **GPU2 (RTX 4060 Ti, 16 GB):** Display GPU (desktop attached, ~1.4 GB baseline usage). Reserve for GUI responsiveness. Avoid heavy compute workloads.

No manual `CUDA_VISIBLE_DEVICES` pinning is applied — Ollama auto-selects based on model size. NVIDIA Container Toolkit deferred until a containerized GPU workload is needed. No GPU scheduler installed; revisit if multi-tenant contention arises.
**Rationale:** GPU0's 96 GB VRAM naturally absorbs all current inference models. GPU2 has the desktop display attached (confirmed via `nvidia-smi display_active=Enabled`), making it unsuitable as a primary compute target without risking UI stutter. This is a conservative baseline — routing can be tightened later when real multi-GPU workloads reveal actual contention patterns.
**Authorship:** CTO-executed

### DEC-019: Phase 3A standardization audit — workspace and tooling baseline
**Date:** 2026-04-09
**Decision:** Establish workspace and tooling conventions based on verified audit:
1. **Workspace layout:** New project repos go in `~/projects/`. Only governance repos (SSOT, akior-governance) and upstream dependencies (paperclip) live at top-level.
2. **AKIOR repos are distinct, not duplicates:** `~/akior` (akior-governance — ledger/governance) and `~/projects/akior` (AKIOR_AUOTONOMUS_ASSISTENT — application code) serve different purposes and have separate GitHub remotes. No consolidation needed.
3. **pnpm install method:** pnpm is installed via npm global (`~/.npm-global/bin/pnpm`). Corepack 0.34.6 is present but not managing pnpm. This is acceptable — no migration to corepack required unless a specific benefit is identified.
4. **Paperclip invocation:** No `paperclip` CLI binary exists in PATH. Control plane runs via `pnpm dev` from `~/paperclip`. CLI available as `pnpm paperclipai` within the repo. Ports 3100 and 13100 (both localhost-only).
**Rationale:** Phase 3 requires documenting reality before prescribing changes. This audit corrects the prior assumption that AKIOR repos were duplicates, clarifies Paperclip naming ambiguity, and establishes directory conventions to prevent workspace sprawl.
**Authorship:** CTO-executed

### DEC-020: Standard project repo template established
**Date:** 2026-04-09
**Decision:** Create a canonical project repo scaffold at `templates/project-repo/` in the SSOT repo. The template is stack-agnostic and governance-first — it provides README.md (with placeholders), CLAUDE.md (agent instructions), .gitignore (multi-language), docs/ directory, and a TEMPLATE_USAGE.md explaining the workflow. Builder agents add language/framework-specific files during the build phase. New projects must use this template after intake approval.
**Rationale:** Phase 3 exit criterion requires a usable project template. The template enforces consistent repo structure across projects without over-prescribing stack choices. Keeping it in the SSOT repo (not inside Paperclip) ensures it persists regardless of control-plane version changes. The intake form (PROJECT_INTAKE_TEMPLATE.md) and repo scaffold (templates/project-repo/) are complementary — intake defines what to build, scaffold defines where to build it.
**Authorship:** CTO-executed

### DEC-021: Phase 4 pilot project — s4a-factory-dashboard
**Date:** 2026-04-09
**Decision:** Select `s4a-factory-dashboard` as the Phase 4 pilot project. A single-page local web dashboard (localhost:3200) showing factory service status, machine vitals, and SSOT phase. Built with Node.js (built-in http module, no framework), reviewed by Codex, QA'd with Playwright. Three existing candidates were evaluated and rejected: ai-desktop-health (already completed), HRM (external repo, not S4A-owned), AKIOR (too large and complex for a first pilot). Port 3200 chosen to avoid conflicts with Paperclip (3100), Jarvis (3000-3002), and Ollama (11434).
**Rationale:** Phase 4 requires a real project through the full delivery pipeline. No existing project fits — ai-desktop-health was built before the pipeline existed, HRM is external, and AKIOR is too complex. A new small project designed specifically as a pipeline test is the safest path. The dashboard is genuinely useful (CEO visibility into factory state) and exercises all pipeline stages with low risk.
**Authorship:** CTO-recommended, CEO-approved (scope approved 2026-04-09)

### DEC-022: Phase 6 — s4a-slice-orchestrator as next factory backend project
**Date:** 2026-04-09
**Decision:** Add s4a-slice-orchestrator as Phase 6 in the roadmap. This is a new factory backend subsystem — an execution and verification layer that enforces delivery slice discipline programmatically. It operationalizes existing SSOT doctrine (slice rules, retry policy, stop conditions, claim classification) into an explicit state machine with transition guards, evidence packets, and human approval gates. Placed as Phase 6 (not Phase 5C) because it is a new subsystem, not a hardening step for existing infrastructure.

**Stack direction for this project:**
- **Temporal** — durable workflow execution (retry, replay, resume, signals)
- **Cedar** — declarative policy evaluation for transition guards
- **TLA+ + Apalache** — formal specification and model checking of state machine (before implementation)
- **Dafny** — deferred to post-MVP; mathematical proof for safety-critical guards

**Implementation law:** NASA Power of Ten coding rules — simple control flow, bounded loops, small functions, explicit assertions, checked returns, zero-warning discipline, tool-verifiable rules first.

**Key constraints:**
- TLA+ spec must be written and model-checked BEFORE any implementation code
- Factory-owned backend logic, not SSOT and not a Paperclip fork
- Local-first, no cloud dependencies
- Claude Code is pre-flagged as primary builder (task exceeds OpenCode capability)
- Runs in `~/projects/s4a-slice-orchestrator`

**Rationale:** The factory currently enforces delivery discipline by convention — agents read CLAUDE.md and follow written rules. This is fragile. A programmatic enforcement layer ensures every slice flows through a verified path: no skipped reviews, no silent transitions, no risky actions without approval. Temporal provides durability and retry. Cedar provides auditable policies. TLA+ catches design errors before they become code bugs.
**Authorship:** CEO-directed

### DEC-023: Phase 6A verification tooling + repo privacy correction
**Date:** 2026-04-09
**Decision:** Three operational decisions for Phase 6A completion:
1. **Repo visibility:** s4a-slice-orchestrator changed from PUBLIC to PRIVATE on GitHub. Backend infrastructure repos should not be public by default.
2. **Java upgrade:** OpenJDK upgraded from 8 to 17.0.18 (Ubuntu `openjdk-17-jre-headless` package). Required by Apalache model checker. Java 17 is now the default `java` on PATH.
3. **TLA+ verification tools installed:** TLC 2.19 (`~/tools/tlaplus/tla2tools.jar`) and Apalache 0.56.1 (`~/tools/apalache/`). Both are free, open-source, local-only. No services, no subscriptions, no cloud. Used for formal verification of the slice state machine as required by DEC-022.

**Rationale:** DEC-022 requires TLA+ model checking before implementation code. CEO approved local-only verification tooling install for this purpose. Repo privacy corrected because this is internal factory infrastructure, not a public project.
**Authorship:** CEO-directed (tool install approval), CTO-executed (installation and verification)

### DEC-024: Temporal CLI installed for Phase 6B durable execution spine
**Date:** 2026-04-09
**Decision:** Install Temporal CLI 1.6.2 (`~/.temporalio/bin/temporal`) for local development. The CLI bundles a lightweight dev server (`temporal server start-dev`) that runs an in-memory or SQLite-backed Temporal server locally — no Docker, no cloud, no subscription. Used to prove durable workflow execution for the slice orchestrator.
**Rationale:** Phase 6B requires a running Temporal server to prove workflow durability (pause/resume across worker restarts). The CLI dev server is the lightest-weight local option. It is free, open-source, and requires no external services.
**Authorship:** CEO-directed (Phase 6B approval), CTO-executed
