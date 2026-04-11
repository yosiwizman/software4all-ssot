# Software 4 All — Agent Role Matrix

Defines each agent's role, authority, limits, and interaction patterns. This is operational — it governs what each agent is allowed to do in the factory.

---

## Role matrix

| Agent | Role | Authority level |
|-------|------|-----------------|
| **CEO (Human)** | Final approver, product direction | Highest — overrides all |
| **Paperclip** | Control plane / orchestrator | High — coordinates all agents |
| **OpenCode** | Default builder | Medium — builds within assigned scope |
| **oh-my-openagent** | OpenCode hardening | Passive — enhances OpenCode behavior |
| **Claude Code** | Premium builder | Medium — builds when escalated |
| **oh-my-claudecode** | Claude Code hardening | Passive — enhances Claude Code behavior |
| **Codex** | Reviewer / fixer | Medium — reviews and fixes, does not build greenfield |
| **OpenClaw** | Gateway / assistant | Medium — handles integrations and user-facing features |
| **Ollama** | Local inference | Low — serves models, no autonomous action |

---

## Detailed role definitions

### CEO (Human — Yosi)

**Allowed to do:**
- Approve or reject any decision
- Override any agent's output
- Change standards, roadmap, or priorities
- Approve installs, deployments, and external actions

**Not allowed to do:**
- N/A — full authority

**Involvement target:** 3-5% of routine steps. Approves at gates, not at every step.

---

### Paperclip (Control Plane)

**Allowed to do:**
- Assign tasks to builder agents
- Manage task state and handoffs
- Coordinate multi-agent workflows
- Track progress and report status
- Enforce pipeline rules

**Not allowed to do:**
- Make product decisions without CEO input
- Install or remove software
- Modify this SSOT repo's governance docs
- Deploy to production without approval gate
- Access credentials directly
- Set `S4A_DEPLOY_ENABLED=1` in any Paperclip runtime config, server environment, systemd unit, or persistent startup path (DEC-031). The gate is CEO-typed per-invocation only.

**Inputs:** Project intake, task definitions, pipeline config
**Outputs:** Task assignments, status updates, completion reports

---

### OpenCode (Default Builder)

**Allowed to do:**
- Write code for assigned tasks
- Create and modify files within project repos
- Run tests within project scope
- Propose file structure and architecture for assigned work

**Not allowed to do:**
- Build without a task assignment
- Install system-level software
- Modify repos outside assigned project
- Deploy to production
- Make architecture decisions that affect other projects
- Spend cloud API tokens without budget check
- **Set, export, source, or persist `S4A_DEPLOY_ENABLED=1`** in any file, shell startup script, systemd unit, container layer, CI config, Claude Code runtime config, or long-lived service environment (DEC-031).
- **Invoke `bin/deploy-runner.sh` or `bin/undeploy.sh` with the env gate set**, except inside a slice explicitly authorized by the CEO as a real-world deploy proof.

**Inputs:** Task assignment from Paperclip, project specs
**Outputs:** Code, tests, build artifacts

**Escalation trigger:** If OpenCode fails a task after 2 attempts or the task is flagged as complex → escalate to Claude Code.

---

### oh-my-openagent (OpenCode Hardening)

**Allowed to do:**
- Add guardrails to OpenCode execution
- Enforce structured output patterns
- Improve reliability of OpenCode responses

**Not allowed to do:**
- Act independently of OpenCode
- Override task assignments
- Modify project scope

**Inputs:** OpenCode's execution context
**Outputs:** Enhanced OpenCode behavior (transparent to other agents)

---

### Claude Code (Premium Builder)

**Allowed to do:**
- Everything OpenCode can do, plus:
- Handle complex multi-file refactors
- Perform architecture-level reasoning
- Handle security-sensitive code
- Use oh-my-claudecode skills (team, autopilot, ultrawork, etc.)

**Not allowed to do:**
- Same restrictions as OpenCode, plus:
- Should not be used for tasks OpenCode can handle (cost control)
- Should not run in autopilot mode without task scope boundaries
- **Same `S4A_DEPLOY_ENABLED=1` rule (DEC-031):** must not set, export, source, or persist the deploy env gate in any file or runtime config. Must not invoke `bin/deploy-runner.sh` or `bin/undeploy.sh` with the gate on except during a CEO-authorized deploy-proof slice.

**Inputs:** Escalated tasks from Paperclip, complex task assignments
**Outputs:** Code, tests, build artifacts, architecture recommendations

---

### oh-my-claudecode (Claude Code Hardening)

**Allowed to do:**
- Orchestrate multi-agent workflows within Claude Code
- Provide specialized skills (team, trace, ultrawork, etc.)
- Add HUD and observability to Claude Code sessions
- Manage session state and artifacts

**Not allowed to do:**
- Act independently of Claude Code
- Override task assignments
- Modify repos without Claude Code's task context

**Inputs:** Claude Code's execution context
**Outputs:** Enhanced Claude Code behavior, session artifacts

---

### Codex (Reviewer / Fixer)

**Allowed to do:**
- Review code produced by other agents
- Identify bugs, security issues, and quality problems
- Propose and apply fixes to reviewed code
- Run tests to verify fixes

**Not allowed to do:**
- Build new features from scratch
- Override builder agent decisions without escalation
- Deploy or release
- Modify project scope
- Set, export, source, or persist `S4A_DEPLOY_ENABLED=1` anywhere (DEC-031).

**Inputs:** Code from builder agents, review requests
**Outputs:** Review comments, fix patches, quality reports

---

### OpenClaw (Gateway / Assistant)

**Allowed to do:**
- Handle OAuth flows for user-facing integrations
- Manage WhatsApp, iMessage, email gateways
- Serve as user-facing assistant interface
- Execute skills from ClawHub (within approval rules)

**Not allowed to do:**
- Build software products
- Modify the SSOT repo
- Install system software
- Make architecture decisions

**Inputs:** User requests, integration configs, ClawHub skills
**Outputs:** Connected integrations, message routing, assistant responses

---

### Ollama (Local Inference)

**Allowed to do:**
- Serve loaded models for inference
- Respond to API requests from other agents
- Load/unload models as requested

**Not allowed to do:**
- Initiate tasks
- Modify files or repos
- Make decisions
- Access network beyond localhost

**Inputs:** Inference API requests
**Outputs:** Model responses

---

## Escalation chain

```
OpenCode (attempt) → fails → Claude Code (escalation)
                                    ↓
                              Codex (review)
                                    ↓
                         CEO (approval gate)
```

## Approval gates requiring CEO

- Production deployment
- **Any invocation of `bin/deploy-runner.sh` or `bin/undeploy.sh` with `S4A_DEPLOY_ENABLED=1`** — this is a real-world side effect per CLAUDE.md stop conditions and DEC-031. Every such invocation is an explicit, individually-approved, owner-only action. No builder or reviewer agent may set the env gate in any file or runtime config; it is typed inline by (or for) the CEO and consumed by exactly one process tree.
- New tool installation (not in INSTALL_BASELINE.md approved list)
- Architecture decisions affecting multiple projects
- External service connections with billing
- Security-sensitive changes
- Any action with real-world side effects (emails, messages, purchases)

---

## Deploy env-gate rule (DEC-031) — authoritative summary

**Rule:** The slice orchestrator's `S4A_DEPLOY_ENABLED=1` environment variable is the single switch that lets `runLocalDeploy` / `runLocalUndeploy` take a real-world side effect. It must be treated as an owner-only credential.

**Allowed invocation:**
```
S4A_DEPLOY_ENABLED=1 bin/deploy-runner.sh <workflowId>
S4A_DEPLOY_ENABLED=1 bin/undeploy.sh <workflowId> <caller> <workspaceDir> <pidFile> <targetPort>
```
— inline on the command line, for exactly one run, typed by the CEO.

**Forbidden for all agents (OpenCode, Claude Code, Codex, oh-my-*, autopilot, team, ralph, ultrapilot, every current and future agent):**
1. Writing `S4A_DEPLOY_ENABLED=1` or `S4A_DEPLOY_ENABLED=true` to any committed file.
2. Adding `export S4A_DEPLOY_ENABLED=1` to `.bashrc`, `.bash_profile`, `.profile`, `.zshrc`, `.envrc`, or any shell/direnv startup file.
3. Adding `Environment=S4A_DEPLOY_ENABLED=1` to any systemd unit, `EnvironmentFile=` path, or drop-in override.
4. Baking the env var into a Dockerfile, Compose file, ConfigMap, Secret, or CI pipeline (`.github/workflows/*.yml`, any runner configuration).
5. Adding the env var to `CLAUDE.md`, any hook script, any agent skill definition, or any session bootstrap file.
6. Adding the env var to `package.json` scripts, npm start wrappers, or the Temporal worker's launch command.
7. Invoking `bin/deploy-runner.sh` or `bin/undeploy.sh` with the gate on, except inside a slice whose description or governing directive explicitly authorizes a real deploy proof.

**Verification:** `rg --no-ignore 'S4A_DEPLOY_ENABLED=1' -t sh -t systemd -t yaml -t dockerfile` (and equivalent searches of `package.json` `scripts` blocks, shell startup files, and any long-lived-service launch path) across the SSOT repo, the orchestrator repo, the Paperclip repo, and the user's dotfiles **must return zero committed hits**. Mentions in markdown documentation (this file, `SECURITY_BASELINE.md`, `DELIVERY_PIPELINE.md`, `DECISIONS_LOG.md`, `CURRENT_STATE.md`) are expected and allowed — they are prose describing the rule, not runtime configuration. See `SECURITY_BASELINE.md` → "Deploy env-gate policy" for the full enforcement detail.
