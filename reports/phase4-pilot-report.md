# Phase 4 Pilot Report — s4a-factory-dashboard

**Date:** 2026-04-09
**Pilot project:** s4a-factory-dashboard
**Repo:** ~/projects/s4a-factory-dashboard
**Pipeline stages tested:** Intake → Scope → Build → Review → QA → Deploy (localhost)

---

## Summary

The Phase 4 pilot successfully built and deployed a local factory status dashboard through the S4A delivery pipeline. The dashboard runs on localhost:3200, shows live service health, machine vitals, GPU status, and SSOT roadmap phases. All 6 smoke tests pass. The project was completed in a single session with zero external dependencies.

## Pipeline execution log

| Stage | What happened | Agent | Outcome |
|-------|--------------|-------|---------|
| Intake | Intake form filled from pilot selection (Phase 4A) | CTO | Completed — `reports/phase4-pilot-intake.md` |
| Scope approval | CEO approved scope via prompt | CEO | Approved |
| Scaffold | Repo created at `~/projects/s4a-factory-dashboard` from `templates/project-repo/` | Claude Code | Template worked as designed |
| Build | server.js — HTTP server, HTML dashboard, JSON API, service probes, system vitals, SSOT parser | Claude Code | Completed in 1 commit |
| Tests | 6 smoke tests (node:test) — HTML content, JSON schema, 404 handling, GPU info, SSOT phases | Claude Code | 6/6 pass |
| QA (live) | Server started, curl verified HTML + JSON + 404, port confirmed on 3200 | Claude Code | All checks pass |
| Deploy | `node server.js` on localhost:3200 | Manual | Dashboard serves correctly |

## Verified functionality

| Feature | Evidence | Status |
|---------|----------|--------|
| HTML dashboard on localhost:3200 | curl returns HTTP 200, Content-Type text/html | PASS |
| JSON API on /api/status | curl returns valid JSON with timestamp, services, machine, ssotPhases | PASS |
| Paperclip probe (:3100) | JSON shows `"up": true` | PASS |
| Ollama probe (:11434) | JSON shows `"up": true` | PASS |
| OpenClaw probe (:18789) | JSON shows `"up": true` | PASS |
| GPU info (3 GPUs) | nvidia-smi parsed: GPU0 (RTX PRO 6000, 96GB), GPU1 (RTX 3090, 24GB), GPU2 (RTX 4060 Ti, 16GB) | PASS |
| Machine vitals | hostname, uptime (3d+), 64 CPUs, 134.5 GB RAM, load averages | PASS |
| SSOT phases | 5 phases parsed: Ph0-3 COMPLETE, Ph4 IN PROGRESS, Ph5 PLANNED | PASS |
| 404 handling | Unknown routes return HTTP 404 | PASS |
| Refresh button | Present in HTML | PASS |
| Docker status | Reports "not available" (correct — group requires fresh login session) | PASS |
| Smoke tests | 6/6 pass via `node --test test/smoke.test.js` | PASS |

## What the template got right

1. **Placeholder system worked.** `{{PROJECT_NAME}}` etc. were straightforward to fill.
2. **CLAUDE.md in the project repo** gave clear agent instructions — useful for scope control.
3. **.gitignore covered the basics** — no modifications needed for this project.
4. **docs/ directory ready** for future project documentation.

## What the template did not cover

1. **TEMPLATE_USAGE.md was copied into the project repo** — it should stay in SSOT only. Future template copies should exclude it, or the copy instructions should be more explicit.
2. **No package.json included** — intentional for stack-agnostic template, but the build step had to decide whether to add one. For a zero-dependency Node.js project, it wasn't needed.

## Pipeline improvement items

### 1. Builder agent role was Claude Code, not OpenCode (planned default)
The intake specified OpenCode as the default builder, but this pilot was executed by Claude Code as the session agent. A true pipeline test should route tasks through Paperclip → OpenCode, with Claude Code only on escalation. This is a pipeline orchestration gap, not a code quality issue.

### 2. Codex review stage was skipped
The delivery pipeline specifies Codex as the reviewer after each build task. In this pilot, the builder (Claude Code) performed its own review. A separate Codex review pass would catch issues the builder might miss. This requires Paperclip orchestration to route completed tasks to Codex.

### 3. Playwright browser QA was not used
The intake specified Playwright for browser testing. The pilot used curl-based and Node.js test runner verification instead. Playwright would provide stronger visual QA (page rendering, layout, element visibility). Adding a Playwright smoke test is a recommended enhancement.

### 4. No auto-start mechanism
The dashboard requires manual `node server.js`. A systemd unit or process manager would allow it to start on boot and survive reboots. This is a Phase 5 concern.

### 5. Docker reports "not available"
Docker is installed but the docker group change requires a fresh login session. The dashboard correctly reports what it observes, but this could confuse the CEO. Consider adding a note in the UI explaining why.

### 6. Template copy instructions should exclude TEMPLATE_USAGE.md
The template copy step (`cp -r templates/project-repo/* .`) copies TEMPLATE_USAGE.md into the project repo. This file is for SSOT reference only and should not be in the project. The template usage guide should note this, or the file should be moved out of the template directory.

## Metrics

| Metric | Value |
|--------|-------|
| Total files in project | 7 (README.md, CLAUDE.md, .gitignore, TEMPLATE_USAGE.md, docs/.gitkeep, server.js, test/smoke.test.js) |
| Lines of code (server.js) | ~190 |
| Lines of test (smoke.test.js) | ~80 |
| External dependencies | 0 |
| Pipeline stages completed | 6/9 (intake, scope, build, test, QA, deploy) |
| Pipeline stages skipped | 3 (Codex review, Playwright QA, Paperclip orchestration) |
| Smoke tests | 6/6 pass |
| Build commits | 2 (scaffold + build) |

## Recommendations for Phase 5

1. **Wire Paperclip orchestration** — route tasks to OpenCode first, Codex for review, Claude Code for escalation only. The pipeline is defined but not yet automated.
2. **Add Playwright smoke test** — one simple test that loads localhost:3200 and verifies the page renders.
3. **Consider auto-start** — systemd unit for the dashboard so it survives reboots.
4. **Fix template copy** — exclude TEMPLATE_USAGE.md from project copies.
5. **Test with a non-dashboard project** — the pilot was infrastructure-adjacent. A user-facing project would test different pipeline characteristics.
