# Software 4 All — Delivery Pipeline

End-to-end flow from idea to maintained software. Defines who owns each stage, what the inputs and outputs are, and where approval gates sit.

---

## Pipeline overview

```
Idea → Intake → Scope/Spec → Build → Review → QA → Approval → Deploy → Maintenance
```

## Stage definitions

### 1. Idea

**Owner:** CEO or any stakeholder
**Input:** A need, problem, or opportunity
**Output:** Verbal or written description of what's needed
**Tool:** None — this is human input
**Gate:** None — ideas flow freely

### 2. Intake

**Owner:** CEO (with Paperclip assistance)
**Input:** Idea description
**Output:** Completed PROJECT_INTAKE_TEMPLATE.md
**Tool:** Paperclip generates intake from CEO's description
**Gate:** CEO reviews and approves the filled template before proceeding

**CEO action:** Review the intake doc. Confirm scope, stack choice, and success criteria. Say "approved" or request changes.

### 3. Scope / Spec

**Owner:** Paperclip + Claude Code (if complex) or OpenCode (if routine)
**Input:** Approved intake template
**Output:** Task breakdown with estimated complexity per task
**Tool:** Paperclip decomposes the project into buildable tasks
**Gate:** CEO reviews task breakdown for scope creep. Approves task list.

**Escalation logic:**
- Simple projects (1-3 tasks, clear spec) → OpenCode scopes directly
- Complex projects (4+ tasks, unclear boundaries) → Claude Code assists with scoping

### 4. Build

**Owner:** OpenCode (default) or Claude Code (escalation)
**Input:** Task assignments from Paperclip
**Output:** Working code, committed to project repo
**Tool:** OpenCode builds first. If it fails after 2 attempts on a task, Paperclip escalates to Claude Code.

**Rules:**
- One task at a time per builder
- Each task produces a commit
- Builder must run basic tests before marking task complete
- No scope expansion without going back to Intake

**Cost control:** OpenCode handles all routine tasks. Claude Code only when:
- OpenCode fails twice on the same task
- Task is pre-flagged as complex in the spec
- Task involves security-sensitive code
- Task requires multi-file architectural changes

### 5. Review

**Owner:** Codex (primary reviewer)
**Input:** Completed code from build stage
**Output:** Review comments, approved / needs-fix verdict
**Tool:** Codex reviews each completed task or PR

**Review checklist:**
- Does the code match the spec?
- Are there bugs or logic errors?
- Are there security issues?
- Is the code readable and maintainable?
- Do tests exist and pass?

**If fixes needed:** Code returns to Build stage. Same builder attempts fix. If fix fails after 2 attempts → escalate builder level.

### 6. QA

**Owner:** Automated tests + Playwright (if UI) + Codex verification
**Input:** Reviewed and approved code
**Output:** QA report — pass or fail with details
**Tool:**
- Unit/integration tests run automatically
- Playwright for browser-based testing (if applicable)
- Codex runs verification checks

**Gate:** All tests must pass. No deploy with failing tests.

### 7. Approval

**Owner:** CEO
**Input:** QA report, review summary, working demo (if applicable)
**Output:** Deploy approval or rejection
**Tool:** Paperclip presents summary to CEO

**CEO action:** Review the summary. Try the demo if applicable. Say "deploy" or "fix [specific issue]".

**What CEO sees:**
- One-paragraph summary of what was built
- Test results (pass/fail count)
- Review verdict
- Link to try it (if applicable)
- Any risks or caveats

### 8. Deploy

**Owner:** Paperclip orchestrates, builder agent executes
**Input:** Approved code
**Output:** Running deployment
**Tool:** Docker (for containerized apps), direct deploy (for simple tools)

**Rules:**
- Local deployment first (localhost)
- Production deploy only with explicit CEO approval
- Rollback plan must exist before deploying
- Deployment steps must be reproducible

### 9. Maintenance

**Owner:** Paperclip monitors, builder agents fix
**Input:** Bug reports, performance issues, feature requests
**Output:** Fixes and updates fed back through the pipeline (starting at Intake)

**Rules:**
- Bug fixes go through abbreviated pipeline (Build → Review → QA → Deploy)
- Feature additions go through full pipeline from Intake
- Critical bugs may skip to Build with CEO notification

---

## Escalation logic summary

```
Task assigned → OpenCode attempts (up to 2 tries)
                    ↓ fails
              Claude Code attempts (up to 2 tries)
                    ↓ fails
              STOP → report to CEO with details
```

```
Review finds issues → return to builder (same level, up to 2 fix attempts)
                         ↓ still failing
                    escalate builder level
                         ↓ still failing
                    STOP → report to CEO
```

## CEO involvement summary

| Stage | CEO involvement | Action required |
|-------|----------------|-----------------|
| Idea | Provides the idea | Describe what's needed |
| Intake | Reviews filled template | Approve or revise |
| Scope | Reviews task breakdown | Approve or flag scope creep |
| Build | None (unless escalation fails) | Only if both agents fail |
| Review | None | — |
| QA | None | — |
| Approval | Reviews summary and demo | Say "deploy" or "fix" |
| Deploy | None (unless production) | Approve production deploys |
| Maintenance | Notified of issues | Decide priority |

**Target:** CEO touches 3-5% of steps. Everything else is autonomous within these rules.
