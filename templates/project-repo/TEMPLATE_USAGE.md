# Project Repo Template — Usage Guide

This template defines the standard file structure for a new Software 4 All project repo. It is stack-agnostic — builder agents add language/framework-specific files during the build phase.

## How to use

1. **Complete intake first.** Fill out `PROJECT_INTAKE_TEMPLATE.md` (in the SSOT repo root) and get CEO approval before creating a repo.

2. **Create the project repo:**
   ```bash
   cd ~/projects
   mkdir <project-name> && cd <project-name>
   git init
   ```

3. **Copy this template into the new repo (excluding this usage guide):**
   ```bash
   cd ~/software4all-ssot/templates/project-repo
   cp README.md CLAUDE.md .gitignore <project-repo>/
   cp -r docs <project-repo>/
   ```
   **Do not copy TEMPLATE_USAGE.md** — it is reference material for the SSOT repo, not a project file.

4. **Fill in placeholders.** Search for `{{PLACEHOLDER}}` markers in the copied files and replace them with project-specific values from the approved intake.

5. **Commit the scaffold:**
   ```bash
   git add -A && git commit -m "Initial scaffold from S4A project template"
   ```

6. **Builder agent takes over** from here, following the build path defined in the intake.

## Placeholder reference

| Placeholder | Source | Example |
|-------------|--------|---------|
| `{{PROJECT_NAME}}` | Intake: Project name | `invoice-tracker` |
| `{{PROJECT_DESCRIPTION}}` | Intake: Business goal | `Automated invoice processing for client billing` |
| `{{REPO_URL}}` | Intake: Repo plan | `github.com/yosiwizman/invoice-tracker` |
| `{{PRIMARY_STACK}}` | Intake: Stack choice | `TypeScript / Next.js` |
| `{{BUILDER_AGENT}}` | Intake: Builder agent | `OpenCode` |

## What this template provides

- **README.md** — Project overview (fill from intake)
- **CLAUDE.md** — Agent instructions for builders working in this repo
- **.gitignore** — Standard ignores covering JS/TS, Python, and common patterns
- **docs/.gitkeep** — Placeholder for project-specific documentation

## What this template does NOT provide

- Language/framework scaffolding (package.json, requirements.txt, etc.) — builder adds these
- Source code structure (src/, app/, etc.) — builder creates per stack choice
- CI/CD config — deferred until Phase 5 of the S4A roadmap
- Dockerfile — builder adds only if intake specifies containerized deployment

## Rules

- Do not add business logic or company standards to the project repo — those live in `software4all-ssot`
- Do not duplicate SSOT decisions — cross-reference the SSOT repo instead
- The project CLAUDE.md should contain only project-specific agent instructions
