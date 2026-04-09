# Phase 5A Hardening Report

**Date:** 2026-04-09
**Scope:** Pilot repo (s4a-factory-dashboard) + SSOT template fix
**Goal:** Improve repeatability, QA quality, and operator usability without expanding project scope

---

## Changes made

### Pilot repo cleanup
- Removed `TEMPLATE_USAGE.md` — this file belongs in the SSOT repo, not in project repos
- Added `test-results/` to `.gitignore`

### Repeatability hardening
- Added `package.json` with scripts: `start`, `test`, `test:browser`
- Zero runtime dependencies; `@playwright/test` as dev dependency only
- Node.js engine requirement: >=22.0.0

### Operator hardening
- Added `RUNBOOK.md` — CEO-readable guide covering start, stop, test, verify, troubleshoot, rollback
- Includes "what healthy looks like" section and common troubleshooting table

### QA improvements
- Added `playwright.config.js` with webServer auto-start on port 3200
- Added `test/browser.spec.js` with 2 Playwright tests:
  1. Dashboard page loads and all sections are visible (title, services, vitals, GPUs, roadmap, refresh button)
  2. `/api/status` returns valid JSON with expected structure
- Both tests pass (Chromium headless)

### Codex review
- **Tool:** codex-cli 0.118.0, model gpt-5.4, read-only sandbox
- **Findings:** 2 issues identified, both fixed

| ID | Severity | Finding | Fix applied |
|----|----------|---------|-------------|
| P1 | Medium | Smoke test `before()` hook silently proceeds if server fails to start — can mask real failures | Replaced with reject-on-exit/error pattern, 5s timeout with explicit error |
| P2 | Low | Dynamic content (GPU names, SSOT phases, hostname) interpolated into HTML without escaping — localhost-only XSS vector | Added `escapeHTML()` function, applied to all dynamic content in renderer |

### SSOT template fix
- Updated `templates/project-repo/TEMPLATE_USAGE.md` copy instructions to explicitly list files to copy and note that TEMPLATE_USAGE.md itself should not be copied into project repos

## Test results after hardening

| Suite | Tests | Result |
|-------|-------|--------|
| Node.js smoke (node --test) | 6 | 6/6 PASS |
| Playwright browser (npx playwright test) | 2 | 2/2 PASS |
| **Total** | **8** | **8/8 PASS** |

## Live verification

| Check | Result |
|-------|--------|
| localhost:3200 serves HTML | PASS |
| /api/status returns valid JSON | PASS |
| Services section shows 3 services | PASS |
| GPU section shows 3 GPUs | PASS |
| SSOT phases parsed correctly (6 phases) | PASS |
| Port bound to 127.0.0.1 only | PASS |
| No HTML injection in rendered output | PASS |

## Remaining hardening gaps

1. **No auto-start mechanism** — dashboard requires manual `node server.js`. Systemd unit would survive reboots.
2. **Paperclip orchestration not wired** — tasks still routed manually, not through Paperclip → OpenCode → Codex flow.
3. **No backup/restore test** — exit criterion for Phase 5. Needs a concrete backup scenario.
4. **No cost/token tracking** — Phase 4 pilot did not measure token spend.
5. **Codex sandbox issue** — Codex review ran in bwrap sandbox which blocked local file reads. It fell back to GitHub MCP to fetch files. Works but fragile for repos not yet pushed.
