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

---

## How to start a new project (CEO quickstart)

This is the minimum path for the CEO to trigger a new project build.

**Step 1 — Describe the idea** (30 seconds)
Tell Claude Code or Paperclip what you want built. One paragraph is enough.

**Step 2 — Review the intake** (2 minutes)
The agent fills out `PROJECT_INTAKE_TEMPLATE.md` from your description. You review it:
- Does the project name make sense?
- Does the business goal match your intent?
- Are the success criteria right?
- Is the stack choice reasonable?

Say "approved" or request changes.

**Step 3 — Scaffold happens automatically**
The agent creates the project repo in `~/projects/<name>` from `templates/project-repo/`, fills placeholders, and commits the scaffold.

**Step 4 — Build happens autonomously**
The builder agent (OpenCode by default, Claude Code for complex tasks) works through the build path defined in the intake. One task, one commit.

**Step 5 — Review and approve** (2 minutes)
After build + review + QA, the agent presents a summary:
- What was built
- Test results
- Any risks

Say "deploy" or "fix [specific issue]."

**Total CEO involvement: ~5 minutes across 3 touchpoints.**

Everything else — scaffold, build, test, review — is autonomous within the rules defined in this pipeline.

---

## Lessons learned (Phase 4 pilot — 2026-04-09)

Findings from running s4a-factory-dashboard through the pipeline end-to-end. See `reports/phase4-pilot-report.md` for the full report.

### What worked
- Intake template was practical and complete — no ambiguity during build
- Project repo template (scaffold) worked as designed — placeholders filled cleanly
- Zero-dependency build was fast and reliable
- Node.js built-in test runner provided adequate smoke tests
- SSOT cross-references kept the project aligned with standards

### Gaps identified
1. **Paperclip orchestration not tested.** The pilot was built by Claude Code directly, bypassing the Paperclip → OpenCode → Codex flow. The pipeline stages are defined but not yet automated through the control plane.
2. **Codex review stage skipped.** No independent review agent was invoked. Builder self-reviewed. This reduces the value of the review stage.
3. **Playwright QA not used.** Browser-level testing was replaced by curl and Node.js test runner. Adequate for this pilot, but insufficient for UI-heavy projects.
4. **Template copies TEMPLATE_USAGE.md into project repos.** This file is reference material for SSOT, not a project file. Copy instructions should exclude it.

### Recommended pipeline improvements
- Wire Paperclip to route build tasks to OpenCode first, then escalate to Claude Code on failure
- Add a Codex review step after each build commit — even if automated, it catches issues the builder misses
- Add Playwright as a standard QA tool for any project with a web UI
- Fix template copy instructions to exclude TEMPLATE_USAGE.md

---

## Slice orchestrator integration (Phase 6)

The s4a-slice-orchestrator enforces this pipeline programmatically using a formal 12-state machine (verified by TLA+/Apalache). Each pipeline stage maps to one or more orchestrator states.

### Pipeline stage → orchestrator state mapping

| Pipeline stage | Orchestrator state(s) | Guard / trigger | Evidence emitted |
|---------------|----------------------|-----------------|-----------------|
| Idea | (pre-workflow) | CEO describes the idea | — |
| Intake | DRAFT | Workflow created with sliceId + description | Yes |
| Scope / Spec | DRAFT → SCOPED | Cedar: `scope_required` (scopeApproved=true) | Yes |
| Build | SCOPED → BUILDING | Cedar: `builder_required` (builderAssigned=true) | Yes |
| Review | BUILDING → REVIEWING | Cedar: `tests_required` (testsPass+commitExists) | Yes |
| QA | REVIEWING → VERIFYING | Cedar: `review_required` (reviewApproved=true) | Yes |
| Approval | VERIFYING → AWAITING_APPROVAL → APPROVED | Cedar: `approval_required` (approvalGranted=true) | Yes |
| Deploy | APPROVED → DEPLOYED | Deploy signal from CEO | Yes |
| Maintenance | (post-workflow) | New slice for fixes/features | — |

### Failure handling → orchestrator states

| Failure scenario | Orchestrator path | Trigger |
|-----------------|-------------------|---------|
| Build fails (tests don't pass) | BUILDING → BLOCKED (Cedar DENY) | reportTests(fail) signal |
| Retry after failure | BLOCKED → SCOPED (retryCount reset) | retry signal from CEO |
| Review rejection | REVIEWING → BUILDING (retry) | reportReview(reject) signal |
| CEO cancels / rolls back | ANY → ROLLED_BACK | rollback signal from CEO |
| Unrecoverable failure | BUILDING/REVIEWING → FAILED (retryCount exhausted) | Automatic after MAX_RETRY |

### How to use

1. **Start the orchestrator:** `temporal server start-dev` + `node dist/worker.js`
2. **Create a slice:** `node dist/client.js` (creates a workflow, prints workflowId)
3. **Drive the lifecycle:** Send signals via `node dist/signal.js <signal> <workflowId>`
4. **Check state:** `temporal workflow query --workflow-id <id> --type getState`
5. **Review evidence:** Inspect `evidence/<sliceId>/` for JSON evidence packets

### Integration points (future)

| Integration | Status | When |
|------------|--------|------|
| Paperclip → orchestrator | Deferred | When Paperclip task routing is automated |
| Builder agents → orchestrator | Deferred | When OpenCode/Claude Code report via signals |
| Dashboard → orchestrator | Deferred | When s4a-factory-dashboard shows slice status |
| Codex → orchestrator | Deferred | When Codex review results are routed as signals |
