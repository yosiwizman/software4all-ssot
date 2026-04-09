# Project intake: s4a-service-snapshot-cli

_Phase 5C — second project to prove pipeline repeatability._

---

## Project name

s4a-service-snapshot-cli

## Business goal

Give operators a one-command factory health snapshot from the terminal. Complements the web dashboard (s4a-factory-dashboard) with a CLI alternative that works without a browser.

## User / problem

- **Target user:** CEO, operators, agents
- **Problem:** Checking factory status requires opening a browser or reading SSOT files. No single terminal command produces a complete snapshot.
- **Current workaround:** `curl localhost:3200/api/status` (requires dashboard running) or manual checks.

## Success criteria

- [ ] `node snapshot.js` outputs human-readable factory snapshot to stdout
- [ ] `node snapshot.js --json` outputs machine-readable JSON
- [ ] Includes: timestamp, hostname, Paperclip/Ollama/OpenClaw reachability, Docker availability, SSOT phase summary
- [ ] Zero external runtime dependencies (Node.js built-in modules only)
- [ ] Tests pass (node --test)

## Stack choice

| Decision | Choice | Reason |
|----------|--------|--------|
| Language | JavaScript (Node.js) | Approved stack, zero deps |
| Framework | None | CLI only, built-in modules |
| Builder agent | Claude Code | Current session agent |
| Database | None | Read-only tool |
| Container | No | Simple CLI |

## Repo plan

- **Repo name:** github.com/yosiwizman/s4a-service-snapshot-cli
- **New repo or existing:** New
- **Branch strategy:** main only
- **Monorepo or standalone:** Standalone

## Security needs

- No PII, no auth, no secrets, no network ports exposed, no HTTPS needed.

## Deployment target

- **Where:** Local only — run from terminal
- **CI/CD:** None
