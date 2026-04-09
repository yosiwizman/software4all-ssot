# Project intake: s4a-factory-dashboard

_Copied from PROJECT_INTAKE_TEMPLATE.md — filled for the Phase 4 pilot project._

---

## Project name

s4a-factory-dashboard

## Business goal

Give the CEO a single browser tab showing whether the AI software factory is healthy and what phase it is in. Replaces the need to SSH in and read markdown files or run CLI commands to understand factory status.

## User / problem

- **Target user:** CEO (Yosi)
- **Problem:** Checking factory status requires reading multiple SSOT files and running terminal commands. No at-a-glance view exists.
- **Current workaround:** Open terminal, run `ai-desktop-health`, read CURRENT_STATE.md manually.

## Success criteria

- [ ] Dashboard serves on localhost:3200 and loads in a browser
- [ ] Shows up/down status for at least 3 services (Paperclip, Ollama, OpenClaw)
- [ ] Shows basic machine vitals (CPU load, RAM usage, GPU VRAM)
- [ ] Shows current SSOT roadmap phase
- [ ] Page auto-refreshes or has a refresh button
- [ ] Built entirely through the S4A delivery pipeline (intake → build → review → QA → deploy)

## Stack choice

| Decision | Choice | Reason |
|----------|--------|--------|
| Language | JavaScript (Node.js) | Approved stack, available on machine (v22.22.2) |
| Framework | None (built-in http module + static HTML) | Minimal dependencies, simple enough for a dashboard |
| Builder agent | OpenCode | Default builder — routine task, no escalation expected |
| Database | None | Reads live state from system commands and files |
| Container | No | Localhost-only, no need for Docker isolation |

## Repo plan

- **Repo name:** github.com/yosiwizman/s4a-factory-dashboard
- **New repo or existing:** New
- **Branch strategy:** main only (simple project, direct push like SSOT)
- **Monorepo or standalone:** Standalone

## Security needs

- [ ] Handles user data (PII)? **No**
- [ ] Requires authentication? **No** (localhost only)
- [ ] Requires secrets/API keys? **No**
- [x] Exposes network ports? **Yes — localhost:3200 only**
- [ ] Needs HTTPS? **No** (localhost)
- [ ] Other security concerns: None — reads system state only, no write operations

## Build path

1. Scaffold repo from `templates/project-repo/`
2. Create HTTP server that serves a single HTML page on localhost:3200
3. Add service health checks (HTTP probe to Paperclip :3100, Ollama :11434, OpenClaw :18789)
4. Add machine vitals (parse output of system commands: `uptime`, `free`, `nvidia-smi`)
5. Add SSOT phase display (parse ROADMAP.md for current phase status)
6. Style the page for readability (clean HTML/CSS, no framework needed)
7. Add auto-refresh (meta refresh or simple JS interval)

## Test / review path

- **Unit tests:** Yes — Node.js built-in test runner (node --test). Test health check functions.
- **Integration tests:** Yes — start server, verify HTTP 200 and page content.
- **Review agent:** Codex
- **Browser testing:** Playwright — verify page loads, shows expected sections, displays live data.

## Deployment target

- **Where:** Local only — localhost:3200
- **Domain:** N/A (localhost)
- **CI/CD:** None (manual start: `node server.js`)

## Approval gates

| Gate | Approver | When |
|------|----------|------|
| Scope approval | CEO | Before build starts — **PENDING** |
| Code review | Codex | After each build task |
| Deploy approval | CEO | Before serving on localhost |
| Post-deploy check | Playwright + CEO | After deploy, verify dashboard works |

## Notes

- This is the Phase 4 pilot project. Its primary purpose is to validate the S4A delivery pipeline end-to-end.
- The dashboard itself is useful but the pipeline lessons are the main output.
- Port 3200 chosen to avoid conflicts with Paperclip (3100), Jarvis (3000-3002), and Ollama (11434).
- If OpenCode cannot handle a task after 2 attempts, escalate to Claude Code per DELIVERY_PIPELINE.md.
- Pipeline report (time, tokens, bottlenecks, lessons) will be written to `reports/` after completion.
