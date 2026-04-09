# Phase 5B Deployment & Restore Report

**Date:** 2026-04-09
**Scope:** Pilot repo deployment hardening + backup/restore validation
**Goal:** Service repeatability, safe startup, and one real backup/restore test

---

## Deployment hardening

### PORT override
- `server.js` now reads `PORT` from environment, defaults to 3200
- `test/smoke.test.js` also respects `PORT` env and passes it to the forked server
- Localhost-only binding preserved regardless of port

### Ops scripts
- Created `ops/dashboard.sh` — bash start/stop/status/restart
- PID tracking via `.dashboard.pid` (gitignored)
- Log output to `.dashboard.log` (gitignored)
- Status command probes `/api/status` and prints service/GPU/phase summary
- Fallback port-based kill if PID file is stale

### Systemd user service
- Created `ops/s4a-dashboard.service` — user-level systemd unit
- Installed to `~/.config/systemd/user/`
- Enabled via `systemctl --user enable s4a-dashboard`
- `loginctl enable-linger` verified — service persists across logouts/reboots
- Verified: started, `active (running)`, serves HTML + JSON correctly

## Backup/restore drill

| Step | Result |
|------|--------|
| Clone from GitHub to `/tmp/s4a-restore-test-*` | Success — all expected files present |
| Run `node --test` from restored clone | 6/6 tests PASS |
| Start restored server on port 3200 | Server starts, `listening at http://localhost:3200` |
| Verify HTML from restored server | "S4A Factory Dashboard" present |
| Verify JSON from restored server | 3 services, 3 GPUs, 6 phases — all correct |
| Port bound to 127.0.0.1 only | Confirmed via `ss -tlnp` |
| Clean up temp restore path | Removed successfully |

**Conclusion:** The pilot can be fully restored from GitHub source control with zero manual configuration. Clone → npm start → working dashboard.

## Verification after all changes

| Suite | Tests | Result |
|-------|-------|--------|
| Node smoke tests (default port) | 6 | 6/6 PASS |
| Playwright browser tests | 2 | 2/2 PASS |
| Ops script start/status/stop | 3 ops | All work correctly |
| Systemd start/status | verified | active (running), serves correctly |
| **Total** | **8 tests + ops verification** | **All PASS** |

## Remaining Phase 5 gaps

1. **"Can start a new project with confidence"** — requires testing the full pipeline on a second project, not just the pilot
2. **"CEO can trigger a project build with minimal involvement"** — requires Paperclip orchestration wiring or a simple UI trigger
