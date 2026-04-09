# Software 4 All — Current State

Last updated: 2026-04-09 (CTO correction pass)

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

## Known risks

| Risk | Severity | Details |
|------|----------|---------|
| Ports 3000/3002 exposed | High | Were exposed to network; source services unknown. Need firewall. |
| No firewall confirmed | High | UFW status not verified. |
| CUDA version mismatch | Medium | Toolkit 12.0 installed, driver supports 13.0. May limit model performance. |
| cuDNN missing | Medium | Required for many ML workloads. |
| No secrets manager | Medium | API keys and credentials have no centralized secure storage. |
| Duplicate AKIOR repos | Low | ~/akior and ~/projects/akior may both exist. Need consolidation. |
| Multiple remote access | Low | RustDesk and GNOME Remote Desktop may both be active. |
| pnpm/bun path overlap | Low | Both installed; need clear standard for which is used when. |
| Docker missing | Medium | Container runtime not available for isolated builds. |

## What is working

- Paperclip control plane operational
- OpenCode builds functional
- Claude Code operational with hooks, guards, and oh-my-claudecode hardening
- Codex available for review
- OpenClaw gateway running
- Ollama serving local models
- Git/GitHub workflow functional
- VS Code available
- GPU0 (96GB) available for inference

## What is not yet verified

- GPU multi-device routing and scheduling
- Ollama model loading across GPU1/GPU2
- Full delivery pipeline end-to-end
- Automated testing in pipeline
- Backup and restore procedures
- Network security posture
