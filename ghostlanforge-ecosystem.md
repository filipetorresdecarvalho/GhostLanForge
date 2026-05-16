# GhostLanForge Ecosystem

> GhostLanForge is a suite of micro applications that run on top of NetBird, turning a simple VPN overlay into a powerful private computing platform.

---

## Table of Contents

1. [Why NetBird](#why-netbird)
2. [Alternative Platforms Compared](#alternative-platforms-compared)
3. [NetBird Deep Dive](#netbird-deep-dive)
4. [NetBird Extensibility](#netbird-extensibility)
5. [The GhostLanForge Approach](#the-ghostlanforge-approach)
6. [Micro Apps](#micro-apps)
7. [Architecture](#architecture)
8. [Development Stack](#development-stack)
9. [Getting Started](#getting-started)

---

## Why NetBird

After evaluating all major open-source overlay networking platforms, NetBird was chosen as the foundation for GhostLanForge because:

| Requirement | NetBird | Tailscale | ZeroTier | Nebula |
|-------------|---------|-----------|----------|--------|
| Fully self-hosted | Yes | No (control plane SaaS) | Partial | Yes |
| Open source license | BSD-3 / AGPL-3 | BSD-3 | MPL-2.0 + source-available | MIT |
| Written in Go | Yes | Yes | No (C++) | Yes |
| WireGuard based | Yes | Yes | No (custom protocol) | No (Noise Protocol) |
| REST API for management | Yes (full) | Yes | Yes | No (config files only) |
| gRPC daemon protocol | Yes | No | No | No |
| NAT traversal (STUN/TURN) | Yes | Yes | Yes | Yes |
| P2P encrypted | Yes | Yes | Yes | Yes |
| Windows support | Yes (tray + CLI) | Yes (tray + CLI) | Yes (CLI) | Yes (CLI) |
| Android/iOS apps | Yes | Yes | Yes | Yes |
| Admin Web UI | Yes (open source) | Yes (SaaS) | Yes | No |
| Private DNS | Yes | Yes | Yes | Yes |
| Access control groups | Yes | Yes | Yes | Yes |
| SSO/MFA | Yes | Yes | No | No |
| Device posture checks | Yes | Yes | No | No |
| Free tier | Unlimited (self-hosted) | 100 devices | 25 devices | Unlimited |
| Active development | Very active (325 releases) | Very active (151 releases) | Moderate | Slow |
| Docker deployment | Yes | No | No | No |
| Quantum resistant | Yes (Rosenpass) | No | No | No |
| Event streaming | Yes | No | No | No |
| Terraform provider | Yes | Yes | No | No |

### Decision Summary

- **NetBird** wins on self-hostability, API richness, Go ecosystem, and active development
- **Tailscale** is the most polished but forces SaaS control plane
- **ZeroTier** is mature but C++, source-available license, and 25-device free limit
- **Nebula** (by Slack) is simple and MIT licensed but lacks API, UI, and active development

---

## Alternative Platforms Compared

### 1. Tailscale

- **GitHub**: https://github.com/tailscale/tailscale
- **Stars**: 31.6k
- **Language**: Go (95.4%)
- **License**: BSD-3-Clause
- **Based on**: WireGuard
- **Self-hosted**: Partially (Headscale is the unofficial self-hosted control server)
- **Strengths**: Most polished UX, MagicDNS, Taildrop (file sharing), Tailscale SSH, Funnel (expose services), iOS/Android apps
- **Weaknesses**: Control plane is proprietary SaaS (Headscale is unofficial), free tier limited to 100 devices, no native Bitwarden/MCP/resource sharing
- **Verdict**: Best for users who don't mind SaaS. Headscale adds self-hosting but is unofficial and may lag behind.

### 2. NetBird

- **GitHub**: https://github.com/netbirdio/netbird
- **Stars**: 25.3k
- **Language**: Go (97%)
- **License**: BSD-3-Clause (client) / AGPL-3 (management, signal, relay)
- **Based on**: WireGuard + Pion ICE (WebRTC)
- **Self-hosted**: Fully (management + signal + relay, all Docker-based)
- **Strengths**: Full REST API, gRPC daemon protocol, self-hosted, Docker quickstart, admin dashboard (open source), SSO/MFA, device posture, quantum resistant (Rosenpass), Terraform provider, event streaming
- **Weaknesses**: Younger project than Tailscale, less polished mobile apps, no built-in file sharing
- **Verdict**: Best self-hosted option with richest API. Chosen as GhostLanForge foundation.

### 3. ZeroTier

- **GitHub**: https://github.com/zerotier/ZeroTierOne
- **Stars**: 16.7k
- **Language**: C++ (78.7%)
- **License**: MPL-2.0 (core) + source-available (nonfree/)
- **Based on**: Custom P2P protocol (not WireGuard)
- **Self-hosted**: Partially (can self-host controllers, but moon/planet infrastructure is centralized)
- **Strengths**: Mature (since 2015), very fast, Layer 2 bridging, programmable rules, large community
- **Weaknesses**: C++ (harder to contribute), source-available license for some features, 25-device free limit, no SSO/MFA on free tier, not WireGuard
- **Verdict**: Best for raw performance and Layer 2 needs. Less suitable for Go ecosystem and full self-hosting.

### 4. Nebula (by Slack)

- **GitHub**: https://github.com/slackhq/nebula
- **Stars**: 17.3k
- **Language**: Go (99.5%)
- **License**: MIT
- **Based on**: Noise Protocol Framework (not WireGuard)
- **Self-hosted**: Fully (just needs a lighthouse with public IP)
- **Strengths**: Extremely lightweight, MIT license, simple config, security groups, built by Slack, works on constrained devices
- **Weaknesses**: No REST API, no admin UI, no SSO/MFA, slow development (last release Feb 2026, only 27 releases total), config-file only management, mobile apps are paid
- **Verdict**: Best for simple, lightweight deployments. Lacks the API and management features needed for GhostLanForge.

### 5. Cloudflare Tunnels (GhostLanForge original design)

- **Not open source** (proprietary transport)
- **Strengths**: Zero open ports by design, Cloudflare global network, Zero Trust access
- **Weaknesses**: Proprietary, depends on Cloudflare, no P2P (all traffic goes through CF), not a mesh network, no API for network management
- **Verdict**: Replaced by NetBird. Cloudflare Tunnels can still be used as an additional transport layer if needed.

---

## NetBird Deep Dive

### What NetBird Provides Out of the Box

| Layer | Component | Description |
|-------|-----------|-------------|
| **Transport** | WireGuard tunnels | Encrypted P2P between all machines |
| **Discovery** | Signal service | Helps peers find each other |
| **NAT Traversal** | STUN/TURN (Coturn) | Hole punching, relay fallback |
| **Management** | Management service | Central config, peers, policies, DNS |
| **Access Control** | Groups + Policies | Fine-grained firewall rules |
| **DNS** | Private DNS zones | `machine.netbird.cloud` or custom |
| **Auth** | SSO/MFA | Google, GitHub, Azure, OIDC, Okta |
| **API** | REST + gRPC | Full programmatic control |
| **UI** | Admin Dashboard | Web-based network management |
| **Security** | Posture checks | OS version, agent version checks |
| **Encryption** | WireGuard + Rosenpass | Quantum-resistant option |

### NetBird Architecture

```
┌─────────────────────────────────────────────────┐
│                  Your Machines                   │
│                                                  │
│  ┌──────────┐    ┌──────────┐   ┌──────────┐   │
│  │ Windows  │◄──►│  Linux   │◄──►│ Android  │   │
│  │ NetBird  │    │  NetBird │   │ NetBird  │   │
│  │ Agent    │    │  Agent   │   │ Agent    │   │
│  └────┬─────┘    └────┬─────┘   └────┬─────┘   │
│       │               │              │          │
│       └───────────────┼──────────────┘          │
│            WireGuard P2P (encrypted)            │
│               (direct or relay)                 │
└────────────────────────┬────────────────────────┘
                         │
            ┌────────────▼────────────┐
            │   Self-Hosted Server    │
            │                         │
            │  ┌──────────────────┐   │
            │  │ Management Svc   │   │
            │  │ (REST API, gRPC) │   │
            │  └──────────────────┘   │
            │  ┌──────────────────┐   │
            │  │ Signal Service   │   │
            │  │ (peer discovery) │   │
            │  └──────────────────┘   │
            │  ┌──────────────────┐   │
            │  │ TURN Relay       │   │
            │  │ (Coturn)         │   │
            │  └──────────────────┘   │
            └─────────────────────────┘
```

### NetBird REST API Endpoints

GhostLanForge micro apps can use these APIs:

| Resource | Endpoints | Use Case |
|----------|-----------|----------|
| **Peers** | GET/PUT/DELETE /peers | List machines, check status, update names |
| **Groups** | CRUD /groups | Organize machines (windows, vps, gpu-servers) |
| **Policies** | CRUD /policies | Define access rules between groups |
| **Routes** | CRUD /routes | Expose subnets through specific peers |
| **DNS** | CRUD /dns | Manage DNS nameserver groups |
| **Setup Keys** | CRUD /setup-keys | Pre-authenticate new machines |
| **Users** | CRUD /users | Manage network users |
| **Events** | GET /events | Audit log, connection events |
| **Posture Checks** | CRUD /posture-checks | Enforce device security requirements |
| **Networks** | CRUD /networks | Define network ranges and allocation |
| **Services** | CRUD /services | Register and discover services |

### NetBird gRPC Daemon Protocol

The NetBird agent exposes a local gRPC interface that allows local applications to:

- Get peer status and connection info
- Listen for network events
- Control the agent (up/down)

This is the integration point for the GhostLanForge Windows UI.

---

## NetBird Extensibility

### Does NetBird Support Plugins?

No. NetBird does not have a native plugin or addon system. However, this is actually an advantage for GhostLanForge because:

1. **No coupling to NetBird internals** — your apps don't break when NetBird updates
2. **Any language** — you're not limited to Go plugins
3. **Independent releases** — each micro app has its own release cycle
4. **Network-level integration** — your apps just use the private network that NetBird creates

### How GhostLanForge Extends NetBird

GhostLanForge micro apps integrate with NetBird at **three levels**:

```
┌──────────────────────────────────────┐
│  Level 3: Network Services           │  Your apps run on the private
│  (MCP, GPU, Files, Vector DB)        │  NetBird network as regular
│  Uses: NetBird IP addresses + DNS    │  TCP/UDP services
├──────────────────────────────────────┤
│  Level 2: Management API             │  Your apps call the NetBird
│  (Peers, Groups, Policies, Routes)   │  REST API to manage the network
├──────────────────────────────────────┤
│  Level 1: Local Agent                │  Your apps talk to the local
│  (gRPC daemon on Windows/Linux)      │  NetBird agent for status/events
└──────────────────────────────────────┘
```

---

## The GhostLanForge Approach

### Philosophy

GhostLanForge is not a fork or modification of NetBird. It is a **suite of independent micro applications** that:

1. **Require** a running NetBird network
2. **Use** the NetBird API for network management
3. **Run on** the NetBird private network
4. **Enhance** NetBird with capabilities it doesn't have

Each micro app is:
- **Independent** — can be installed/used alone or combined
- **Small** — single purpose, does one thing well
- **Self-documenting** — README explains the NetBird requirement
- **Composable** — apps can chain together

### Naming Convention

Each micro app follows the naming pattern:

```
GhostForge.<Purpose>
```

Examples:
- `GhostForge.Setup` — Interactive setup wizard
- `GhostForge.Monitor` — Dashboard + diagnostics
- `GhostForge.Vault` — Bitwarden integration
- `GhostForge.MCP` — MCP protocol bridge
- `GhostForge.GPU` — GPU sharing server
- `GhostForge.Files` — Bidirectional file sharing
- `GhostForge.Scan` — Service discovery
- `GhostForge.Agent` — VPS server agent

---

## Micro Apps

### App 1: GhostForge.Setup

**Purpose**: Interactive setup wizard that configures NetBird + all GhostForge apps

**What it does**:
- Checks system requirements (NetBird installed, Go, .NET, etc.)
- Validates NetBird connectivity (is the network running?)
- Generates beautiful MD error reports for failures
- Offers auto-fix for common issues
- Configures which GhostForge apps to install
- Sets up NetBird groups and policies for your use case
- Generates VPS server install commands

**Requires**: NetBird agent installed and connected

**Platform**: Windows (WPF), Linux (CLI)

**Repo**: `GhostForge.Setup`

---

### App 2: GhostForge.Monitor

**Purpose**: Beautiful real-time dashboard for your NetBird network

**What it does**:
- Shows all connected peers with status (online/offline/latency)
- Real-time throughput graphs per peer
- NetBird tunnel health monitoring
- DNS resolution status for private names
- Alert system (peer disconnected, high latency, tunnel down)
- System tray icon with forge link status
- Dark/light theme

**Requires**: NetBird running + NetBird API access token

**Platform**: Windows (WPF)

**Integration**: Uses NetBird REST API (`/peers`, `/events`) + local gRPC daemon

**Repo**: `GhostForge.Monitor`

---

### App 3: GhostForge.Vault

**Purpose**: Bitwarden CLI integration for your private network

**What it does**:
- Authenticates with Bitwarden via CLI
- Pulls secrets, API keys, and credentials from your vault
- Automatically configures other GhostForge apps with secrets
- Rotates credentials on schedule
- Shares select secrets across NetBird peers (encrypted)
- Never stores secrets on disk (memory only)

**Requires**: Bitwarden CLI installed + NetBird running

**Platform**: Windows (WPF service), Linux (daemon)

**Repo**: `GhostForge.Vault`

---

### App 4: GhostForge.MCP

**Purpose**: MCP (Model Context Protocol) bridge across your private network

**What it does**:
- Runs MCP server on VPS (or any NetBird peer)
- Exposes tools, resources, and prompts via MCP protocol
- Windows AI tools can discover and use VPS MCP tools through the tunnel
- Example: VPS exposes a "vector search" tool, Windows AI agent calls it
- Supports tool chaining across machines
- Authentication via GhostForge.Vault (Bitwarden)

**Use cases**:
- AI agent on Windows uses VPS vector DB via MCP
- AI agent on VPS uses Windows GPU via MCP
- Cross-machine tool orchestration

**Requires**: NetBird running (VPS and Windows must be peers)

**Platform**: Windows (client), Linux (server)

**Protocol**: MCP over TCP (through NetBird tunnel)

**Repo**: `GhostForge.MCP`

---

### App 5: GhostForge.GPU

**Purpose**: Share your Windows GPU with VPS and other NetBird peers

**What it does**:
- Detects available GPUs (NVIDIA, AMD)
- Exposes GPU as a network service on the NetBird private IP
- VPS can send inference requests to Windows GPU
- Supports CUDA, OpenCL, DirectML
- Rate limiting and job queuing
- Monitors GPU temperature, VRAM, utilization

**API**:
```
GET  /gpu/status          → GPU info, VRAM, temperature
POST /gpu/inference       → Submit inference job
GET  /gpu/inference/{id}  → Get job result
POST /gpu/train           → Submit training job
```

**Requires**: NetBird running + NVIDIA CUDA / AMD ROCm drivers

**Platform**: Windows (server, WPF service), Linux (client)

**Repo**: `GhostForge.GPU`

---

### App 6: GhostForge.Files

**Purpose**: Bidirectional file sharing between NetBird peers

**What it does**:
- Mount VPS filesystem on Windows as a network drive
- Mount Windows filesystem on VPS via FUSE
- Drag-and-drop file transfer in the dashboard
- Sync folders between machines (like rsync over private network)
- File versioning and conflict resolution
- Streaming for large files (no full download needed)

**Requires**: NetBird running

**Platform**: Windows (WPF + network provider), Linux (FUSE daemon)

**Integration**: Uses NetBird private IP + DNS names

**Repo**: `GhostForge.Files`

---

### App 7: GhostForge.Scan

**Purpose**: Automatic service discovery across your NetBird network

**What it does**:
- Scans all NetBird peers for running services
- Detects: Docker containers, web servers, databases, APIs, custom services
- Builds a service catalog with health status
- Presents services as clickable tiles in the dashboard
- Auto-generates access URLs using NetBird DNS names
- Notifies when new services appear or disappear
- Integrates with GhostForge.Monitor for real-time status

**Detection methods**:
- Port scanning (on private network only)
- Docker API query
- mDNS/DNS-SD on the overlay
- Manual service registration via API

**Requires**: NetBird running + NetBird API access

**Platform**: Windows (WPF scanner), Linux (agent + scanner)

**Repo**: `GhostForge.Scan`

---

### App 8: GhostForge.Agent

**Purpose**: Lightweight VPS server agent that reports to Windows

**What it does**:
- Runs as systemd service or Docker container on VPS
- Reports system stats (CPU, RAM, disk, network)
- Reports running Docker containers and their ports
- Reports GPU status (if available)
- Executes commands from GhostForge.Monitor (restart service, pull container, etc.)
- Registers services with GhostForge.Scan
- Health check endpoint for GhostForge.Monitor

**API**:
```
GET  /agent/status        → System stats
GET  /agent/services      → List running services
GET  /agent/docker        → List Docker containers
POST /agent/docker/pull   → Pull a Docker image
POST /agent/exec          → Execute a command (authenticated)
GET  /agent/health        → Health check
```

**Requires**: NetBird running on VPS

**Platform**: Linux (systemd/Docker)

**Install**: `curl -sSL https://github.com/.../GhostForge.Agent/install.sh | bash`

**Repo**: `GhostForge.Agent`

---

### App 9: GhostForge.Vector

**Purpose**: Easy vector database client that works through your private network

**What it does**:
- Connects to vector DBs (ChromaDB, Qdrant, Milvus, Weaviate) running on VPS
- Provides a Windows UI for querying and managing collections
- Embeds text using local or remote models
- Integrates with GhostForge.MCP for AI agent access
- Integrates with GhostForge.GPU for accelerated embedding
- Import/export collections

**Requires**: NetBird running + vector DB on VPS

**Platform**: Windows (WPF client)

**Repo**: `GhostForge.Vector`

---

### App 10: GhostForge.Tunnel

**Purpose**: Enhanced tunnel management on top of NetBird

**What it does**:
- Creates NetBird routes and policies from a simple UI
- Exposes local Windows services to VPS (e.g., local dev server)
- Exposes VPS services to Windows (e.g., VPS databases)
- One-click "expose service" button
- Auto-configures DNS entries
- Integrates with GhostForge.Scan for service detection

**Requires**: NetBird running + NetBird API admin access

**Platform**: Windows (WPF), uses NetBird REST API

**Repo**: `GhostForge.Tunnel`

---

### App 11: GhostForge.Diag

**Purpose**: Network diagnostics and auto-fix engine

**What it does**:
- Runs health checks against NetBird infrastructure
- Tests: tunnel connectivity, DNS resolution, latency, throughput, packet loss
- Generates MD-formatted diagnostic reports
- Compares results against baseline
- Suggests fixes for common issues
- Auto-fixes: restart NetBird agent, flush DNS, reset WireGuard interface
- Historical diagnostic data with trends

**Requires**: NetBird running

**Platform**: Windows (WPF), Linux (CLI)

**Repo**: `GhostForge.Diag`

---

### App 12: GhostForge.WebAI

**Purpose**: Secure web AI access layer (future)

**What it does**:
- Proxies web requests through VPS for AI tools
- Temporary DB for web content (auto-expires)
- Prompt sanitizer (removes sensitive data before sending to AI)
- Threat detection (scans URLs and content)
- Rate limiting and quota management
- Audit log of all AI requests

**Requires**: NetBird running + GhostForge.Vault for API keys

**Platform**: Windows (client), Linux (proxy server)

**Repo**: `GhostForge.WebAI`

---

## Architecture

### Overall System

```
┌─────────────────────────────────────────────────────────────────────┐
│                        Windows PC                                    │
│                                                                      │
│  ┌─────────────────────────────────────────────────────────────┐    │
│  │                GhostForge.Monitor (WPF)                      │    │
│  │     Dashboard • Status • Alerts • Service Tiles • Logs      │    │
│  └──────────┬──────────┬──────────┬──────────┬─────────────────┘    │
│             │          │          │          │                       │
│  ┌──────────▼──┐ ┌─────▼────┐ ┌──▼────────┐ ┌▼──────────────┐     │
│  │ Forge.Scan  │ │Forge.Diag│ │Forge.Tunnel│ │Forge.Vector  │     │
│  └──────┬──────┘ └────┬─────┘ └─────┬─────┘ └──────┬────────┘     │
│         │             │             │               │               │
│  ┌──────▼──────┐ ┌────▼─────┐ ┌────▼──────┐ ┌─────▼───────┐      │
│  │ Forge.Vault │ │Forge.GPU │ │Forge.Files│ │ Forge.MCP   │      │
│  │ (Bitwarden) │ │(GPU Srv) │ │ (Drives)  │ │(AI Bridge)  │      │
│  └─────────────┘ └──────────┘ └───────────┘ └─────────────┘       │
│                          │                                          │
│              ┌───────────▼───────────┐                              │
│              │   NetBird Agent       │                              │
│              │   (WireGuard VPN)     │                              │
│              └───────────┬───────────┘                              │
└──────────────────────────┼──────────────────────────────────────────┘
                           │
              WireGuard P2P (encrypted)
                           │
┌──────────────────────────┼──────────────────────────────────────────┐
│                    Linux VPS │                                       │
│              ┌───────────▼───────────┐                              │
│              │   NetBird Agent       │                              │
│              │   (WireGuard VPN)     │                              │
│              └───────────┬───────────┘                              │
│                          │                                          │
│         ┌────────────────┼────────────────┐                        │
│         │                │                │                         │
│  ┌──────▼──────┐ ┌──────▼──────┐ ┌───────▼──────┐                 │
│  │Forge.Agent  │ │ Forge.MCP   │ │ Forge.Files  │                 │
│  │(System Svc) │ │ (MCP Server)│ │ (FUSE Mount) │                 │
│  └─────────────┘ └─────────────┘ └──────────────┘                  │
│                                                                      │
│  ┌─────────────┐ ┌─────────────┐ ┌──────────────┐                  │
│  │ ChromaDB    │ │ Docker      │ │ Grafana      │                  │
│  │ (Vector DB) │ │ (Containers)│ │ (Monitoring) │                  │
│  └─────────────┘ └─────────────┘ └──────────────┘                  │
└─────────────────────────────────────────────────────────────────────┘
```

### Communication Flow

```
GhostForge.Monitor  ──REST API──►  NetBird Management Service
                                    (list peers, events, groups)

GhostForge.Monitor  ──gRPC──────►  NetBird Agent (local)
                                    (status, connect/disconnect)

GhostForge.Scan     ──TCP scan──►  NetBird private IPs
                                    (discover services on peers)

GhostForge.GPU      ──HTTP API──►  NetBird private IP:3002
                                    (GPU inference over tunnel)

GhostForge.MCP      ──MCP/TCP───►  NetBird private IP:8765
                                    (AI tools over tunnel)

GhostForge.Files    ──SMB/FUSE──►  NetBird private IP:3003
                                    (file sharing over tunnel)

GhostForge.Agent    ──HTTP API──►  NetBird private IP:3005
                                    (VPS management over tunnel)

GhostForge.Vault    ──CLI───────►  Bitwarden CLI (local)
                                    (secrets management)

GhostForge.Tunnel   ──REST API──►  NetBird Management Service
                                    (create routes, policies, DNS)

GhostForge.Diag     ──ICMP/TCP──►  NetBird private IPs
                                    (health checks over tunnel)
```

---

## Development Stack

| Component | Technology | Why |
|-----------|-----------|-----|
| Windows UI | WPF (.NET 10, C#, MVVM) | Native Windows, beautiful, already installed |
| Core Services | Go (Golang) | Single binary, fast, NetBird compatible |
| Communication | JSON-RPC + HTTP REST | Simple, language agnostic |
| Transport | NetBird (WireGuard) | Free, self-hosted, zero open ports |
| Secrets | Bitwarden CLI | Secure credential storage |
| Vector DB | ChromaDB / Qdrant | Open source, easy to use |
| AI Protocol | MCP (Model Context Protocol) | Standard for AI tool use |
| Containerization | Docker | VPS services |
| VPS Agent | Go + systemd | Lightweight, always running |

---

## Getting Started

### Prerequisites

1. **NetBird** installed and running on all machines
   - Windows: Download from https://netbird.io/install
   - Linux: `curl -fsSL https://pkgs.netbird.io/install.sh | sh`
   - Self-hosted server: Follow https://netbird.io/docs/getting-started/self-hosting

2. **Go 1.21+** for core services
3. **.NET 10 SDK** for Windows UI apps
4. **Bitwarden CLI** for GhostForge.Vault

### Installation Order

1. Install and configure NetBird (all machines connected)
2. Install GhostForge.Setup (runs the setup wizard)
3. Install GhostForge.Agent on VPS
4. Install desired micro apps on Windows
5. Configure via GhostForge.Setup or individual app settings

### Quick Install (Windows)

```powershell
# Install NetBird first
winget install NetBird.NetBird

# Install GhostForge.Setup
# (download from GitHub releases)

# Run setup wizard
GhostForge.Setup.exe
```

### Quick Install (VPS)

```bash
# Install NetBird first
curl -fsSL https://pkgs.netbird.io/install.sh | sh
netbird up --setup-key <YOUR_KEY>

# Install GhostForge.Agent
curl -sSL https://github.com/filipetorresdecarvalho/GhostForge.Agent/releases/latest/download/install.sh | bash
```

---

## Repository Structure

Each micro app is a separate repository under the GhostLanForge GitHub organization:

```
filipetorresdecarvalho/
├── GhostLanForge                  # This repo (ecosystem documentation)
├── GhostForge.Setup               # Setup wizard
├── GhostForge.Monitor             # Dashboard + monitoring
├── GhostForge.Vault               # Bitwarden integration
├── GhostForge.MCP                 # MCP protocol bridge
├── GhostForge.GPU                 # GPU sharing
├── GhostForge.Files               # File sharing
├── GhostForge.Scan                # Service discovery
├── GhostForge.Agent               # VPS server agent
├── GhostForge.Vector              # Vector DB client
├── GhostForge.Tunnel              # Tunnel management
├── GhostForge.Diag                # Diagnostics + auto-fix
└── GhostForge.WebAI               # Web AI proxy (future)
```

Each repo includes:
- `README.md` with "Requires NetBird" notice
- `CONTRIBUTING.md`
- `LICENSE` (MIT)
- Source code
- Release binaries
- Install scripts

---

## License

GhostLanForge and all GhostForge micro apps are released under the **MIT License**.

NetBird is licensed under BSD-3-Clause (client) and AGPL-3 (management/signal/relay services).

---

## Credits

- **NetBird** — https://github.com/netbirdio/netbird — BSD-3-Clause / AGPL-3
- **WireGuard** — https://www.wireguard.com/ — GPL-2.0
- **Tailscale** — https://github.com/tailscale/tailscale — BSD-3-Clause
- **ZeroTier** — https://github.com/zerotier/ZeroTierOne — MPL-2.0
- **Nebula (Slack)** — https://github.com/slackhq/nebula — MIT
