# Software 4 All — Single Source of Truth

## Mission

Build a local-first AI software factory that produces end-to-end software with minimal human involvement, using controlled agent stacks on owned hardware, keeping token costs low and quality high.

## Operating model

Software 4 All runs on a single high-end workstation (ai-desktop) with multiple AI agents coordinated by Paperclip as the control plane. The CEO approves key decisions at ~3-5% involvement. All other execution is autonomous within approved boundaries.

**Core principle:** Local-first. Use local models and tools by default. Use cloud APIs only when local capability is insufficient.

## This repo's role

This repo (`software4all-ssot`) is the **canonical business operating system**. It defines what is true, what is approved, what is planned, and how work flows.

It is NOT:
- The Paperclip codebase (that lives in its own repo)
- An application runtime
- A place for source code

## Canonical doc map

Each topic has exactly one authoritative file. If information about a topic exists elsewhere, the canonical file wins.

| Topic | Canonical file | Update trigger |
|-------|---------------|----------------|
| Company standards & governance | SSOT.md (this file) | Policy change |
| Machine & ecosystem state | CURRENT_STATE.md | Any infra change |
| Approved tool stack | STACK_STANDARD.md | Tool approval/removal |
| Install requirements | INSTALL_BASELINE.md | New install need identified |
| Architecture decisions | DECISIONS_LOG.md | Any major decision |
| Agent roles & authority | AGENT_ROLE_MATRIX.md | Agent added/changed |
| Delivery workflow | DELIVERY_PIPELINE.md | Pipeline change |
| Security standards | SECURITY_BASELINE.md | Security policy change |
| Planned work phases | ROADMAP.md | Phase completed or replanned |
| New project template | PROJECT_INTAKE_TEMPLATE.md | Template improvement |

## Governance rules

1. **CEO is final approver.** AI agents propose; the CEO decides on anything outside pre-approved scope.
2. **One canonical file per topic.** Never duplicate facts across files. Cross-reference instead.
3. **Append-only decisions.** Decisions are logged in DECISIONS_LOG.md and never silently removed.
4. **Update, don't create.** Before adding a new doc, check if the information belongs in an existing one.
5. **New top-level docs require justification.** Propose the purpose and get approval before creating.
6. **State changes go to CURRENT_STATE.md.** Not inline in other docs.
7. **No scope creep in updates.** Change only what the update requires.

## Update rules

- Any agent or human updating this repo must follow the canonical doc map above.
- Updates to CURRENT_STATE.md should mark items as Confirmed / Unverified / Missing / Risk.
- DECISIONS_LOG.md entries are append-only with dates.
- ROADMAP.md phases update only when a phase completes or is replanned.
- If two docs conflict, the canonical file for that topic wins. Flag and fix the other.

## Repo update workflow

When specific events happen, update the corresponding canonical file immediately. Do not batch updates or defer them.

| Event | Update required |
|-------|----------------|
| Software installed or removed | CURRENT_STATE.md (version + status tag) and INSTALL_BASELINE.md (move item to correct section) |
| Major architecture or policy decision | DECISIONS_LOG.md (append new DEC-NNN entry) |
| Roadmap phase completed or replanned | ROADMAP.md (check off exit criteria, update phase status) |
| New project onboarded | Copy PROJECT_INTAKE_TEMPLATE.md, fill it out, store in project's own repo |
| Security posture changed | SECURITY_BASELINE.md and CURRENT_STATE.md (risks table) |
| Agent role or authority changed | AGENT_ROLE_MATRIX.md |
| Tool approved or removed from stack | STACK_STANDARD.md and DECISIONS_LOG.md |

**After any update:** Commit with a descriptive message and push directly to `main`. This repo uses direct-push flow, not feature branches/PRs (see DEC-013). Do not let the repo drift from reality.

## Truth hierarchy (within this repo)

1. SSOT.md — governance and operating model
2. DECISIONS_LOG.md — what was decided and why
3. CURRENT_STATE.md — what exists right now
4. STACK_STANDARD.md — what is approved
5. All other docs — operational details under the above
