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
- New tool installation (not in INSTALL_BASELINE.md approved list)
- Architecture decisions affecting multiple projects
- External service connections with billing
- Security-sensitive changes
- Any action with real-world side effects (emails, messages, purchases)
