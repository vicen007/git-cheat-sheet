# Homelab Project Plan: `uranus` Server

This document outlines the complete plan for setting up the `uranus` homelab server. The project is divided into three phases, starting with a secure foundation and progressively adding functionality.

-   **Hostname:** `uranus`
-   **OS:** Debian 12 (Bookworm)
-   **Primary Goal:** Run the OpenClaw AI agent to monitor Amazon product prices using a locally hosted Large Language Model (LLM).

---

## Phase 1: Foundation (Current Focus)

This phase focuses on building a secure, stable, and functional server ready for application deployment. All tasks are performed on the LAN only, with no exposure to the public internet.

| Step | Task                                                               | Description                                                                                                                                                                                                                                                                                       |
| :--- | :----------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1    | **Install Debian 12**                                              | ✅ **COMPLETE.** A minimal net-install of Debian 12 (Bookworm) is complete. The system has an SSH server, standard utilities, no desktop environment, and a `sudo`-privileged user with the root account disabled.                                                                                  |
| 2    | **Basic Server Hardening**                                         | Secure the server against common threats. This involves:<br>- **SSH Key-Only Authentication:** Disable password logins to prevent brute-force attacks.<br>- **UFW Firewall:** Configure a "default deny" firewall to block all unauthorized incoming traffic, allowing only SSH.<br>- **Fail2ban:** Install and configure Fail2ban to automatically ban IPs that exhibit malicious behavior like repeated failed logins.<br>- **Unattended Upgrades:** Enable automatic installation of security patches to minimize vulnerability windows. |
| 3    | **NVIDIA Driver & CUDA Toolkit**                                   | Install the proprietary NVIDIA drivers for the GeForce GTX 1080 Ti and the corresponding CUDA toolkit. This is a prerequisite for leveraging the GPU for hardware-accelerated machine learning tasks.                                                                                               |
| 4    | **Docker & Docker Compose Setup**                                  | Install Docker and Docker Compose to manage applications in isolated containers. This is a core technology for the project, providing sandboxing and simplifying deployment. We will cover the fundamentals of Docker since this is the user's first time with it.                                |
| 5    | **Deploy Ollama & Local LLM**                                      | Deploy Ollama as a Docker container. Select and download a lightweight LLM (e.g., a 7B parameter model like Llama 3 8B Instruct or Phi-3 Mini) that can run efficiently on the GTX 1080 Ti with 11GB of VRAM. Verify the LLM is accessible and operational via its API.                           |
| 6    | **Deploy OpenClaw (Sandboxed)**                                    | Deploy the OpenClaw AI agent in a dedicated, hardened Docker container. This is a critical security step due to the agent's ability to execute terminal commands and access the filesystem. Configuration will focus on:<br>- **Strict Isolation:** Use Docker networking to isolate OpenClaw from other services.<br>- **Least-Privilege Filesystem Access:** Mount only the specific directories OpenClaw needs, in read-only mode where possible. |
| 7    | **Configure Price Monitoring & Alerting**                          | Configure OpenClaw's primary task: monitoring Amazon product prices. Set up a secure, FOSS-based alerting mechanism. Options to evaluate include:<br>- **Gotify:** A self-hosted push notification server.<br>- **Apprise:** A library that supports dozens of notification services, including Telegram, Discord, and email via SMTP.                                                              |
| 8    | **LAN Access to Web UIs**                                          | Ensure that any services with web interfaces (like a potential Gotify UI or a monitoring dashboard) are accessible from other devices on the local network by configuring the firewall (UFW) to allow the necessary ports.                                                                         |
| 9    | **Server Monitoring Strategy**                                     | Implement a basic, lightweight monitoring and alerting strategy for the `uranus` server itself. This could involve using a simple tool like `uptimekuma` (in a Docker container) to monitor service health and resource usage (CPU, RAM, Disk).                                                 |
| 10   | **Backup Strategy**                                                | Design and recommend a cost-effective backup strategy. Since the user has no NAS, this will likely involve:<br>- **Option 1 (External Drive):** Using a new external USB drive connected to the server.<br>- **Option 2 (Cloud):** Using a free-tier or low-cost cloud object storage service (e.g., Backblaze B2, Wasabi) with a command-line tool like `rclone` or `restic` for encrypted, automated backups. |
| 11   | **AT&T Gateway Hardening**                                         | Review and apply security best practices to the ISP-provided router. This includes:<br>- Changing default admin credentials.<br>- Disabling WPS (Wi-Fi Protected Setup).<br>- Disabling UPnP (Universal Plug and Play).<br>- Reviewing and removing any unnecessary open ports or port forwarding rules. |

---

## Phase 2: Remote Access & Networking (Future)

This phase focuses on securely exposing services to the internet and improving the internal network structure.

| Step | Task                                       | Description                                                                                                                                                                                               |
| :--- | :----------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1    | **Domain & DNS**                           | Purchase a domain name and configure DNS records (likely with a provider like Cloudflare for its free security features) to point to the home IP address.                                                    |
| 2    | **Reverse Proxy with SSL**                 | Deploy a reverse proxy (e.g., Nginx Proxy Manager or Traefik) in a Docker container. This will manage incoming web traffic, terminate SSL/TLS, and route requests to the appropriate internal services without exposing them directly. It will be configured to automatically obtain and renew free SSL certificates from Let's Encrypt. |
| 3    | **VPN for Remote Access**                  | Set up a VPN server (e.g., WireGuard) on `uranus`. This will provide secure, encrypted access to the entire home network from remote locations, acting as the primary method for remote administration and service access, avoiding direct exposure of services to the internet. |
| 4    | **Network Segmentation (VLANs)**           | Discuss the concept and benefits of network segmentation using VLANs. Plan for creating separate virtual networks for different device types (e.g., servers, IoT devices, trusted clients) to prevent lateral movement in case one device is compromised. Implementation would depend on acquiring VLAN-capable network hardware. |

---

## Phase 3: Expansion (Future)

This phase focuses on integrating more hardware and services into the homelab ecosystem.

| Step | Task                                         | Description                                                                                                                                                                                                                                                                    |
| :--- | :------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1    | **Integrate Gaming Rig for LLM Inference**   | Install Ollama on the powerful Windows 11 gaming rig (RTX 5080). Configure the OpenClaw agent on `uranus` to offload LLM inference requests to the gaming rig, freeing up resources on the server.                                                                                   |
| 2    | **Integrate Other Devices**                  | Explore use cases for the Mac Mini and gaming laptop. This could involve running specific services, acting as development environments, or serving as additional nodes in a future cluster.                                                                                       |
| 3    | **Advanced Monitoring & Logging**            | Deploy a more advanced monitoring stack, such as the Prometheus/Grafana/Loki stack, to aggregate metrics and logs from all services and hardware. Set up an Intrusion Detection System (IDS) like Suricata to monitor network traffic for suspicious activity.                       |
| 4    | **Additional Self-Hosted Services**          | Explore and recommend other valuable open-source services based on user interest, such as:<br>- `AdGuard Home`: Network-wide ad and tracker blocking.<br>- `Vaultwarden`: Self-hosted password manager.<br>- `Immich`: Self-hosted photo and video backup.<br>- `Jellyfin`: Self-hosted media server. |

