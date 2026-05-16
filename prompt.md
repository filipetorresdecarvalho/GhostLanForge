# GhostLanForge - AI Development Prompt

> This is the initial prompt to give to an AI coding agent (like opencode, Claude Code, Cursor, etc.) to build GhostLanForge from scratch. The AI should read this document first, then read all documentation in the `docs/` folder and the root `.md` files before writing any code.

---

## Project Identity

**Name**: GhostLanForge
**Tagline**: Forge your Windows PC and Linux VPS into one invisible supercomputer.
**Repo**: https://github.com/filipetorresdecarvalho/GhostLanForge
**License**: MIT

---

## What GhostLanForge Does

GhostLanForge creates a private, bidirectional network between a Windows PC and a Linux VPS using Cloudflare Zero Trust + Cloudflare Tunnels. The result:

- Windows and VPS see each other as if on the same home router (`vps.local`, `windows.local`)
- VPS has ZERO open ports — no ping, no HTTP, no scan responses, invisible to everyone
- Windows can use VPS resources (vector DB, MCP, Docker, databases) as if local
- VPS can use Windows resources (GPU, files, AI models) as if local
- Only the authenticated user (via Bitwarden + Cloudflare Zero Trust) can connect
- Works from anywhere: home, office, coffee shop

---

## Architecture

```
┌──────────────────────────┐
│  GhostLanForge UI (WPF)  │  C# / .NET 10 / MVVM
│  Beautiful Windows app    │  System tray + dashboard
├──────────────────────────┤
│  GhostLanForge Core (Go)  │  Networking engine
│  Single binary embedded   │  CF Tunnel + WebSocket + MCP
├──────────────────────────┤
│  Cloudflare Zero Trust    │  Auth + Transport
│  Bitwarden CLI            │  Secrets management
└──────────────────────────┘
```

### Technology Stack

| Layer | Technology | Why |
|-------|-----------|-----|
| Windows UI | **WPF (.NET 10, C#, MVVM)** | Native Windows, beautiful, fast to build, already installed |
| Core Engine | **Go (Golang)** | Networking performance, single binary, cross-compile |
| Transport | **Cloudflare Tunnels** | Outbound-only, no open ports, invisible server |
| Auth | **Cloudflare Zero Trust** | Enterprise-grade access control |
| Secrets | **Bitwarden CLI** | Secure credential storage |
| Protocol | **WebSocket + MCP** | Real-time, low latency (20-50ms) |
| Encryption | **TLS 1.3** | End-to-end encrypted |
| VPS Server | **Go binary + Docker** | Lightweight, fast, containerized |

### Communication Between Layers

The WPF app launches and communicates with the Go core binary via **stdin/stdout JSON-RPC**. This gives us:
- Go's networking performance
- WPF's beautiful native UI
- Clean process isolation
- Single self-contained publish

---

## Project Structure

```
GhostLanForge/
├── README.md                          # Main documentation
├── prompt.md                          # THIS FILE - AI development prompt
├── LICENSE                            # MIT License
├── .gitignore
│
├── docs/                              # All design documents
│   ├── architecture.md                # Full architecture docs
│   ├── setup-guide.md                 # Step-by-step setup
│   ├── troubleshooting.md             # Common issues
│   ├── private-network-solution.md    # Private network technical details
│   ├── private-network-complete.md    # Complete usage examples
│   ├── vps-solutions.md              # Solution comparison matrix
│   └── websocket-proxy-plan.md       # Go implementation plan
│
├── src/
│   ├── ui/                            # WPF Application (C# / .NET 10)
│   │   ├── GhostLanForge.sln
│   │   ├── GhostLanForge.UI/
│   │   │   ├── GhostLanForge.UI.csproj
│   │   │   ├── App.xaml
│   │   │   ├── MainWindow.xaml
│   │   │   ├── Views/
│   │   │   │   ├── SetupWizard.xaml          # Interactive setup
│   │   │   │   ├── Dashboard.xaml            # Main dashboard
│   │   │   │   ├── ConnectionStatus.xaml     # Connection monitor
│   │   │   │   ├── ServiceBrowser.xaml       # Browse VPS services
│   │   │   │   ├── FileExplorer.xaml         # Bidirectional files
│   │   │   │   ├── Settings.xaml             # Configuration
│   │   │   │   └── Diagnostics.xaml          # System diagnostics
│   │   │   ├── ViewModels/
│   │   │   │   ├── SetupWizardViewModel.cs
│   │   │   │   ├── DashboardViewModel.cs
│   │   │   │   ├── ConnectionStatusViewModel.cs
│   │   │   │   ├── ServiceBrowserViewModel.cs
│   │   │   │   ├── FileExplorerViewModel.cs
│   │   │   │   ├── SettingsViewModel.cs
│   │   │   │   └── DiagnosticsViewModel.cs
│   │   │   ├── Models/
│   │   │   │   ├── ServiceInfo.cs
│   │   │   │   ├── ConnectionInfo.cs
│   │   │   │   ├── DiagnosticResult.cs
│   │   │   │   └── SetupConfig.cs
│   │   │   ├── Services/
│   │   │   │   ├── GoBridge.cs               # JSON-RPC to Go binary
│   │   │   │   ├── SystemChecker.cs          # Check system requirements
│   │   │   │   ├── AutoFixer.cs              # Auto-fix issues
│   │   │   │   └── TrayIconService.cs        # System tray
│   │   │   ├── Controls/
│   │   │   │   ├── StatusIndicator.xaml      # Green/yellow/red status
│   │   │   │   ├── ServiceCard.xaml          # Service tile card
│   │   │   │   └── LogViewer.xaml            # Real-time log viewer
│   │   │   ├── Themes/
│   │   │   │   ├── DarkTheme.xaml            # Dark mode
│   │   │   │   └── LightTheme.xaml           # Light mode
│   │   │   └── Resources/
│   │   │       ├── Icons/
│   │   │       └── Strings.xaml
│   │   └── GhostLanForge.UI.Tests/
│   │       └── GhostLanForge.UI.Tests.csproj
│   │
│   └── core/                          # Go Core Engine
│       ├── go.mod
│       ├── go.sum
│       ├── cmd/
│       │   ├── ghostcore/             # Main Go binary
│       │   │   └── main.go
│       │   └── ghostcore-server/      # VPS server binary
│       │       └── main.go
│       ├── internal/
│       │   ├── forge/                 # Core forge link engine
│       │   │   ├── forge.go
│       │   │   ├── tunnel.go
│       │   │   └── bridge.go
│       │   ├── tunnel/                # Cloudflare tunnel management
│       │   │   ├── manager.go
│       │   │   ├── config.go
│       │   │   └── health.go
│       │   ├── auth/                  # Authentication
│       │   │   ├── bitwarden.go
│       │   │   ├── zerotrust.go
│       │   │   └── jwt.go
│       │   ├── mcp/                   # MCP protocol
│       │   │   ├── client.go
│       │   │   ├── protocol.go
│       │   │   └── router.go
│       │   ├── ghostnet/              # Private network abstraction
│       │   │   ├── network.go
│       │   │   ├── dns.go
│       │   │   └── service.go
│       │   ├── setup/                 # Setup wizard backend
│       │   │   ├── checker.go         # System requirements check
│       │   │   ├── questions.go       # Setup questions logic
│       │   │   ├── validator.go       # Validate answers
│       │   │   └── configurer.go      # Apply configuration
│       │   ├── diag/                  # Diagnostics & auto-fix
│       │   │   ├── diagnostician.go
│       │   │   ├── fixer.go
│       │   │   ├── reporter.go        # Generate MD error reports
│       │   │   └── checks/
│       │   │       ├── network.go
│       │   │       ├── bitwarden.go
│       │   │       ├── cloudflare.go
│       │   │       ├── tunnel.go
│       │   │       └── vps.go
│       │   └── rpc/                   # JSON-RPC for WPF communication
│       │       ├── server.go
│       │       ├── handlers.go
│       │       └── types.go
│       ├── pkg/
│       │   ├── cloudflare/            # CF tunnel SDK
│       │   │   ├── client.go
│       │   │   └── tunnel.go
│       │   ├── bitwarden/             # Bitwarden CLI integration
│       │   │   ├── client.go
│       │   │   └── vault.go
│       │   └── vector/                # Vector DB client
│       │       ├── client.go
│       │       └── search.go
│       └── configs/
│           ├── server.yaml
│           └── client.yaml
│
├── scripts/
│   ├── build.ps1                      # Build everything
│   ├── install-server.sh              # VPS one-line install
│   ├── install-client.ps1             # Windows one-line install
│   └── dev-setup.ps1                  # Dev environment setup
│
├── deployments/
│   ├── docker/
│   │   ├── Dockerfile                 # VPS server Docker image
│   │   └── docker-compose.yml
│   └── systemd/
│       └── ghostlanforge.service      # VPS systemd service
│
└── assets/
    ├── icon.ico                       # App icon
    ├── icon.png                       # App icon PNG
    └── banner.png                     # GitHub social preview
```

---

## Phase 1: Interactive Setup Wizard (CRITICAL - Build First)

The setup wizard is the most important part. It must be built FIRST. Here is the exact behavior:

### Startup Sequence

1. User launches GhostLanForge (WPF app)
2. App immediately runs system checks BEFORE showing any UI
3. If all checks pass → show Setup Wizard questions
4. If any check fails → show beautiful error report in Markdown format

### System Requirements Check (runs automatically on startup)

The app checks these requirements IN ORDER:

| # | Check | What It Verifies | Auto-Fixable? |
|---|-------|-----------------|---------------|
| 1 | **OS Version** | Windows 10 1903+ or Windows 11 | No |
| 2 | **Administrator** | Running as admin | Yes (re-launch as admin) |
| 3 | **Internet** | Active internet connection | No |
| 4 | **.NET Runtime** | .NET 10 installed | Yes (download + install) |
| 5 | **Go Runtime** | Go 1.21+ installed | Yes (download + install) |
| 6 | **Cloudflared** | cloudflared CLI installed | Yes (download + install) |
| 7 | **Bitwarden CLI** | bw CLI installed and logged in | Yes (download + install) |
| 8 | **Cloudflare Account** | CF account with Zero Trust enabled | No (manual setup) |
| 9 | **SSH Key** | SSH key pair exists | Yes (generate) |
| 10 | **VPS Reachable** | VPS responds to SSH | No (manual - no open ports expected) |

### Error Report Format (when checks fail)

When a check fails, the app shows a beautiful Markdown-formatted error report:

```markdown
# ❌ GhostLanForge Setup - Issues Found

## Check Results

| # | Check | Status | Details |
|---|-------|--------|---------|
| 1 | OS Version | ✅ Pass | Windows 11 24H2 |
| 2 | Administrator | ✅ Pass | Running as admin |
| 3 | Internet | ✅ Pass | Connected (ping: 12ms) |
| 4 | .NET Runtime | ✅ Pass | .NET 10.0.300 |
| 5 | Go Runtime | ❌ FAIL | Not installed |
| 6 | Cloudflared | ❌ FAIL | Not installed |
| 7 | Bitwarden CLI | ✅ Pass | v2024.1.0 (logged in) |

---

## Issues Found: 2

### Issue #1: Go Runtime Not Installed

**What**: Go (Golang) is required for the GhostLanForge core engine.
**Why**: The forge networking engine is written in Go for maximum performance.
**Impact**: Without Go, the core engine cannot compile or run.

**How to fix manually:**
1. Download Go 1.21+ from https://go.dev/dl/
2. Run the installer
3. Restart GhostLanForge

**Or let GhostLanForge fix it automatically:**
→ [Fix Automatically] button

---

### Issue #2: Cloudflared Not Installed

**What**: cloudflared is the Cloudflare tunnel client.
**Why**: GhostLanForge uses cloudflared to create invisible tunnels.
**Impact**: Without cloudflared, no private network can be established.

**How to fix manually:**
1. Download from https://github.com/cloudflare/cloudflared/releases
2. Install to C:\Program Files\cloudflared\
3. Add to system PATH
4. Restart GhostLanForge

**Or let GhostLanForge fix it automatically:**
→ [Fix Automatically] button
```

### Auto-Fix Behavior

When user clicks "Fix Automatically":

1. App downloads the required tool
2. Installs it silently
3. Verifies installation
4. Re-runs the check
5. Updates the status (pass/fail)
6. If still failing, shows updated error report with new details

### Setup Wizard Questions (after all checks pass)

The wizard asks these questions IN ORDER:

```
Step 1/7: Cloudflare Account
┌──────────────────────────────────────────┐
│                                          │
│  🔗 Cloudflare Zero Trust               │
│                                          │
│  Do you have a Cloudflare account with   │
│  Zero Trust enabled?                     │
│                                          │
│  [Yes, I have an account]                │
│  [No, help me set one up]               │
│  [Skip - configure later]               │
│                                          │
└──────────────────────────────────────────┘

Step 2/7: VPS Connection
┌──────────────────────────────────────────┐
│                                          │
│  🖥️ Your VPS                            │
│                                          │
│  VPS IP Address: [________________]      │
│  SSH Username:    [________________]      │
│  SSH Port:        [22___________]        │
│                                          │
│  [Test Connection]                        │
│                                          │
│  Status: ⏳ Not tested yet               │
│                                          │
│  [Back]                    [Next →]      │
│                                          │
└──────────────────────────────────────────┘

Step 3/7: Bitwarden Authentication
┌──────────────────────────────────────────┐
│                                          │
│  🔐 Bitwarden Integration               │
│                                          │
│  Bitwarden Server: [bitwarden.com___]    │
│  Email:           [________________]     │
│  Master Password: [****************]     │
│                                          │
│  [Test Authentication]                    │
│                                          │
│  Status: ⏳ Not tested yet               │
│                                          │
│  [Back]                    [Next →]      │
│                                          │
└──────────────────────────────────────────┘

Step 4/7: Tunnel Configuration
┌──────────────────────────────────────────┐
│                                          │
│  🚇 Cloudflare Tunnel                   │
│                                          │
│  Tunnel Name: [ghostlanforge______]      │
│  Domain:      [yourdomain.com____]       │
│                                          │
│  ○ Create new tunnel                     │
│  ○ Use existing tunnel                   │
│    Tunnel ID: [________________]         │
│                                          │
│  [Back]                    [Next →]      │
│                                          │
└──────────────────────────────────────────┘

Step 5/7: VPS Services
┌──────────────────────────────────────────┐
│                                          │
│  🐳 Services to Expose                  │
│                                          │
│  ☑ Vector Database (port 3000)           │
│  ☑ MCP Server (port 8765)               │
│  ☑ Docker API (port 2375)               │
│  ☑ Grafana (port 3001)                  │
│  ☐ Custom Service [port____]             │
│                                          │
│  [Add Custom Service]                     │
│                                          │
│  [Back]                    [Next →]      │
│                                          │
└──────────────────────────────────────────┘

Step 6/7: Windows Resources
┌──────────────────────────────────────────┐
│                                          │
│  🖥️ Windows Resources to Share          │
│                                          │
│  ☑ GPU Server (port 3002)                │
│  ☑ File Server (port 3003)              │
│  ☑ AI Models (port 3004)                │
│  ☐ Custom Resource [port____]            │
│                                          │
│  GPU detected: NVIDIA RTX 4090 ✅        │
│  Shared folder: C:\GhostLanForge\shared  │
│                                          │
│  [Back]                    [Next →]      │
│                                          │
└──────────────────────────────────────────┘

Step 7/7: Review & Forge
┌──────────────────────────────────────────┐
│                                          │
│  🔨 Ready to Forge                      │
│                                          │
│  Cloudflare: ✅ Configured               │
│  VPS:        ✅ Reachable                │
│  Bitwarden:  ✅ Authenticated            │
│  Tunnel:     ✅ Created                  │
│  Services:   4 VPS + 3 Windows           │
│                                          │
│  ⚠️ The VPS server component must be     │
│  installed. Generate install command?    │
│                                          │
│  [Generate Server Install Command]        │
│  [Back]              [🔥 FORGE LINK]     │
│                                          │
└──────────────────────────────────────────┘
```

---

## Phase 2: Dashboard (Build Second)

After setup, the main window shows a dashboard:

```
┌─────────────────────────────────────────────────────────────────┐
│ 🔨 GhostLanForge                              [_] [□] [X]    │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐            │
│  │  🔗 Forge   │  │  🖥️ VPS    │  │  🖥️ You    │            │
│  │  Link       │  │  Resources  │  │  Resources  │            │
│  │  ACTIVE ✅  │  │  4 services │  │  3 services │            │
│  │  12ms ping  │  │  Online     │  │  Online     │            │
│  └─────────────┘  └─────────────┘  └─────────────┘            │
│                                                                 │
│  📊 Performance                                                 │
│  ┌───────────────────────────────────────────────────┐          │
│  │  Latency: 12ms ████████░░░░░░ (20-50ms target)   │          │
│  │  Throughput: 847 Mbps ████████████░░ (High)       │          │
│  │  Uptime: 4h 23m                                   │          │
│  └───────────────────────────────────────────────────┘          │
│                                                                 │
│  🔧 VPS Services                                                │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────┐            │
│  │ Vector DB    │ │ MCP Server   │ │ Docker API   │            │
│  │ :3000  ✅    │ │ :8765  ✅    │ │ :2375  ✅    │            │
│  │ vps.local    │ │ vps.local    │ │ vps.local    │            │
│  └──────────────┘ └──────────────┘ └──────────────┘            │
│                                                                 │
│  📟 Logs                                        [Clear] [Export]│
│  ┌───────────────────────────────────────────────────┐          │
│  │ 10:23:45 INFO  Forge link established             │          │
│  │ 10:23:46 INFO  Tunnel vps.local → :8080 active    │          │
│  │ 10:23:46 INFO  Tunnel vector-db.local → :3000     │          │
│  │ 10:23:47 INFO  Tunnel mcp-server.local → :8765    │          │
│  │ 10:23:47 INFO  Bitwarden auth verified            │          │
│  │ 10:23:48 INFO  All 7 tunnels active               │          │
│  │ 10:24:01 INFO  Windows GPU shared on :3002        │          │
│  └───────────────────────────────────────────────────┘          │
│                                                                 │
│  [⚙ Settings]  [🔧 Diagnostics]  [📖 Help]                     │
└─────────────────────────────────────────────────────────────────┘
```

---

## Phase 3: VPS Server (Build Third)

A Go binary that runs on the VPS:

- Accepts tunnel connections from Windows
- Exposes configured services via the private network
- Runs as systemd service or Docker container
- One-line install: `curl -sSL ... | bash`

---

## Build Instructions

```bash
# Build Go core
cd src/core
go build -o ../../build/ghostcore ./cmd/ghostcore
go build -o ../../build/ghostcore-server ./cmd/ghostcore-server

# Build WPF app (embeds Go binary)
cd src/ui
dotnet publish GhostLanForge.UI -c Release -r win-x64 --self-contained

# Output: build/GhostLanForge.exe (single file, ~50MB)
```

---

## Development Priority

1. **FIRST**: Setup wizard with system checks, error reporting, and auto-fix
2. **SECOND**: Go core engine with JSON-RPC bridge
3. **THIRD**: Dashboard UI with real-time status
4. **FOURTH**: VPS server binary + install script
5. **FIFTH**: Service browser, file explorer, diagnostics

---

## Important Notes for the AI Agent

- Read ALL `.md` files in the root and `docs/` folder before writing code
- Follow the project structure EXACTLY as defined above
- The WPF app MUST embed the Go binary and communicate via JSON-RPC
- Error reports MUST be in beautiful Markdown format
- The setup wizard MUST check system requirements BEFORE asking questions
- Auto-fix MUST ask user permission before downloading/installing anything
- All UI text MUST be user-friendly, no technical jargon for normal users
- Dark mode MUST be the default theme
- The app MUST minimize to system tray and show forge link status
- The VPS server MUST have ZERO open ports at all times
