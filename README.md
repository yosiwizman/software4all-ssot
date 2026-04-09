# Software 4 All — SSOT Repository

This is the **single source of truth** for Software 4 All, a local-first AI software factory.

This repo contains company standards, architecture decisions, machine state, security baselines, delivery pipeline definitions, and project templates. It does **not** contain application source code or the Paperclip runtime.

## How to use this repo

- **Before building anything**, check the standards and pipeline docs here first.
- **Before installing software**, check INSTALL_BASELINE.md for approved tools.
- **Before making architecture decisions**, check DECISIONS_LOG.md for prior decisions.
- **When machine state changes**, update CURRENT_STATE.md.
- **When starting a new project**, copy PROJECT_INTAKE_TEMPLATE.md and fill it out.

## Document map

| File | Purpose |
|------|---------|
| [SSOT.md](SSOT.md) | Master index and governance rules |
| [ROADMAP.md](ROADMAP.md) | Phased plan from setup to production |
| [CURRENT_STATE.md](CURRENT_STATE.md) | Live machine and ecosystem state |
| [STACK_STANDARD.md](STACK_STANDARD.md) | Approved tools, roles, and boundaries |
| [INSTALL_BASELINE.md](INSTALL_BASELINE.md) | What to install, in what order |
| [DECISIONS_LOG.md](DECISIONS_LOG.md) | Append-only decision record |
| [AGENT_ROLE_MATRIX.md](AGENT_ROLE_MATRIX.md) | Who does what, with what authority |
| [PROJECT_INTAKE_TEMPLATE.md](PROJECT_INTAKE_TEMPLATE.md) | Template for new projects |
| [DELIVERY_PIPELINE.md](DELIVERY_PIPELINE.md) | End-to-end software delivery flow |
| [SECURITY_BASELINE.md](SECURITY_BASELINE.md) | Minimum security standards |

## Directories

| Directory | Purpose |
|-----------|---------|
| `templates/` | Reusable templates (intake, ADR, etc.) |
| `decisions/` | Individual ADR-style decision records |
| `reports/` | Audit and environment reports |
| `diagrams/` | Architecture visuals and text diagrams |

## Governance

- This repo is owned by the CEO (Yosi).
- AI agents may propose changes but must follow the rules in SSOT.md.
- All major decisions are logged in DECISIONS_LOG.md.
- See SSOT.md for the full governance model.
