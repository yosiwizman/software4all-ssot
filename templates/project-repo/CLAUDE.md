# {{PROJECT_NAME}} — Agent Instructions

This file governs how builder and reviewer agents operate in this project repo.

## Project context

- **Project:** {{PROJECT_NAME}}
- **Stack:** {{PRIMARY_STACK}}
- **Builder:** {{BUILDER_AGENT}} (default), escalate to Claude Code if needed
- **Reviewer:** Codex

## Rules

1. **Follow the approved intake.** The build path and success criteria are defined in the intake form. Do not expand scope.
2. **One task, one commit.** Each task produces a focused commit. Do not batch unrelated changes.
3. **Run tests before marking a task complete.** If no test framework exists yet, set one up as the first build step.
4. **pnpm for JS/TS packages, uv for Python.** See `software4all-ssot/STACK_STANDARD.md` for the full standard.
5. **No secrets in code.** Use `.env` files (gitignored). See `software4all-ssot/SECURITY_BASELINE.md`.
6. **Localhost only** unless the intake explicitly approves network exposure.
7. **Do not modify the SSOT repo** from inside this project repo.

## Escalation

If a task fails after 2 attempts:
- OpenCode task → escalate to Claude Code
- Claude Code task → stop and report to CEO with details

See `software4all-ssot/DELIVERY_PIPELINE.md` for the full escalation chain.
