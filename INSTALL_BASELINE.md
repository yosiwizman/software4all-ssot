# Software 4 All — Install Baseline

Exact software baseline for the ai-desktop machine. Defines what is installed, what needs to be installed next, and what to avoid.

---

## Already installed and accepted

These tools are present, verified, and approved for use.

| Tool | Version | Notes |
|------|---------|-------|
| Ubuntu | 24.04.4 LTS | OS — do not upgrade major version without decision |
| NVIDIA driver | 580.126.09 | Supports CUDA 13.0 |
| CUDA toolkit | 12.0 | Functional but outdated — see "required next" |
| Paperclip | 0.3.1 | Control plane |
| OpenCode | 1.4.0 | Default builder |
| oh-my-openagent | installed | OpenCode hardening |
| Claude Code | 2.1.97 | Premium builder |
| Claude Desktop | 1.569.0 | GUI interface |
| Codex | 0.118.0 | Reviewer/fixer |
| OpenClaw | 2026.4.2 | Gateway/assistant |
| oh-my-claudecode | 4.11.4 | Claude Code hardening (19 agents, 37 skills) |
| Ollama | 0.20.2 | Local model runtime |
| git | 2.43.0 | Version control |
| gh | 2.45.0 | GitHub CLI |
| python3 | 3.12.3 | System Python |
| pip | 24.0 | Legacy Python packages |
| pipx | 1.4.3 | Isolated Python CLI tools |
| node | 22.22.2 | JS runtime |
| npm | 10.9.7 | Comes with node — use pnpm instead |
| pnpm | 10.33.0 | Standard JS package manager |
| bun | 1.3.11 | JS runtime only, not package manager |
| go | 1.26.1 | Go runtime |
| gcc/g++ | 13.3.0 | C/C++ compiler |
| make | 4.3 | Build tool |
| cmake | 3.28.3 | Build system generator |
| jq | 1.7 | JSON processor |
| ripgrep | 14.1.0 | Fast search |
| tmux | 3.4 | Terminal multiplexer |
| sqlite3 | 3.45.1 | Embedded database |
| VS Code | 1.114.0 | Standard IDE |
| Chrome | installed | Browser |
| Firefox | installed | Browser |
| UFW | 0.36.2-6 | Firewall — active, deny incoming, allow outgoing, enabled on startup |
| RustDesk | 1.3.8 | Standard remote access (DEC-014) |

## Required next installs

Install these in the order listed. Each is a prerequisite for the factory to function fully.

| Priority | Tool | Reason | Install method |
|----------|------|--------|----------------|
| 1 | **Docker** | Container runtime for isolated builds. Many projects require it. | `sudo apt install docker.io` + add user to docker group |
| 2 | **uv** | Python package/project manager. Replaces pip for new projects. | `curl -LsSf https://astral.sh/uv/install.sh \| sh` |
| 3 | **CUDA toolkit upgrade** | Current 12.0 should match driver's 13.0 capability. | Follow NVIDIA's official Ubuntu 24.04 repo instructions |
| 4 | **cuDNN** | Required for ML model training and some inference workloads. | Install after CUDA upgrade, matching CUDA version |

## Install later (not blocking)

| Tool | Reason | When to install |
|------|--------|-----------------|
| Secrets manager (sops, age, or vault) | Centralized credential management | Phase 1, after security baseline review |
| Observability stack | Log aggregation and error tracking | Phase 5, production hardening |
| GPU monitoring tools (nvtop) | Multi-GPU workload visibility | Phase 2, with GPU routing rules |

## Not approved — do not install

| Tool | Reason |
|------|--------|
| Podman | Docker is the container standard |
| Cursor | VS Code is the IDE standard |
| Windsurf | VS Code is the IDE standard |
| Poetry | uv is the Python standard |
| Additional inference servers (vLLM, TGI, etc.) | Ollama is sufficient unless explicitly approved |
| yarn | pnpm is the JS package manager standard |

Installing anything from this list requires a DECISIONS_LOG.md entry and CEO approval.
