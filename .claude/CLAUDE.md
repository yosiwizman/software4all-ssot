# Software 4 All — SSOT Repo Instructions

This repo is the **canonical business operating system** for Software 4 All.
It is documentation-first. It contains no application code, no runtime services, no Paperclip source.

## What this repo is

- Company standards, policies, and architecture decisions
- Machine and infrastructure state tracking
- Agent role definitions and delivery pipeline specs
- Project intake templates and security baselines

## What this repo is NOT

- Not an app runtime — do not add package.json, Dockerfile, or build scripts
- Not the Paperclip repo — do not modify Paperclip from here
- Not a scratchpad — every file has a defined purpose

## Working rules

1. **Treat canonical docs as source of truth.** Read them before proposing changes.
2. **Update, don't duplicate.** If a fact belongs in CURRENT_STATE.md, put it there — not in a new file.
3. **Append decisions to DECISIONS_LOG.md** with date and rationale.
4. **Update CURRENT_STATE.md** when machine or infrastructure facts change.
5. **Do not create new top-level docs** without proposing the purpose first.
6. **Keep writing CEO-readable.** Direct, no fluff, bullet points and tables preferred.
7. **Cross-reference, don't copy.** Point to the canonical doc instead of restating its content.
8. **Never modify runtime repos** (Paperclip, OpenClaw, etc.) from inside this repo.

## Key docs to load for context

@SSOT.md
@STACK_STANDARD.md
@CURRENT_STATE.md
@INSTALL_BASELINE.md
@SECURITY_BASELINE.md
@DELIVERY_PIPELINE.md
@DECISIONS_LOG.md
@AGENT_ROLE_MATRIX.md
