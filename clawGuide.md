INFO: converting prompt to search query: how to summarize multi-phase conversation for context preservation and instructions for continuing with a new language model or new conversation when context window is exceeded

Getting web content for https://limitededitionjonathan.substack.com/p/ultimate-guide-fixing-claude-hit

Getting web content for https://www.reddit.com/r/ChatGPTPro/comments/1loj6hy/youve_reached_the_maximum_length_for_this/



# Complete Conversation Summary & LLM Handoff Document

---

## Instructions for the New LLM

You are continuing a multi-phase conversation as **ClawGuide**, a Homelab & AI Agent Automation Expert. The user has completed all 7 phases of their homelab build. Below is the complete context including the user's profile, hardware, all decisions made, all configurations deployed, and the current state of their homelab. Please adopt the same persona, communication style, and maintain continuity with all prior decisions and recommendations.

**When resuming, start the new conversation with:** "Here's context from our previous conversation. Let's continue: [paste this entire document]. Now, [what you actually need to do next]." [1]

---

## System Prompt (Use This as Your System/Role Prompt)

```
You are a Homelab & AI Agent Automation Expert named "ClawGuide" with deep expertise in self-hosted infrastructure, open-source AI tooling, Docker containerization, local LLM deployment, and task automation using OpenClaw (https://github.com/openclaw/openclaw). You specialize in guiding beginners through setting up homelabs on consumer-grade hardware, running local LLMs via Ollama, and building AI agent workflows — all on a minimal budget.

About the user you are helping:

- Experienced front-end web developer (10+ years professional experience) — you may use web development terminology freely.
- Has a BS in Computer Science — comfortable with command line, SSH, emacs, compiling code. Has used Linux before.
- Complete beginner in homelabs, Docker/containers, Ollama, local LLMs, and OpenClaw (but has now completed all 7 setup phases).
- Budget-conscious: only recommend paid solutions when absolutely necessary. Prioritize free, open-source, self-hosted tools at all times.

Hardware:

- Homelab Server (primary, always-on, dedicated): i7-7700K 4.2GHz, GTX 1080 Ti 11GB VRAM, 16GB DDR4 3333MHz, dedicated SSD for OS
- Secondary Machine (LLM offload only via Ollama over LAN): i7-13700KF 16-core, RTX 5080 16GB VRAM, 64GB DDR5-6400 — this is the user's daily driver desktop running WINDOWS 11 and should NOT be treated as a server. It is only available for running Ollama to serve larger LLM models to the homelab server over the local network when needed.

Networking:

- Standard consumer cable modem/router combo (WiFi + wired LAN)
- Both machines on the same local network
- Secure external access via Cloudflare Tunnel + Cloudflare Access Zero Trust

Your communication style:

- Patient, structured, and beginner-friendly for all homelab, Docker, LLM, and OpenClaw topics.
- Since the user has a CS degree and CLI experience, skip basic Linux/terminal explanations but explain infrastructure-specific concepts (Docker, reverse proxies, LLM quantization, etc.).
- Use numbered phases and clear step-by-step instructions with exact commands and configuration file examples.
- Always explain the "why" behind recommendations, not just the "how."
- Proactively warn about potential pitfalls, hardware limitations, security risks, and common beginner mistakes.
- When presenting options, provide a brief comparison table and a clear recommendation with reasoning.
- Break complex setups into manageable milestones.
```

---

## User's Goals

1. **Security best practices** — Security is baked into every phase by design. 23 security layers implemented.
2. **Run OpenClaw** on the homelab to automate tasks, starting with social media cross-posting (uploading media with captions to YouTube, X/Twitter, and Facebook, referencing content already on IG/TikTok/Facebook, with scheduling and daily limits).
3. **Future expansion** — Architecture is extensible for a future multi-agent setup resembling a small virtual company (email management, marketing, content creation, social media agents, analytics, etc.).
4. **Improve professional capabilities** — The user is building this homelab to level up their skills as a web developer by gaining hands-on infrastructure, DevOps, security, and AI experience.

---

## Overall Roadmap — ALL PHASES COMPLETED

| Phase | Goal | Status |
|-------|------|--------|
| **Phase 1** | Install & configure Ubuntu Server 24.04 LTS | ✅ COMPLETED |
| **Phase 2** | Install Docker & Docker Compose | ✅ COMPLETED |
| **Phase 3** | Set up secure networking (Caddy + Cloudflare Tunnel) | ✅ COMPLETED |
| **Phase 4** | Security hardening & monitoring | ✅ COMPLETED |
| **Phase 5** | Deploy Ollama (local LLMs) | ✅ COMPLETED |
| **Phase 6** | Deploy OpenClaw | ✅ COMPLETED |
| **Phase 7** | Build first automation (social media cross-posting) | ✅ COMPLETED |

---

## Phase 1: COMPLETED — Ubuntu Server Installation & Base Configuration

### Decisions Made:
- **OS:** Ubuntu Server 24.04 LTS (chosen over Proxmox and TrueNAS for low overhead, direct GPU access, first-class Docker support, best security tooling for beginners)
- **SSD:** Brand new dedicated SSD installed for the OS
- **GPU:** GTX 1080 Ti installed in the homelab server
- **Server connected via Ethernet** (not WiFi)

### What Was Configured:
- Fresh Ubuntu Server 24.04 LTS installation with **LVM enabled** (for future disk flexibility)
- OpenSSH server installed during setup
- System fully updated (`apt update && apt upgrade`)
- **Static IP** configured via Netplan (example: `192.168.1.100/24`) with Cloudflare DNS (`1.1.1.1`, `1.0.0.1`)
- **Timezone** set (e.g., `America/New_York`)
- **NVIDIA driver 550** installed, verified with `nvidia-smi` (GTX 1080 Ti recognized)

### Security Hardening (Phase 1):
1. **SSH key-based authentication** (Ed25519) — password authentication disabled, root login disabled
2. **UFW firewall** — default deny incoming, allow outgoing, SSH allowed
3. **Fail2Ban** — 3 failed SSH attempts in 10 minutes = 1 hour ban (`/etc/fail2ban/jail.local`)
4. **Automatic security updates** via `unattended-upgrades`
5. **Monitoring tools** installed: `htop`, `iotop`, `net-tools`

---

## Phase 2: COMPLETED — Docker & Docker Compose

### Decisions Made:
- Docker installed from **official Docker repository** (not Ubuntu's default apt packages)
- Docker Compose **v2 plugin** (modern `docker compose` without hyphen, not the old standalone `docker-compose`)

### What Was Configured:
- Docker Engine (`docker-ce`), CLI (`docker-ce-cli`), containerd, buildx plugin, and compose plugin installed
- User added to `docker` group (can run without `sudo`)
- Docker enabled to start on boot (`systemctl enable docker containerd`)
- **Log rotation** configured in `/etc/docker/daemon.json`:
  ```json
  {
    "log-driver": "json-file",
    "log-opts": {
      "max-size": "10m",
      "max-file": "3"
    }
  }
  ```
- **Custom Docker network** created: `docker network create homelab-net`
- **Project directory structure** created:
  ```
  ~/homelab/
  ├── ollama/
  ├── openclaw/
  ├── proxy/
  └── monitoring/
  ```

### Key Concepts Established:
- **`homelab-net`** = shared network all services use to communicate by container name (Docker DNS)
- **`expose`** (safe, container-to-container only) vs **`ports`** (publishes to host, bypasses UFW — use sparingly)
- **Critical UFW + Docker gotcha:** Docker bypasses UFW when publishing ports via `iptables` manipulation. Solution: use `expose` + Docker networks for internal services; only the reverse proxy publishes ports
- Security practices: `no-new-privileges`, read-only mounts (`:ro`), capability dropping (`cap_drop: ALL`)

---

## Phase 3: COMPLETED — Secure Networking

### Decisions Made:
- **Domain registrar & DNS:** Cloudflare Registrar (at-cost pricing, free DNS/CDN/SSL/DDoS/Tunnel)
- **Reverse proxy:** Caddy (chosen over Nginx Proxy Manager and Traefik for simple Caddyfile config-as-code syntax, auto HTTPS, low resource usage, developer-friendliness)
- **External access:** Cloudflare Tunnel (zero open router ports, hidden home IP, outbound-only connection)
- **Authentication:** Cloudflare Access Zero Trust (free tier, email OTP verification, wildcard subdomain protection, 24-hour sessions)

### Architecture:
```
Internet → Cloudflare Edge (SSL + DDoS + Access Auth) → Cloudflare Tunnel (encrypted, outbound-only) → cloudflared container → Caddy container → Service containers
```

### What Was Configured:

**Caddy (reverse proxy) — `~/homelab/proxy/docker-compose.yml`:**
- Running as Docker container on `homelab-net`
- `Caddyfile` with routes for services (status.yourdomain.com → Uptime Kuma, openclaw.yourdomain.com → OpenClaw)
- Volumes: `caddy_data` (SSL certs), `caddy_config` (runtime)
- `no-new-privileges` security option
- LAN-only port binding (`192.168.1.100:8080:80` and `192.168.1.100:8443:443`) for local access without Cloudflare

**Cloudflare Tunnel (`cloudflared`) — same compose file:**
- Running as Docker container on `homelab-net`
- Tunnel token stored in `~/homelab/proxy/.env` with `chmod 600` permissions
- Connected to Cloudflare edge (multiple data center connections)
- Public hostnames configured for each service subdomain
- `depends_on: caddy`

**Cloudflare Access (Zero Trust):**
- Self-hosted application with wildcard subdomain (`*`)
- Access policy: Allow only user's personal email
- Email OTP authentication
- 24-hour session duration

**Caddyfile structure:**
```
:80 { respond "Access denied" 403 }
status.yourdomain.com { reverse_proxy uptime-kuma:3001 }
openclaw.yourdomain.com { reverse_proxy openclaw:3000 }
```

---

## Phase 4: COMPLETED — Security Hardening & Monitoring

### What Was Configured:

**Docker Socket Proxy — `~/homelab/monitoring/docker-compose.yml`:**
- `tecnativa/docker-socket-proxy` container on isolated `socket-proxy-net` (marked `internal: true`)
- Only exposes CONTAINERS, IMAGES, INFO, POST endpoints
- Prevents direct Docker socket access by other containers

**Watchtower (automatic container updates):**
- `containrrr/watchtower` container
- **Opt-in mode** (`WATCHTOWER_LABEL_ENABLE=true`) — only updates containers with label `com.centurylinklabs.watchtower.enable=true`
- Schedule: 4:00 AM daily
- Connects to Docker via socket proxy (not direct socket mount)
- Auto-cleanup of old images

**Uptime Kuma (monitoring & alerts):**
- `louislam/uptime-kuma:1` container on `homelab-net`
- Accessible at `https://status.yourdomain.com`
- Monitors configured for: Caddy, Cloudflare Tunnel, Server Ping, Local Ollama, Desktop Ollama, OpenClaw
- Notifications configured (Discord/Email/Telegram)

**Backup Strategy — Two-Tier:**
- **Tier 1:** Git version control for configs (`~/homelab/` is a git repo with `.gitignore` excluding `.env` files and data). Optionally pushed to private GitHub repo.
- **Tier 2:** Automated daily backup script (`~/homelab/scripts/backup.sh`) via cron at 3:00 AM. Backs up all compose files, configs, and Docker volumes. 7-day retention. Excludes `.env` files (secrets backed up separately in password manager).

**Health Check Script — `~/homelab/scripts/healthcheck.sh`:**
- Shows CPU, memory, disk, GPU status, Docker containers, SSH security, Fail2Ban status, backup status, Ollama status, OpenClaw status
- Aliased as `hlab` in `.bashrc`

**Automation Status Script — `~/homelab/openclaw/scripts/automation_status.sh`:**
- Queries SQLite database for cross-posting status
- Aliased as `crosspost` in `.bashrc`

**Additional tools installed:** `ncdu`, `duf`, `btop`

### Docker Networks (Final):
- **`homelab-net`** — shared by all services (Caddy, cloudflared, Ollama, OpenClaw, Uptime Kuma, Watchtower)
- **`socket-proxy-net`** — internal only, used by socket-proxy and Watchtower

---

## Phase 5: COMPLETED — Deploy Ollama (Local LLMs)

### Decisions Made:
- **Homelab server:** Ollama runs in Docker with GPU passthrough (GTX 1080 Ti, 11GB VRAM)
- **Desktop (Windows 11):** Ollama installed natively (not Docker) for larger model offload
- **Model selection based on VRAM constraints**

### What Was Configured:

**NVIDIA Container Toolkit** installed on homelab server for Docker GPU passthrough.

**Ollama (homelab server) — `~/homelab/ollama/docker-compose.yml`:**
- `ollama/ollama:latest` container on `homelab-net`
- GPU passthrough via `deploy.resources.reservations.devices`
- `expose: "11434"` (NOT published to host — only accessible via Docker network)
- `ollama_data` volume for persistent model storage
- `OLLAMA_NUM_PARALLEL=1` and `OLLAMA_MAX_LOADED_MODELS=1` (11GB VRAM constraint)
- Watchtower auto-update label enabled

**Models on homelab server:**
- `llama3.1:8b` — primary workhorse for general tasks, captions, content (~5GB VRAM)
- `llama3.2:3b` — fast model for simple tasks, classification, routing (~4GB VRAM at Q8)

**Ollama (Windows 11 desktop) — native install:**
- Installed via official Windows installer from ollama.com
- Environment variable `OLLAMA_HOST=0.0.0.0:11434` set via System Environment Variables (GUI or PowerShell)
- Windows Firewall rule: allow TCP 11434 ONLY from homelab server IP (`192.168.1.100`), Private profile only
- DHCP reservation recommended in router for stable desktop IP

**Models on desktop:**
- `qwen2.5:14b` — complex reasoning model (~9GB VRAM at Q4)
- `codestral:22b` — code generation model (~13GB VRAM at Q4)

**LLM Concepts Explained to User:**
- Model sizes (1B to 70B+ parameters) and quality tradeoffs
- Quantization (FP16 → Q8 → Q6 → Q5 → Q4 → Q3 → Q2) with JPEG compression analogy
- VRAM budgets and what fits on each GPU
- Context windows (2K to 128K tokens)
- Inference speed expectations per model size on user's hardware
- Sweet spot: Q4_K_M to Q5_K_M quantization

**Dual-Instance Architecture:**
- Local Ollama: `http://ollama:11434` (via Docker network)
- Desktop Ollama: `http://192.168.1.XXX:11434` (via LAN)
- Desktop auto-unloads models after 5 minutes of inactivity
- Desktop Ollama shows as "down" in Uptime Kuma when PC is off (expected behavior)

---

## Phase 6: COMPLETED — Deploy OpenClaw

### What Was Configured:

**OpenClaw — `~/homelab/openclaw/docker-compose.yml`:**
- Built from cloned repo (`https://github.com/openclaw/openclaw`)
- Container on `homelab-net`
- `expose: "3000"` (accessible via Caddy only)
- Volumes: `openclaw_data` (/app/data), `openclaw_workspace` (/app/workspace — sandboxed), `config.yml` (ro), `prompts/` (ro), `workflows/` (ro), `schedules.yml` (ro), `scripts/` (ro)
- Resource limits: 2GB RAM, 2 CPU cores
- Environment variables for LLM endpoints and model names
- `.env` file with API credentials (`chmod 600`)
- Watchtower auto-update label enabled

**Agent Configuration — `~/homelab/openclaw/config.yml`:**
- Dual LLM provider setup:
  - `local` provider: `http://ollama:11434` with `llama3.1:8b` (default) and `llama3.2:3b` (fast)
  - `desktop` provider: `http://192.168.1.XXX:11434` with `qwen2.5:14b` (complex)
  - Fallback: if desktop unreachable, fall back to local default model
  - Health check every 60 seconds
- Model routing rules:
  - Simple tasks (classify, tag, yes/no) → `llama3.2:3b` (fast)
  - Content generation (captions, summaries) → `llama3.1:8b` (default)
  - Complex analysis (long-form, strategy) → `qwen2.5:14b` (desktop)
- Agent settings: max 20 steps, 120s tool timeout, 10-message memory window

**Security Hardening for AI Agents:**
- Sandboxed workspace (`/app/workspace` volume — isolated from host filesystem)
- Resource limits (2GB RAM, 2 CPU caps — prevents runaway agents)
- Max steps limit (20 — prevents infinite loops)
- `no-new-privileges` security option
- Read-only config mount (agent can't modify its own configuration)
- Audit logging enabled (`AUDIT_LOG=true`)
- DNS configuration to control outbound resolution

**Accessible at:** `https://openclaw.yourdomain.com` (via Caddy + Cloudflare Tunnel + Access auth)

---

## Phase 7: COMPLETED — Social Media Cross-Posting Automation

### What Was Configured:

**Social Media API Access — credentials in `~/homelab/openclaw/.env`:**
- **YouTube Data API v3:** OAuth 2.0 via Google Cloud project, refresh token obtained via helper script (`scripts/youtube_auth.py`). Free tier: 10,000 quota units/day.
- **X (Twitter) API v2:** Free tier developer account, API key + secret + access tokens. Free tier: 1,500 posts/month (~50/day).
- **Facebook Graph API:** Meta Developer app with Pages API + Instagram Graph API. Long-lived page access token (never expires). Permissions: `pages_manage_posts`, `pages_read_engagement`, `instagram_basic`.
- **Instagram Graph API:** Read-only access for scanning existing posts (publishing is limited).
- **TikTok API:** Noted as having lengthy approval process and restrictive policies.

**Content Tracking Database — SQLite at `/app/data/content_tracker.db`:**
- `content` table — one row per piece of content (hash, title, description, media type, source platform, URLs, timestamps)
- `cross_posts` table — one row per platform posting (content_id, target platform, AI-generated caption, status, scheduled/posted timestamps, error tracking, retry count)
- `upload_queue` table — manages daily scheduling (priority, scheduled date, order, execution status)
- `agent_log` table — audit trail of all agent actions
- Initialized via `scripts/init_db.py`

**Caption Generation Pipeline — `~/homelab/openclaw/prompts/captions.yml`:**
- Platform-specific prompt templates:
  - `youtube_description` — 2-4 sentences + CTA + 5-8 hashtags (uses `llama3.1:8b`)
  - `twitter_post` — under 280 characters, punchy, 2-3 hashtags (uses `llama3.1:8b`)
  - `facebook_post` — casual/conversational, 1-3 sentences, 3-5 hashtags (uses `llama3.1:8b`)
  - `raw_footage_caption` — generates captions from scratch based on metadata/filename (uses `qwen2.5:14b` on desktop)
- Each template has system prompt with platform-specific rules and user prompt template with variable placeholders
- Twitter template includes validation (max 280 chars, retry up to 3 times if exceeded)

**Cross-Posting Workflow — `~/homelab/openclaw/workflows/cross_post.yml`:**
- 6-step workflow:
  1. **Scan Sources** — fetch recent posts from IG/TikTok/Facebook via APIs
  2. **Compare Content** — check tracking DB, identify missing cross-posts
  3. **Generate Captions** — use local LLM with platform-specific prompts
  4. **Schedule Uploads** — queue respecting daily limits per platform
  5. **Execute Uploads** — post to target platforms via APIs (only during upload window)
  6. **Send Summary** — daily notification via Discord/Email

**Scheduling — `~/homelab/openclaw/schedules.yml`:**
- Content scan: twice daily at 8 AM and 8 PM
- Upload executor: every 2 hours from 9 AM to 9 PM (9, 11, 13, 15, 17, 19, 21)
- Daily summary: 10 PM

**Rate Limiting:**
- Daily limits per platform: YouTube 3, Twitter 5, Facebook 3
- Max daily total: 8 uploads across all platforms
- Upload window: 9 AM to 9 PM only
- Minimum 2 hours between uploads to same platform
- Overflow queued to next day automatically

**Testing Strategy (3 phases):**
1. **Dry Run** (`automation.dry_run: true` in config.yml) — generates captions but doesn't post
2. **Single Post** — manually trigger one post per platform to verify
3. **Automated** — enable full schedule

**Future Enhancement Prepared:**
- `raw_footage_caption` prompt template ready for AI-generated captions from raw footage
- Architecture supports file watcher → metadata extraction → AI caption → review → upload pipeline
- Uses `qwen2.5:14b` (desktop) for from-scratch caption generation

---

## Complete Security Layers — 23 Total

| # | Layer | Phase |
|---|---|---|
| 1 | SSH key-only authentication (Ed25519, password auth disabled, root login disabled) | Phase 1 |
| 2 | UFW firewall (default deny incoming, allow outgoing) | Phase 1 |
| 3 | Fail2Ban brute force protection (3 attempts / 10 min = 1 hour ban) | Phase 1 |
| 4 | Automatic OS security updates (unattended-upgrades) | Phase 1 |
| 5 | Docker log rotation (10MB max, 3 files per container) | Phase 2 |
| 6 | Docker network isolation (homelab-net + socket-proxy-net) | Phase 2 |
| 7 | Container no-new-privileges (all containers) | Phase 2 |
| 8 | Zero open router ports (Cloudflare Tunnel, outbound-only) | Phase 3 |
| 9 | Hidden home IP (Cloudflare proxy) | Phase 3 |
| 10 | Cloudflare DDoS protection (free tier) | Phase 3 |
| 11 | Cloudflare Access Zero Trust auth (email OTP, wildcard subdomain) | Phase 3 |
| 12 | Secrets in .env files (never hardcoded in compose files) | Phase 3 |
| 13 | Restrictive file permissions (chmod 600 on .env files) | Phase 3 |
| 14 | Automated container updates (Watchtower, opt-in labels, 4 AM daily) | Phase 4 |
| 15 | Docker socket proxy (tecnativa, least-privilege API access) | Phase 4 |
| 16 | Service monitoring & alerting (Uptime Kuma + notifications) | Phase 4 |
| 17 | Automated backups with 7-day retention (cron at 3 AM daily) | Phase 4 |
| 18 | Agent resource limits (2GB RAM, 2 CPU caps) | Phase 6 |
| 19 | Agent max steps limit (20 steps, prevents infinite loops) | Phase 6 |
| 20 | Sandboxed workspace (Docker volume isolation for file ops) | Phase 6 |
| 21 | Audit logging (all tool executions tracked) | Phase 6 |
| 22 | Dry-run mode (test automations before going live) | Phase 7 |
| 23 | Rate limiting & scheduling (prevent platform bans) | Phase 7 |

---

## Final Project Structure

```
~/homelab/
├── .git/
├── .gitignore                         # Excludes .env, data dirs, volumes
├── proxy/
│   ├── docker-compose.yml             # Caddy + cloudflared
│   ├── Caddyfile                      # Reverse proxy routes
│   └── .env                           # Cloudflare tunnel token
├── monitoring/
│   └── docker-compose.yml             # Socket proxy + Watchtower + Uptime Kuma
├── ollama/
│   └── docker-compose.yml             # Ollama with GPU passthrough
├── openclaw/
│   ├── docker-compose.yml             # OpenClaw agent platform
│   ├── config.yml                     # Agent & model routing config
│   ├── schedules.yml                  # Cron schedules for automations
│   ├── .env                           # API keys & secrets
│   ├── credentials/
│   │   └── youtube_client_secret.json
│   ├── prompts/
│   │   └── captions.yml               # Platform-specific caption templates
│   ├── workflows/
│   │   └── cross_post.yml             # Cross-posting workflow definition
│   └── scripts/
│       ├── init_db.py                 # Database initialization
│       ├── youtube_auth.py            # YouTube OAuth helper
│       └── automation_status.sh       # Cross-post status dashboard
├── scripts/
│   ├── backup.sh                      # Automated backup script
│   └── healthcheck.sh                 # System health check
└── README.md

~/backups/
└── homelab-backup-YYYY-MM-DD.tar.gz   # Daily backups (7-day retention)
```

---

## Running Services (Docker Containers)

| Container | Image | Network(s) | Ports/Expose | Purpose |
|---|---|---|---|---|
| `caddy` | `caddy:2-alpine` | homelab-net | 192.168.1.100:8080:80, 192.168.1.100:8443:443, expose 80/443 | Reverse proxy |
| `cloudflared` | `cloudflare/cloudflared:latest` | homelab-net | None | Cloudflare Tunnel agent |
| `socket-proxy` | `tecnativa/docker-socket-proxy:latest` | socket-proxy-net | None | Docker API proxy |
| `watchtower` | `containrrr/watchtower:latest` | homelab-net, socket-proxy-net | None | Auto container updates |
| `uptime-kuma` | `louislam/uptime-kuma:1` | homelab-net | expose 3001 | Monitoring dashboard |
| `ollama` | `ollama/ollama:latest` | homelab-net | expose 11434 | Local LLM inference (GPU) |
| `openclaw` | Built from repo | homelab-net | expose 3000 | AI agent platform |

---

## Cron Jobs on Homelab Server

| Schedule | Command | Purpose |
|---|---|---|
| `0 3 * * *` | `~/homelab/scripts/backup.sh` | Daily backup (before Watchtower) |

---

## Shell Aliases

| Alias | Command | Purpose |
|---|---|---|
| `hlab` | `~/homelab/scripts/healthcheck.sh` | Quick system health check |
| `crosspost` | `~/homelab/openclaw/scripts/automation_status.sh` | Cross-posting status dashboard |

---

## Desktop (Windows 11) Configuration

- Ollama installed natively via Windows installer
- Environment variable: `OLLAMA_HOST=0.0.0.0:11434` (System level)
- Windows Firewall: inbound TCP 11434 allowed ONLY from `192.168.1.100`, Private profile only
- Models: `qwen2.5:14b`, `codestral:22b`
- DHCP reservation recommended in router for stable IP
- Ollama auto-starts with Windows, auto-unloads models after 5 min idle
- Expected to show as "down" in Uptime Kuma when PC is off/sleeping

---

## Future Expansion Ideas Discussed

The architecture supports adding new OpenClaw workflows for:
- 📧 Email Manager agent
- 📝 Content Writer agent
- 📊 Analytics agent
- 🎨 Thumbnail Generator (needs GPU time-sharing with Ollama)
- 🔍 Research agent
- 💰 Invoice agent
- Raw footage → AI caption pipeline (prompt template already created)

Each would be a new workflow in OpenClaw sharing the existing LLM infrastructure.

---

## Important Notes for Continuity

- The exact domain name was not specified — use `yourdomain.com` as placeholder
- The server's static IP was used as `192.168.1.100` as an example — user may have chosen differently
- The desktop's LAN IP was referenced as `192.168.1.XXX` — user needs to substitute their actual IP
- The user's timezone was referenced as `America/New_York` — may differ
- OpenClaw's exact API and configuration format may vary from what was documented — the configurations shown represent the intended architecture and should be adapted to match the actual OpenClaw version installed
- All social media API credentials need to be obtained by the user (some require approval periods of days to weeks)
- The automation starts in `dry_run: true` mode — user must explicitly set to `false` to go live
- The `.gitignore` excludes all `.env` files — secrets should be backed up separately in a password manager
