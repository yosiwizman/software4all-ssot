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
