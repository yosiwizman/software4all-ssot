# Phase 4 Pilot Selection Report

**Date:** 2026-04-09
**Phase:** 4A — Pilot selection and planning
**Author:** CTO (Claude Code)

---

## Objective

Select one real project to run through the full Software 4 All delivery pipeline (Intake → Scope → Build → Review → QA → Approval → Deploy) to validate the factory works end-to-end.

## Candidates audited

### 1. ai-desktop-health (~/projects/ai-desktop-health)

- **Purpose:** Machine health check CLI (Ollama, GPUs, disk, SSOT files)
- **State:** COMPLETE — POC approved by owner, all acceptance criteria met
- **Stack:** Node.js, zero dependencies
- **Verdict:** Not suitable. Already finished. Built before the SSOT pipeline existed — running it through the pipeline retroactively would not be a genuine test.

### 2. HRM (~/projects/HRM)

- **Purpose:** Hierarchical Reasoning Model — external ML research repo
- **Remote:** github.com/sapientinc/HRM (not owned by S4A)
- **State:** Cloned reference, not an active S4A build project
- **Verdict:** Not suitable. External code, wrong ownership, wrong scope. S4A should not modify or "build" someone else's research project.

### 3. AKIOR (~/projects/akior)

- **Purpose:** Fully autonomous AI assistant — manages communications, calendar, business ops
- **Remote:** github.com/yosiwizman/AKIOR_AUOTONOMUS_ASSISTENT
- **State:** Large, WIP, synced from Mac Mini, many agent checkpoints
- **Stack:** Node.js, multi-channel integrations (WhatsApp, iMessage, email, calendar)
- **Verdict:** Not suitable as first pilot. Too large, too many external dependencies, unclear completion boundary. Would make a good future project but not a pipeline validation pilot.

## Decision

No existing project meets the pilot criteria. The pilot must be a **new, purpose-built small project**.

## Chosen pilot: s4a-factory-dashboard

A single-page local web dashboard that shows the current state of the Software 4 All factory — running services, agent availability, machine vitals, and SSOT status.

### Why this pilot

| Criterion | Assessment |
|-----------|-----------|
| Real value | CEO can see factory status at a glance instead of reading markdown files |
| Scope | Small — 3-5 build tasks |
| Pipeline coverage | Full — intake, scope, build, review, QA (Playwright), deploy (localhost) |
| Dependencies | None external — reads local process state and SSOT files |
| Builder tier | OpenCode-appropriate (simple Node.js web app) |
| Risk | Low — localhost only, no secrets, no external services |
| Testable | Yes — Playwright can verify page renders, checks run |
| Deployable | Yes — serves on localhost, simple start/stop |

### What the dashboard shows

1. **Service status** — Paperclip (:3100), Ollama (:11434), OpenClaw (:18789) — up/down indicator
2. **Machine vitals** — CPU, RAM, GPU VRAM usage (from system commands)
3. **SSOT phase** — Current roadmap phase (parsed from ROADMAP.md)
4. **Agent roster** — List from AGENT_ROLE_MATRIX.md with availability

### What success looks like

- Dashboard serves on a localhost port (e.g., :3200)
- Page loads in a browser and shows live data
- All data is from local sources (no external APIs)
- Built entirely through the S4A delivery pipeline
- Pipeline report documents what worked and what didn't

## Risk assessment

- **Technical risk:** Low — standard Node.js HTTP server, HTML rendering, shell commands for system data
- **Pipeline risk:** Medium — this is the first real pipeline test; expect some friction in agent handoffs
- **Infrastructure risk:** None — all approved tools are installed, no new installs needed

## Recommended execution plan

1. CEO reviews and approves this pilot selection and the intake form
2. Scaffold repo from `templates/project-repo/` into `~/projects/s4a-factory-dashboard`
3. OpenCode builds (task-by-task, committed per task)
4. Codex reviews each completed task
5. Playwright QA on the rendered page
6. CEO reviews working dashboard
7. Deploy: simple `node server.js` or `pnpm dev` on localhost
8. Pipeline report written in `reports/`
