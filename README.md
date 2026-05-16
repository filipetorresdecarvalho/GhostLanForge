# GhostLanForge

**Forge your Windows PC and Linux VPS into one invisible supercomputer.**

GhostLanForge is a suite of micro applications that run on top of [NetBird](https://github.com/netbirdio/netbird), turning a simple WireGuard VPN overlay into a powerful private computing platform. Your machines share GPU, vector databases, AI tools, files, and more — all through an encrypted tunnel with zero open ports.

Your VPS gets your GPU. Your PC gets the cloud's vector databases, Docker containers, and MCP services. Both machines forge each other into something neither could be alone.

---

## What It Does

```
┌──────────────────────┐         ┌──────────────────────┐
│   Your Windows PC    │         │     Your VPS          │
│                      │         │                      │
│  • GPU (RTX 4090)    │◄──────►│  • Vector Database    │
│  • Local Files       │  Forge  │  • MCP Server         │
│  • AI Models         │  Link   │  • Docker Containers  │
│  • Windows Apps      │         │  • Databases          │
│                      │         │  • Processing Power   │
└──────────────────────┘         └──────────────────────┘
              ▲                           ▲
              │      NetBird (WireGuard)  │
              │      Private Network      │
              │                           │
              └───────────┬───────────────┘
                          │
              The Internet sees NOTHING
              No open ports on VPS
              Encrypted P2P tunnel
              Zero Trust access control
```

---

## Foundation: NetBird

GhostLanForge builds on [NetBird](https://github.com/netbirdio/netbird) (25.3k stars, Go, WireGuard-based, fully self-hostable). NetBird handles the hard networking so GhostLanForge can focus on the experience:

| NetBird Handles (free) | GhostLanForge Adds |
|------------------------|-------------------|
| WireGuard VPN tunnels | Beautiful WPF dashboard |
| P2P encrypted connections | Bitwarden integration |
| NAT traversal (STUN/TURN) | GPU sharing server |
| Access control + policies | MCP protocol bridge |
| Private DNS | Service discovery |
| SSO/MFA | Bidirectional file sharing |
| REST API + gRPC | Diagnostics + auto-fix |
| Admin dashboard | Vector DB client |
| Device posture checks | Interactive setup wizard |

---

## Micro Apps

Each GhostForge app is independent — install only what you need.

| App | Purpose | Platform |
|-----|---------|----------|
| **GhostForge.Setup** | Interactive setup wizard with system checks and auto-fix | Windows (WPF), Linux (CLI) |
| **GhostForge.Monitor** | Real-time dashboard — peers, latency, throughput, alerts | Windows (WPF) |
| **GhostForge.Vault** | Bitwarden CLI integration — secrets management for all apps | Windows, Linux |
| **GhostForge.MCP** | MCP protocol bridge — AI tools across machines | Windows (client), Linux (server) |
| **GhostForge.GPU** | Share Windows GPU with VPS for inference/training | Windows (server), Linux (client) |
| **GhostForge.Files** | Bidirectional file sharing — mounts, sync, drag-and-drop | Windows (drive), Linux (FUSE) |
| **GhostForge.Scan** | Auto-discover services running on NetBird peers | Windows, Linux |
| **GhostForge.Agent** | VPS server agent — stats, Docker management, health reports | Linux (systemd/Docker) |
| **GhostForge.Vector** | Vector DB client — query ChromaDB/Qdrant through the tunnel | Windows (WPF) |
| **GhostForge.Tunnel** | One-click tunnel management — expose services, configure routes | Windows (WPF) |
| **GhostForge.Diag** | Network diagnostics — health checks, MD reports, auto-fix | Windows, Linux |
| **GhostForge.WebAI** | Secure web AI proxy — prompt sanitizer, threat detection (future) | Windows, Linux |

See [ghostlanforge-ecosystem.md](ghostlanforge-ecosystem.md) for full details on each app.

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        Windows PC                                │
│                                                                  │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │              GhostForge.Monitor (WPF Dashboard)            │  │
│  └──────────┬──────────┬──────────┬──────────┬───────────────┘  │
│  ┌──────────▼──┐ ┌─────▼────┐ ┌──▼────────┐ ┌▼──────────────┐  │
│  │ Forge.Scan  │ │Forge.Diag│ │Forge.Tunnel│ │Forge.Vector   │  │
│  └──────┬──────┘ └────┬─────┘ └─────┬─────┘ └──────┬────────┘  │
│  ┌──────▼──────┐ ┌────▼─────┐ ┌────▼──────┐ ┌─────▼───────┐   │
│  │ Forge.Vault │ │Forge.GPU │ │Forge.Files│ │ Forge.MCP    │   │
│  └─────────────┘ └──────────┘ └───────────┘ └─────────────┘   │
│                          │                                      │
│              ┌───────────▼───────────┐                          │
│              │   NetBird Agent       │                          │
│              │   (WireGuard VPN)     │                          │
│              └───────────┬───────────┘                          │
└──────────────────────────┼──────────────────────────────────────┘
                           │              WireGuard P2P
                           │              (encrypted)
┌──────────────────────────┼──────────────────────────────────────┐
│                    Linux VPS │                                   │
│              ┌───────────▼───────────┐                          │
│              │   NetBird Agent       │                          │
│              └───────────┬───────────┘                          │
│         ┌────────────────┼────────────────┐                    │
│  ┌──────▼──────┐ ┌──────▼──────┐ ┌───────▼──────┐             │
│  │Forge.Agent  │ │ Forge.MCP   │ │ Forge.Files  │             │
│  └─────────────┘ └─────────────┘ └──────────────┘              │
│  ┌─────────────┐ ┌─────────────┐ ┌──────────────┐             │
│  │ ChromaDB    │ │ Docker      │ │ Grafana      │             │
│  └─────────────┘ └─────────────┘ └──────────────┘             │
└─────────────────────────────────────────────────────────────────┘
```

---

## Stack

| Component | Technology |
|-----------|-----------|
| VPN / Network | **NetBird** (WireGuard, self-hosted) |
| Windows UI | **WPF** (.NET 10, C#, MVVM) |
| Core Services | **Go** (single binary) |
| Secrets | **Bitwarden CLI** |
| AI Protocol | **MCP** (Model Context Protocol) |
| Vector DB | ChromaDB / Qdrant |
| Containers | Docker |
| VPS Agent | Go + systemd |

---

## Quick Start

### 1. Install NetBird

```powershell
# Windows
winget install NetBird.NetBird

# Linux VPS
curl -fsSL https://pkgs.netbird.io/install.sh | sh
netbird up --setup-key <YOUR_KEY>
```

Self-host the management server: https://netbird.io/docs/getting-started/self-hosting

### 2. Install GhostForge.Setup

Download from [GitHub Releases](https://github.com/filipetorresdecarvalho/GhostLanForge/releases) and run the setup wizard. It will detect your NetBird network, check prerequisites, and help you install the micro apps you want.

### 3. Install GhostForge.Agent on VPS

```bash
curl -sSL https://github.com/filipetorresdecarvalho/GhostForge.Agent/releases/latest/download/install.sh | bash
```

---

## Repositories

| Repo | Description |
|------|-------------|
| [GhostLanForge](https://github.com/filipetorresdecarvalho/GhostLanForge) | This repo — ecosystem documentation |
| GhostForge.Setup | Interactive setup wizard |
| GhostForge.Monitor | Dashboard + monitoring |
| GhostForge.Vault | Bitwarden integration |
| GhostForge.MCP | MCP protocol bridge |
| GhostForge.GPU | GPU sharing |
| GhostForge.Files | File sharing |
| GhostForge.Scan | Service discovery |
| GhostForge.Agent | VPS server agent |
| GhostForge.Vector | Vector DB client |
| GhostForge.Tunnel | Tunnel management |
| GhostForge.Diag | Diagnostics + auto-fix |
| GhostForge.WebAI | Web AI proxy (future) |

---

## Documentation

| File | Description |
|------|-------------|
| [ghostlanforge-ecosystem.md](ghostlanforge-ecosystem.md) | Full ecosystem documentation with all micro apps |
| [prompt.md](prompt.md) | AI development prompt for background agents |
| [private-network-solution.md](private-network-solution.md) | Original private network technical details |
| [private-network-complete.md](private-network-complete.md) | Usage examples |
| [vps-solutions.md](vps-solutions.md) | VPS solution comparison (10 solutions) |
| [websocket-proxy-plan.md](websocket-proxy-plan.md) | Original Go implementation plan |

---

## Performance Targets

| Metric | Target |
|--------|--------|
| Connection Time | < 1.5s |
| Query Latency | 20-50ms |
| Throughput | Very High |
| Concurrent Connections | 500+ |
| Security | Zero Trust (NetBird + Bitwarden) |

---

## License

MIT License

---

**Created by**: Filipe Torres de Carvalho
**Status**: Planning Phase
**Built on**: [NetBird](https://github.com/netbirdio/netbird) (BSD-3 / AGPL-3)
**Languages**: Go + C# (.NET 10)
