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

**Current status:** Unverified — UFW status not confirmed. This is **Priority 1** in Phase 1.

**Action required:** Run `sudo ufw status` and configure per above rules. Log result in CURRENT_STATE.md.

---

## Exposed port policy

No service may listen on 0.0.0.0 (all interfaces) unless:
1. There is a documented business reason
2. It is logged in CURRENT_STATE.md with the reason
3. CEO has approved the exposure
4. Firewall rules limit access to specific IPs if possible

**Known concern:** Ports 3000 and 3002 were previously observed as exposed. Source services are unknown. Must be identified and either locked to localhost or explicitly approved.

---

## Secrets handling

**Current state:** No centralized secrets manager. API keys and credentials are stored in various locations without standardization.

**Minimum standard (immediate):**
- No secrets in git repos (enforced by .gitignore patterns)
- No secrets in SSOT docs
- API keys stored in environment variables or `.env` files that are gitignored
- Credentials files (`.credentials.json`, etc.) must be in gitignored paths

**Target standard (Phase 1):**
- Adopt a lightweight secrets manager (sops + age, or similar)
- Decision on specific tool logged in DECISIONS_LOG.md
- All existing scattered credentials consolidated

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

**Known concern:** Both RustDesk and GNOME Remote Desktop may be active, creating two access paths.

**Standard:** One remote access method only. Choose one, disable the other, document the choice in DECISIONS_LOG.md.

**Requirements for whichever method is chosen:**
- Strong authentication (not default passwords)
- Encrypted connection
- Access logged
- Disabled when not in active use (preferred)

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
| Ports 3000/3002 exposed to network | High | Unresolved | Identify services, lock to localhost or approve |
| UFW not confirmed active | High | Unverified | Check and configure firewall |
| No secrets manager | Medium | Missing | Decide on tool in Phase 1 |
| Multiple remote access tools | Medium | Unresolved | Choose one, disable other |
| Scattered credentials | Medium | Known | Consolidate in Phase 1 |
| No pre-commit secret scanning | Low | Missing | Add after Phase 1 security baseline |

---

## Enforcement

These standards are enforced by:
1. Claude Code hooks (destructive-guard, secret-guard, branch-guard — already active)
2. Agent role limits (see AGENT_ROLE_MATRIX.md)
3. CEO approval gates at deploy stage
4. Manual review during Phase 1 security hardening

Standards not yet enforceable by automation are enforced by convention until tooling is in place.
