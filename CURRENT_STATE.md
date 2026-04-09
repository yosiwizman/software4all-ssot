# Software 4 All — Current State

Last updated: 2026-04-09 (Phase 1 security baseline)

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
| Installed CUDA toolkit | 12.0 | Risk — mismatch with driver capability |
| cuDNN | Not installed | Missing |

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
| Docker | not installed | Missing |
| uv | not installed | Missing |

## Browsers

| Browser | Status |
|---------|--------|
| Chrome | Confirmed |
| Firefox | Confirmed |
| Playwright | Confirmed — project-local in Paperclip node_modules |

## Security posture

| Item | Value | Status |
|------|-------|--------|
| UFW (firewall) | Installed (v0.36.2-6) but status unverified — requires sudo | Risk — needs CEO to run `sudo ufw status` and configure |
| RustDesk | v1.3.8 active (service + server + tray) | Confirmed — chosen as standard remote access |
| GNOME Remote Desktop | System service active, RDP disabled, no credentials set | Confirmed — effectively inactive, not a risk |
| Port 3000 | Was exposed (Jarvis dev-proxy on 0.0.0.0) — process killed | Confirmed resolved — no auto-restart mechanism found |
| Port 3002 | Nothing listening | Confirmed — no service on this port |
| Externally-reachable TCP | None after port 3000 fix | Confirmed — all TCP listeners now localhost-only |
| Secrets management | Policy-only baseline — gitignore enforcement, no centralized tool yet | Confirmed — sops+age recommended for future |

## Known risks

| Risk | Severity | Details |
|------|----------|---------|
| UFW not configured | High | Installed but requires sudo to verify/configure. CEO action needed. See SECURITY_BASELINE.md. |
| CUDA version mismatch | Medium | Toolkit 12.0 installed, driver supports 13.0. May limit model performance. |
| cuDNN missing | Medium | Required for many ML workloads. |
| No secrets manager tool | Medium | Policy baseline set. No centralized tool yet. sops+age recommended for later phase. |
| Duplicate AKIOR repos | Low | ~/akior and ~/projects/akior may both exist. Need consolidation. |
| pnpm/bun path overlap | Low | Both installed; standard defined (pnpm for packages, bun for runtime only). |
| Docker missing | Medium | Container runtime not available for isolated builds. |

## What is working

- Paperclip control plane operational
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
- SSOT repo committed and pushed to GitHub

## What is not yet verified

- UFW firewall status and rules (requires sudo)
- GPU multi-device routing and scheduling
- Ollama model loading across GPU1/GPU2
- Full delivery pipeline end-to-end
- Automated testing in pipeline
- Backup and restore procedures
