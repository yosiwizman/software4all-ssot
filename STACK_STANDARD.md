# Software 4 All — Stack Standard

This document defines the approved tool stack, each tool's role, and what is explicitly not approved.

---

## Primary tools

These are the core tools that make up the factory. All are approved for daily use.

| Tool | Role | Why this tool |
|------|------|---------------|
| **Paperclip** | Control plane / orchestrator | Coordinates agents, manages task flow, governs execution. It is the runtime, not the SSOT. |
| **OpenCode** | Default builder agent | Low token cost, good for routine code generation. First choice for all builds. |
| **oh-my-openagent** | OpenCode hardening layer | Adds guardrails, structured output, and reliability to OpenCode. |
| **Claude Code** | Premium builder agent | Higher capability, higher cost. Used only when OpenCode cannot handle the task. |
| **oh-my-claudecode** | Claude Code hardening layer | Multi-agent orchestration, enhanced skills, token efficiency for Claude Code. |
| **Codex** | Secondary reviewer / fixer | Reviews code from other agents. Fixes issues found in review. Not primary builder. |
| **OpenClaw** | Local gateway / assistant layer | Handles integrations, messaging, OAuth flows, user-facing assistant features. |
| **Ollama** | Local model runtime | Runs open-source models locally. Keeps inference costs at zero for supported tasks. |

## Dev tools — approved standards

| Category | Standard | Notes |
|----------|----------|-------|
| IDE | VS Code | All agents and humans use VS Code as standard |
| JS package manager | pnpm | Not npm, not yarn, not bun (bun allowed only as runtime, not package manager). Installed via npm global (~/.npm-global/bin/pnpm). Corepack is present but not used to manage pnpm. |
| Python package manager | uv | Once installed. Replaces pip for project dependency management. |
| Container runtime | Docker | Once installed. Standard for isolated builds and deployments. |
| Version control | git + GitHub (gh CLI) | All repos on GitHub. gh for PR/issue workflows. |
| Shell | bash | Standard across all agents |

## Why Paperclip is control plane, not SSOT

Paperclip orchestrates agent execution — it assigns tasks, manages state during builds, and coordinates handoffs. But it is an **upstream dependency** that may update independently. Company standards, decisions, and architecture live here in `software4all-ssot` so they persist regardless of Paperclip version changes.

**Rule:** Business logic and company standards never live inside the Paperclip repo.

## Why OpenCode is default, Claude Code is premium

| Factor | OpenCode | Claude Code |
|--------|----------|-------------|
| Token cost | Low | High |
| Speed | Fast | Moderate |
| Capability ceiling | Good for routine work | Higher for complex tasks |
| When to use | First attempt on any build task | Escalation when OpenCode fails or task requires premium reasoning |

**Default path:** OpenCode attempts first → if it fails or produces poor quality → escalate to Claude Code.
**Direct Claude Code use:** Allowed for tasks known in advance to exceed OpenCode's capability (complex architecture, multi-file refactors, security-sensitive code).

## Fallback / limited-role tools

| Tool | Allowed use | Not allowed |
|------|------------|-------------|
| bun | JS runtime only (e.g., running scripts) | Not as package manager (use pnpm) |
| pip | Legacy installs only, prefer uv | Not for new project setup |
| npm | Only when pnpm is incompatible | Not as default |

## Not approved — do not add to main workflow

| Tool | Reason |
|------|--------|
| Podman | Docker is the standard container runtime |
| Cursor | VS Code is the standard IDE |
| Windsurf | VS Code is the standard IDE |
| Poetry | uv is the Python standard |
| Extra inference servers | Ollama is sufficient unless explicitly approved |

Adding any tool from this list requires a DECISIONS_LOG.md entry with rationale and CEO approval.

---

## Workspace directory conventions

All work lives under `/home/ai-desktop`. Project repos go in `~/projects/`. Top-level exceptions exist for historically established repos.

| Location | Convention |
|----------|-----------|
| `~/software4all-ssot` | SSOT repo — top-level, not in ~/projects (governance, not a project) |
| `~/paperclip` | Paperclip control plane — top-level (upstream dependency, not an S4A project) |
| `~/projects/<name>` | Standard location for all S4A project repos |
| `~/akior` | AKIOR governance repo — top-level (historical, governance-focused) |

**Rule:** New project repos go in `~/projects/`. Only governance or upstream-dependency repos live at top-level, and only if they are already established there. Do not create new top-level repos without justification.
