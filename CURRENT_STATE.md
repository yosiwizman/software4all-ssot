# Software 4 All — Current State

Last updated: 2026-04-09 (Phase 2A — runtime completion partial)

This file tracks the actual state of the machine and ecosystem. Update this file whenever infrastructure changes. Mark every item with a status tag.

**Status tags:** `Confirmed` = verified directly | `Unverified` = believed true, not re-checked | `Missing` = not installed/configured | `Risk` = known issue needing attention

---

## Hardware

| Component | Spec | Status |
|-----------|------|--------|
| CPU | AMD Ryzen Threadripper 7970X (32-core) | Confirmed |
| Motherboard | ASUS Pro WS TRX50-SAGE WIFI | Confirmed |
| RAM | 128GB DDR5 ECC RDIMM | Confirmed |
| GPU0 | NVIDIA RTX PRO 6000 Blackwell 96GB | Confirmed |
| GPU1 | NVIDIA RTX 3090 24GB | Confirmed |
| GPU2 | NVIDIA RTX 4060 Ti 16GB | Confirmed |
| OS drive | WD Blue SN580 1TB | Confirmed |
| Model storage | /mnt/models ~14TB NVMe | Confirmed |
| Hostname | ai-desktop-System-Product-Name | Confirmed |

## Operating system

| Item | Value | Status |
|------|-------|--------|
| OS | Ubuntu 24.04.4 LTS | Confirmed |
| Kernel | 6.17.0-20-generic | Confirmed |
| NVIDIA driver | 580.126.09 | Confirmed |
| Driver CUDA support | Up to 13.0 | Confirmed |
| Installed CUDA toolkit | 12.0.140 (Ubuntu `nvidia-cuda-toolkit` package) | Risk — driver supports 13.0, toolkit is 12.0 |
| cuDNN (system) | Not installed as system package | Missing — Ollama bundles its own cuDNN 9.20.0 internally |

## AI / Agent stack

| Tool | Version | Status |
|------|---------|--------|
| Paperclip | 0.3.1 | Confirmed |
| OpenCode | 1.4.0 | Confirmed |
| oh-my-openagent | installed | Confirmed |
| Claude Code | 2.1.97 | Confirmed |
| Claude Desktop | 1.569.0 | Confirmed |
| oh-my-claudecode | 4.11.4 (19 agents, 37 skills) | Confirmed |
| Codex | 0.118.0 | Confirmed |
| OpenClaw | 2026.4.2 | Confirmed |
| Ollama | 0.20.2 | Confirmed |

## Dev tools

| Tool | Version | Status |
|------|---------|--------|
| git | 2.43.0 | Confirmed |
| gh | 2.45.0 | Confirmed |
| python3 | 3.12.3 | Confirmed |
| pip | 24.0 | Confirmed |
| pipx | 1.4.3 | Confirmed |
| node | 22.22.2 | Confirmed |
| npm | 10.9.7 | Confirmed |
| pnpm | 10.33.0 | Confirmed |
| bun | 1.3.11 | Confirmed |
| go | 1.26.1 | Confirmed |
| gcc/g++ | 13.3.0 | Confirmed |
| make | 4.3 | Confirmed |
| cmake | 3.28.3 | Confirmed |
| jq | 1.7 | Confirmed |
| ripgrep | 14.1.0 | Confirmed |
| tmux | 3.4 | Confirmed |
| sqlite3 | 3.45.1 | Confirmed |
| VS Code | 1.114.0 | Confirmed |
| Docker | not installed | Missing — requires sudo to install |
| uv | 0.11.6 | Confirmed |

## Browsers

| Browser | Status |
|---------|--------|
| Chrome | Confirmed |
| Firefox | Confirmed |
| Playwright | Confirmed — project-local in Paperclip node_modules |

## Security posture

| Item | Value | Status |
|------|-------|--------|
| UFW (firewall) | Active, enabled on startup. Default: deny incoming, allow outgoing. Logging: low. | Confirmed |
| RustDesk | v1.3.8 active (service + server + tray) | Confirmed — chosen as standard remote access |
| GNOME Remote Desktop | System service active, RDP disabled, no credentials set | Confirmed — effectively inactive, not a risk |
| Port 3000 | Was exposed (Jarvis dev-proxy on 0.0.0.0) — process killed | Confirmed resolved — no auto-restart mechanism found |
| Port 3002 | Nothing listening | Confirmed — no service on this port |
| Externally-reachable TCP | None after port 3000 fix | Confirmed — all TCP listeners now localhost-only |
| Secrets management | Policy-only baseline — gitignore enforcement, no centralized tool yet | Confirmed — sops+age recommended for future |

## Runtime port ownership

| Port | Owner project | Service | Status |
|------|--------------|---------|--------|
| 3100 | Software 4 All / Paperclip | Control plane local entrypoint | Confirmed — localhost-only |
| 3000 | Jarvis / AKIOR | Dev proxy (when running) | Not S4A — Jarvis project-specific, localhost-only |
| 3001 | Jarvis / AKIOR | Next.js dev server (when running) | Not S4A — Jarvis project-specific, localhost-only |
| 3002 | Jarvis / AKIOR | Reserved for Jarvis dev use | Not S4A — currently unused |

See DEC-016 for the formal separation policy.

## Known risks

| Risk | Severity | Details |
|------|----------|---------|
| ~~UFW not configured~~ | ~~High~~ | **Resolved** — Active, deny incoming, allow outgoing, enabled on startup. |
| CUDA version mismatch | Medium | Toolkit 12.0.140 (Ubuntu pkg) vs driver 13.0 capability. Upgrade requires NVIDIA repo, not trivial. See CUDA audit notes below. |
| cuDNN missing (system) | Medium | Ollama bundles cuDNN 9.20.0 internally. System-wide cuDNN for custom ML workloads not installed. |
| No secrets manager tool | Medium | Policy baseline set. No centralized tool yet. sops+age recommended for later phase. |
| Duplicate AKIOR repos | Low | ~/akior and ~/projects/akior may both exist. Need consolidation. |
| pnpm/bun path overlap | Low | Both installed; standard defined (pnpm for packages, bun for runtime only). |
| Docker missing | Medium | Container runtime not available for isolated builds. |

## What is working

- UFW firewall active (deny incoming, allow outgoing, enabled on startup)
- Paperclip control plane operational on localhost:3100
- OpenCode builds functional
- Claude Code operational with hooks, guards, and oh-my-claudecode hardening
- Codex available for review
- OpenClaw gateway running (localhost-only on ports 18789/18791)
- Ollama serving local models (localhost-only on port 11434)
- Git/GitHub workflow functional
- VS Code available
- GPU0 (96GB) available for inference
- RustDesk remote access active
- All TCP services bound to localhost (no external exposure)
- uv Python package manager operational
- SSOT repo committed and pushed to GitHub

## CUDA / cuDNN audit (2026-04-09)

| Item | Value | Source |
|------|-------|--------|
| NVIDIA driver | 580.126.09 | `nvidia-smi` |
| Driver max CUDA | 13.0 | `nvidia-smi` header |
| Installed CUDA toolkit | 12.0.140 | `nvcc --version`, Ubuntu `nvidia-cuda-toolkit` package |
| CUDA install path | System packages (no `/usr/local/cuda` symlink) | `dpkg -l`, filesystem check |
| cuDNN (system) | Not installed | `dpkg -l` |
| cuDNN (Ollama internal) | 9.20.0 in `/usr/local/lib/ollama/mlx_cuda_v13/` | Filesystem check |
| GPU0 | RTX PRO 6000 Blackwell 96GB VRAM | `nvidia-smi` |
| GPU1 | RTX 3090 24GB VRAM | `nvidia-smi` |
| GPU2 | RTX 4060 Ti 16GB VRAM (desktop display attached) | `nvidia-smi` |

**CUDA upgrade assessment:**
- The installed toolkit (12.0) came from Ubuntu's `nvidia-cuda-toolkit` package, not from NVIDIA's CUDA repo.
- Upgrading to CUDA 13.x requires adding NVIDIA's official apt repository and may conflict with Ubuntu's packaged version.
- This is not a trivial `apt upgrade` — it requires careful package management to avoid breaking the driver or existing CUDA-dependent tools.
- **Recommendation:** Defer CUDA toolkit upgrade until a specific project requires CUDA 13.x features. The current 12.0 toolkit works with the 580.x driver for compilation and most workloads. Ollama already bundles its own CUDA 13 runtime internally.
- **cuDNN:** Install system cuDNN only when a project requires training or custom inference outside Ollama. Match cuDNN version to whichever CUDA toolkit is active at that time.

## What is not yet verified

- GPU multi-device routing and scheduling
- Ollama model loading across GPU1/GPU2
- Full delivery pipeline end-to-end
- Automated testing in pipeline
- Backup and restore procedures
