# 🔨 GhostLanForge

**Forge your Windows PC and Linux VPS into one invisible supercomputer.**

GhostLanForge creates a private, bidirectional network between your local machine and cloud VPS using Cloudflare Zero Trust — making them behave as if they're on the same home router, while your server remains completely invisible to the internet. No open ports. No ping responses. No traces. Just pure, silent power.

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
              │     Cloudflare Zero Trust  │
              │     (Invisible Tunnel)     │
              │                           │
              └───────────┬───────────────┘
                          │
              🌐 The Internet sees NOTHING
              ❌ No ping
              ❌ No HTTP
              ❌ No open ports
              ❌ No scan responses
```

---

## Key Features

### 🔮 Invisible Server
- VPS has **ZERO** open ports
- Doesn't respond to `ping`, `nmap`, `curl`, or anything
- Completely invisible to port scanners and attackers
- No one even knows your server exists

### 🔁 Bidirectional Power
- **Windows → VPS**: Access Docker, vector DB, MCP, databases as if local
- **VPS → Windows**: Use your GPU, access files, run commands remotely
- Both machines amplify each other's capabilities

### 🏠 Same Network Feel
- Windows sees VPS at `vps.local`
- VPS sees Windows at `windows.local`
- Works from home, coffee shop, office — anywhere
- Low latency (20-50ms), Very High throughput

### 🛡️ Zero Trust Security
- Only **YOU** can connect (Bitwarden authentication)
- Cloudflare Zero Trust verification
- TLS 1.3 encryption
- Device-based access control

---

## Usage Examples

### Windows Uses VPS Vector Database

```python
import requests

# Access VPS vector DB as if it's local
response = requests.get('http://vector-db.local/api/query',
    json={'query': 'machine learning', 'top_k': 10})

results = response.json()
# Works like VPS is on your local network!
```

### VPS Uses Windows GPU

```python
import requests

# VPS accesses your Windows GPU as if it's local
response = requests.post('http://windows-gpu.local/api/generate',
    json={'prompt': 'A sunset over mountains', 'steps': 50})

image = response.json()['image']
# VPS just used YOUR GPU to generate an AI image!
```

### Bidirectional File Sharing

```python
# Windows: Get files from VPS
files = requests.get('http://vps.local/api/files').json()

# VPS: Get files from Windows
files = requests.get('http://windows-files.local/api/list').json()

# Both see each other's resources as local!
```

---

## Architecture

GhostLanForge uses **Cloudflare Tunnels + Zero Trust** to create outbound-only connections from both machines. No inbound ports. No exposed services. Just encrypted tunnels through Cloudflare's global network.

```
Windows (outbound only)  ──►  Cloudflare  ◄──  VPS (outbound only)
         No open ports         Zero Trust         No open ports
```

### Security Layers

1. **Layer 1**: Cloudflare Zero Trust Authentication (Bitwarden + 2FA)
2. **Layer 2**: Private Network Isolation (virtual LAN)
3. **Layer 3**: Outbound-Only Connections (no listening ports)
4. **Layer 4**: TLS 1.3 End-to-End Encryption
5. **Layer 5**: Application-Level Authentication (JWT tokens)

---

## Stack

| Component | Technology |
|-----------|-----------|
| Language | **Go** (Golang) |
| Transport | Cloudflare Tunnels |
| Auth | Cloudflare Zero Trust + Bitwarden |
| Protocol | WebSocket + MCP |
| Encryption | TLS 1.3 |
| Database | Vector DB (VPS side) |
| Container | Docker |
| Monitoring | Prometheus + Grafana |

---

## Quick Start

### Prerequisites

- Go 1.21+
- Bitwarden CLI (`npm install -g @bitwarden/cli`)
- Cloudflare account with Zero Trust enabled
- Linux VPS (Ubuntu 20.04+ recommended)
- Windows 10/11

### Install

```bash
# Clone the repo
git clone https://github.com/filipetorresdecarvalho/GhostLanForge.git
cd GhostLanForge

# Build
go build -o ghostlanforge ./cmd/client

# Run the interactive setup
./ghostlanforge setup
```

The setup wizard will:
1. Check your system requirements
2. Ask relevant configuration questions
3. Test connectivity to your VPS
4. Configure Cloudflare Zero Trust
5. Set up Bitwarden authentication
6. Start the forge link

### Deploy Server (VPS)

```bash
# On your VPS
curl -sSL https://raw.githubusercontent.com/filipetorresdecarvalho/GhostLanForge/main/scripts/install-server.sh | bash
```

---

## Project Structure

```
GhostLanForge/
├── cmd/
│   ├── server/main.go          # VPS server daemon
│   └── client/main.go          # Windows CLI client
├── internal/
│   ├── forge/                  # Core forge link engine
│   ├── tunnel/                 # Cloudflare tunnel management
│   ├── auth/                   # Bitwarden + Zero Trust auth
│   ├── mcp/                    # MCP protocol client
│   ├── setup/                  # Interactive setup wizard
│   └── diag/                   # Diagnostics & auto-fix
├── pkg/
│   ├── ghostnet/               # Private network abstraction
│   ├── bitwarden/              # Bitwarden CLI integration
│   └── cloudflare/             # CF tunnel SDK
├── configs/
│   ├── server.yaml
│   └── client.yaml
├── scripts/
│   ├── install-server.sh
│   └── install-client.ps1
├── docs/
│   ├── architecture.md
│   ├── setup-guide.md
│   └── troubleshooting.md
└── tests/
```

---

## Documentation

| File | Description |
|------|-------------|
| `docs/architecture.md` | Full architecture documentation |
| `docs/setup-guide.md` | Step-by-step setup instructions |
| `docs/troubleshooting.md` | Common issues and solutions |
| `prompt.md` | AI development prompt |
| `private-network-solution.md` | Private network technical details |
| `private-network-complete.md` | Complete usage examples |
| `vps-solutions.md` | Solution comparison matrix |
| `websocket-proxy-plan.md` | Go implementation plan |

---

## Performance Targets

| Metric | Target |
|--------|--------|
| Connection Time | < 1.5s |
| Query Latency | 20-50ms |
| Throughput | Very High |
| Concurrent Connections | 500+ |
| Security | Zero Trust |

---

## License

MIT License

---

**Created by**: Filipe Torres de Carvalho
**Status**: Planning Phase
**Language**: Go
