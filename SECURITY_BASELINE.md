# Software 4 All — Security Baseline

Minimum security standards for the ai-desktop machine and the software factory. These are practical and enforceable — not aspirational.

---

## Core principle

**Localhost-first.** All dev services bind to 127.0.0.1 by default. Nothing is exposed to the network unless explicitly approved and documented.

---

## Firewall

**Standard:** UFW must be active with a default-deny incoming policy.

**Required rules:**
- Default: deny incoming, allow outgoing
- SSH (port 22): allow from trusted IPs only, or disable if not needed
- All dev services (3000, 3002, 8080, etc.): localhost only unless explicitly opened
- Ollama API: localhost only (127.0.0.1:11434)

**Current status (2026-04-09):** UFW is active and enabled on system startup.
- Default: deny incoming, allow outgoing
- Routed: disabled
- Logging: on (low)
- No explicit allow rules added yet (default-deny covers all inbound)

---

## Exposed port policy

No service may listen on 0.0.0.0 (all interfaces) unless:
1. There is a documented business reason
2. It is logged in CURRENT_STATE.md with the reason
3. CEO has approved the exposure
4. Firewall rules limit access to specific IPs if possible

**First deploy class (Slice 33, DEC-031):** All orchestrator-driven deploys land on `127.0.0.1` only. Binding to `0.0.0.0`, `::`, any LAN interface, any tunnel, or any public address is **forbidden** for deploys executed by `bin/deploy-runner.sh` / `runLocalDeploy`. This is enforced in three layers and none of them may be weakened without a new CEO decision: (1) `isLoopbackHealthUrl` pure-string prefix check in `src/deploy-constants.ts`, (2) Cedar Policy 6 `deploy_allowed` requiring `targetIsLoopback == true`, (3) runner-side `fetchLoopback` which refuses non-loopback URLs as a paranoid second check. A future non-loopback deploy class is not permitted until an explicit CEO decision re-opens this rule.

**Resolved (2026-04-09):**
- **Port 3000** was a Jarvis V5 OS `dev-proxy-http.mjs` (node) bound to `0.0.0.0`. It was a stale dev process, not a production service. Process killed. No auto-restart mechanism found (no systemd service, no cron).
- **Port 3002** had nothing listening. The original concern is cleared.
- **Current state:** All TCP services are now bound to localhost only. No externally-reachable TCP listeners remain.

## Runtime port ownership (DEC-016)

This machine hosts multiple projects. Port ownership prevents cross-project confusion.

| Port | Owner | Purpose | Rule |
|------|-------|---------|------|
| **3100** | **Software 4 All / Paperclip** | Control plane local entrypoint | Canonical S4A endpoint. Do not reassign. |
| 3000 | Jarvis / AKIOR | Dev proxy | Not S4A. Jarvis-only. Localhost-only. |
| 3001 | Jarvis / AKIOR | Next.js dev server | Not S4A. Jarvis-only. Localhost-only. |
| 3002 | Jarvis / AKIOR | Reserved for Jarvis dev | Not S4A. Currently unused. Localhost-only. |

**Rules:**
- All project-specific local services must bind to `127.0.0.1` unless separately approved
- Non-3100 ports must never be treated as Software 4 All control-plane endpoints
- If a new project needs local ports, document ownership before binding

---

## Deploy env-gate policy (DEC-031)

The slice orchestrator exposes `S4A_DEPLOY_ENABLED=1` as the single switch that allows `runLocalDeploy` / `runLocalUndeploy` to take a real-world side effect. It **must not** be persisted anywhere. Persistence is prohibited in:

- **Committed files:** any committed file in any repo (SSOT, orchestrator, Paperclip, Jarvis, AKIOR, downstream projects, dotfiles).
- **Shell startup files:** `.bashrc`, `.bash_profile`, `.profile`, `.zshrc`, `.envrc`, `direnv` configs.
- **Systemd units:** user or system unit files, `EnvironmentFile=` directives, drop-in overrides.
- **Container layers:** Dockerfiles, Compose files, ConfigMaps, Secrets, sidecar injectors.
- **CI/CD config:** `.github/workflows/*.yml`, any CI secrets store, any runner environment.
- **Claude Code runtime:** `CLAUDE.md`, hook scripts, agent skill definitions, session bootstrap files.
- **Long-lived services:** Temporal worker env, `start:worker` npm scripts, any boot path that persists across reboots.

**Allowed invocation pattern:** inline on a single shell command, typed by the CEO for exactly one deploy or undeploy run — e.g. `S4A_DEPLOY_ENABLED=1 bin/deploy-runner.sh <workflowId>`. The gate is consumed by that process and its subprocesses only. It does not leak to siblings, does not persist across shells, and does not re-appear on the next login.

**Enforcement:**
1. `applyDeployEnvGate` in `adapter-core.ts` forcibly demotes `pass=true` to `false` unless `S4A_DEPLOY_ENABLED === '1'` in the adapter's process environment.
2. `runLocalDeploy` branches on `isDeployEnvGateOn(process.env)` and only executes the real spawn + probe + liveness path when the gate is on.
3. Cedar Policy 7 `deploy_success_required` denies `DEPLOYING → DEPLOYED` without `healthProbePassed && pidAlive`, so even if the env gate is somehow set, a missing real health probe still routes the slice to `DEPLOY_FAILED`.
4. A grep for `S4A_DEPLOY_ENABLED=1` across **executable or runtime config contexts** — shell scripts (`*.sh`, `*.bash`, `*.zsh`), shell startup files (`.bashrc`, `.bash_profile`, `.profile`, `.zshrc`, `.envrc`), systemd units (`*.service`, `*.socket`, drop-in overrides, `EnvironmentFile=` targets), Dockerfiles, Compose files, CI workflow YAML (`.github/workflows/*.yml`), npm `scripts` blocks in `package.json`, and any long-lived-service launch path — **must return zero committed hits**. If it does, that is a security incident and the file must be cleaned in a dedicated slice before any further deploy work. Literal mentions in markdown documentation (this file, `AGENT_ROLE_MATRIX.md`, `DELIVERY_PIPELINE.md`, `DECISIONS_LOG.md`, `CURRENT_STATE.md`, `DEPLOY_CONTRACT.md`) are expected and allowed because they are prose describing the rule — they do not cause the gate to be set at runtime.

**Audit trail:** the only places `S4A_DEPLOY_ENABLED=1` should appear at runtime are: (a) the single shell command the CEO types to invoke `bin/deploy-runner.sh` or `bin/undeploy.sh`, and (b) the *output* side of a shell session (`.bash_history`, `set -x` traces, observability logs captured during the deploy). It must never appear on the *input* side of any configuration file that is loaded automatically by a shell, process, service, or CI runner.

---

## Secrets handling

**Current state (2026-04-09):** No centralized secrets manager. Policy baseline established. Tool selection deferred to a later phase — current risk is medium and policy enforcement is sufficient for now.

**Enforced standard (now):**
- No secrets in git repos (enforced by .gitignore patterns in all repos)
- No secrets in SSOT docs or any checked-in file
- API keys stored in environment variables or `.env` files that are gitignored
- Credentials files (`.credentials.json`, etc.) must be in gitignored paths

**Future standard (Phase 2 or later):**
- Adopt `sops + age` for encrypted secrets at rest (recommended path — see DEC-015)
- Consolidate existing scattered credentials into managed storage

**Never acceptable:**
- Secrets committed to git
- Secrets in CLAUDE.md or any checked-in file
- Secrets shared in plain text across systems
- API keys hardcoded in source code

---

## SSH key policy

- SSH keys must use Ed25519 (preferred) or RSA 4096-bit minimum
- No password-only SSH authentication
- SSH agent forwarding: disabled unless explicitly needed
- GitHub authentication: via SSH key or gh CLI token (not password)

---

## Remote access policy

**Decision (2026-04-09, DEC-014):** RustDesk v1.3.8 is the standard remote access method.

**Current state:**
- RustDesk: Active (service, server, tray running). Encrypted, authenticated, functional.
- GNOME Remote Desktop: System service is active but RDP is disabled with no credentials configured. Effectively a no-op — not a security risk in current state. Can be left as-is or disabled for cleanliness.

**Requirements for RustDesk (enforced):**
- Strong password or key-based authentication (not default)
- Encrypted connection (RustDesk default)
- Access logged
- If not in active use, service can be stopped but is acceptable to leave running for on-demand access

---

## Rollback and backup baseline

**Minimum standard:**
- Git for all code repos (already in place)
- Timeshift for OS-level snapshots (verify if installed and configured)
- SSOT repo backed up to GitHub (push after each significant update)
- `/mnt/models` data: at minimum, know what's there and how to recreate it

**Before any destructive operation:**
- Git commit or stash current state
- Timeshift snapshot if OS-level changes
- Document rollback steps before executing

---

## Repo hygiene

- Every repo must have a `.gitignore` that excludes secrets, credentials, and build artifacts
- No large binaries committed to git (use .gitignore or git-lfs)
- No credential files in any repo
- Pre-commit hooks recommended for secret detection (e.g., git-secrets or similar)

---

## Container security (once Docker is installed)

- Run containers as non-root where possible
- Do not expose container ports to 0.0.0.0 unless explicitly approved
- Use official or verified base images only
- Pin image versions (no `latest` in production)

---

## Agent security boundaries

- Agents must not access credentials they don't need for their current task
- Build agents (OpenCode, Claude Code) should not have access to production credentials
- OpenClaw handles OAuth flows — builder agents do not
- Ollama listens on localhost only

---

## Current known security concerns

| Concern | Severity | Status | Action needed |
|---------|----------|--------|---------------|
| Ports 3000/3002 exposed to network | ~~High~~ | **Resolved** | Port 3000 process killed, port 3002 was clear. All TCP now localhost-only. |
| UFW not confirmed active | ~~High~~ | **Resolved** | Active, deny incoming, allow outgoing, enabled on startup. |
| No secrets manager tool | Medium | Policy set | sops+age recommended for future. Policy enforcement sufficient for now. |
| Multiple remote access tools | ~~Medium~~ | **Resolved** | RustDesk chosen as standard (DEC-014). GNOME RD inactive. |
| Scattered credentials | Medium | Known | Consolidate when secrets tool is adopted |
| No pre-commit secret scanning | Low | Missing | Add after Phase 1 security baseline |

---

## Enforcement

These standards are enforced by:
1. Claude Code hooks (destructive-guard, secret-guard, branch-guard — already active)
2. Agent role limits (see AGENT_ROLE_MATRIX.md)
3. CEO approval gates at deploy stage
4. Manual review during Phase 1 security hardening

Standards not yet enforceable by automation are enforced by convention until tooling is in place.
