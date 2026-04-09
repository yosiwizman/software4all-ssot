# Project intake template

Copy this template for each new project entering the factory. Fill in every section before starting a build.

---

## Project name

_[Short, clear project name]_

## Business goal

_[What does this project accomplish? Why does it matter? Who asked for it?]_

## User / problem

- **Target user:** _[Who will use this?]_
- **Problem:** _[What problem does it solve?]_
- **Current workaround:** _[How is this handled today without this project?]_

## Success criteria

_[How do we know this project is done and working? Be specific.]_

- [ ] _[Criterion 1]_
- [ ] _[Criterion 2]_
- [ ] _[Criterion 3]_

## Stack choice

| Decision | Choice | Reason |
|----------|--------|--------|
| Language | _[JS/TS/Python/Go/etc.]_ | _[Why]_ |
| Framework | _[Next.js/FastAPI/etc.]_ | _[Why]_ |
| Builder agent | _[OpenCode / Claude Code]_ | _[Why — default to OpenCode]_ |
| Database | _[SQLite/Postgres/none]_ | _[Why]_ |
| Container | _[Docker yes/no]_ | _[Why]_ |

## Repo plan

- **Repo name:** _[github.com/org/repo-name]_
- **New repo or existing:** _[New / Existing — which one?]_
- **Branch strategy:** _[main only / feature branches]_
- **Monorepo or standalone:** _[Standalone unless reason for monorepo]_

## Security needs

- [ ] Handles user data (PII)?
- [ ] Requires authentication?
- [ ] Requires secrets/API keys?
- [ ] Exposes network ports?
- [ ] Needs HTTPS?
- [ ] Other security concerns: _[describe]_

## Build path

1. _[Step 1 — e.g., scaffold project]_
2. _[Step 2 — e.g., implement core feature]_
3. _[Step 3 — e.g., add tests]_
4. _[Continue as needed]_

## Test / review path

- **Unit tests:** _[Yes/No — framework?]_
- **Integration tests:** _[Yes/No — what?]_
- **Review agent:** _[Codex / Claude Code / manual]_
- **Browser testing:** _[Playwright / manual / N/A]_

## Deployment target

- **Where:** _[Local only / Docker / cloud / static hosting]_
- **Domain:** _[N/A / custom domain / localhost]_
- **CI/CD:** _[None / GitHub Actions / manual]_

## Approval gates

| Gate | Approver | When |
|------|----------|------|
| Scope approval | CEO | Before build starts |
| Code review | Codex or Claude Code | After build, before deploy |
| Deploy approval | CEO | Before any production deploy |
| Post-deploy check | _[Agent or human]_ | After deploy |

## Notes

_[Anything else relevant — constraints, deadlines, dependencies, risks]_
