# 🏠 My Homelab

A practical, low-cost homelab built around Ubuntu Server, CasaOS, Docker, Home Assistant, and a small secondary Raspberry Pi.

The goal of this project is to run useful self-hosted services at home, learn Linux and networking, automate everyday tasks, and keep the setup simple enough to maintain long-term.

> **Note:** This repository intentionally does **not** include private IP addresses, credentials, firewall rules, security keys, access tokens, internal hostnames, or detailed hardening configuration.

---

## 📌 Overview

My homelab is built around two systems:

- **Primary Homelab Server**
  - Ubuntu Server
  - CasaOS
  - Docker
  - Home automation
  - Media services
  - Networking tools
  - Reverse proxy
  - Remote access services

- **Secondary Raspberry Pi Server**
  - Uptime monitoring
  - Secondary DNS service
  - Lightweight backup infrastructure services

The idea is to avoid depending on a single device for everything while still keeping the environment simple.

---

# 🖥️ Primary Server

## Hardware

My main homelab server is based on older laptop hardware that was repurposed instead of being discarded.

### Basic specifications

| Component | Specification |
|---|---|
| CPU | Intel Core i3-5010U |
| Memory | 8 GB RAM |
| System Storage | ~223 GB |
| Additional Storage | Separate media/data drive |
| Operating System | Ubuntu Server 24.04 LTS |
| Container Platform | Docker |
| Management Layer | CasaOS |

This hardware is not powerful by modern server standards, but it is more than enough for a surprisingly capable home server.

One of the main goals of this build is to show that you do not need expensive enterprise hardware to start learning self-hosting.

---

# 🐳 Docker & CasaOS

Most of the applications in my homelab run as Docker containers.

CasaOS provides a convenient web interface for managing many of these applications, while Docker Compose is used when more control over an application is required.

The basic architecture looks like this:

```text
Ubuntu Server
     │
     ├── CasaOS
     │
     └── Docker
          │
          ├── Home Automation
          ├── Networking
          ├── Media
          ├── Monitoring
          └── Utility Services
```

---

# ⚙️ Services Running on the Main Server

## 🏡 Home Assistant

Home Assistant is the heart of my smart-home environment.

It is used for:

- Device automation
- Energy monitoring
- Smart switches
- Solar inverter monitoring
- Notifications
- HomeKit integration
- ESPHome devices
- MQTT-based devices

Home Assistant also acts as the central dashboard for many devices around the house.

---

## 🔌 ESPHome

ESPHome is used for DIY smart-home devices based on ESP8266 and ESP32 hardware.

Examples include:

- Smart switches
- Relay controllers
- Sensors
- Custom IoT devices

ESPHome integrates directly with Home Assistant.

---

## 📡 Mosquitto MQTT

Mosquitto provides the MQTT message broker used by several IoT and automation services.

Typical flow:

```text
IoT Device
    │
    ▼
Mosquitto MQTT
    │
    ▼
Home Assistant / Node-RED
```

---

## 🔄 Node-RED

Node-RED is used for more complex automation workflows.

I use it when an automation becomes easier to understand visually than through a large configuration file.

Typical uses include:

- Home Assistant automation
- Notifications
- Device logic
- API integration
- MQTT workflows

---

## 🎥 Frigate + go2rtc

Frigate is used for local camera processing and NVR functionality.

go2rtc handles camera stream redistribution and helps reduce unnecessary direct connections to cameras.

Typical architecture:

```text
IP Cameras
    │
    ▼
  go2rtc
    │
    ▼
  Frigate
    │
    ▼
Home Assistant
```

This allows camera events and video streams to integrate with the smart-home system.

---

## 🌐 Pi-hole

Pi-hole provides network-level DNS filtering.

It can block many advertising, telemetry, and unwanted tracking domains before devices on the network connect to them.

The main server acts as one DNS provider while a secondary DNS service runs on another device for redundancy.

---

## 🔀 Nginx Proxy Manager

Nginx Proxy Manager is used as the reverse proxy for selected internal web services.

It provides a simple interface for managing:

- Reverse proxy hosts
- HTTPS certificates
- Internal service routing

Not every service is exposed externally.

Where possible, services remain available only from the local network or through private remote-access solutions.

---

## ☁️ Cloudflare Tunnel

Cloudflare Tunnel is used for selected services that need controlled remote web access.

Instead of directly exposing the server through traditional router port forwarding, the tunnel establishes an outbound connection to Cloudflare.

Simplified traffic flow:

```text
Internet
   │
   ▼
Cloudflare
   │
   ▼
Cloudflare Tunnel
   │
   ▼
Reverse Proxy
   │
   ▼
Internal Service
```

This allows me to avoid exposing the server's public IP address directly for those services.

---

## 🔐 Twingate

Twingate is used as a private remote-access solution.

It acts as an alternative way to reach services inside the home network without making them publicly accessible.

For example:

```text
Laptop / Phone
      │
      ▼
   Twingate
      │
      ▼
Home Network
      │
      ▼
Private Services
```

I consider this as an important backup method of accessing the homelab remotely.

---

## 📦 Portainer

Portainer provides another interface for managing Docker containers.

I mainly use it for:

- Checking container status
- Viewing logs
- Inspecting networks
- Inspecting volumes
- Restarting containers
- Basic Docker troubleshooting

---

## 🎬 Jellyfin

Jellyfin is used as the local media server.

It allows media stored on the server to be streamed to supported devices on the network.

---

## ⬇️ qBittorrent

qBittorrent runs as a container and is used as the download client for Linux ISOs and other legitimate downloads.

Running it inside Docker keeps the application isolated from the rest of the system and makes management easier.

---

## 📊 MySpeed

MySpeed is used to perform regular Internet speed tests.

It helps track:

- Download speed
- Upload speed
- Latency
- ISP performance over time

---

## 🔎 Network Monitoring

I also run lightweight network-monitoring tools to help understand what devices are connected to the network and whether important services are reachable.

This is useful for both troubleshooting and learning.

---

# 🥧 Secondary Server

The secondary server is a Raspberry Pi.

## Hardware

| Component | Specification |
|---|---|
| Device | Raspberry Pi 4 |
| Memory | 2 GB |
| Operating System | Raspberry Pi OS Lite |
| Container Platform | Docker |

The Raspberry Pi is intentionally kept lightweight.

It does not run the heavy applications from the main server.

Instead, it provides a few important supporting services.

---

## ❤️ Uptime Kuma

Uptime Kuma monitors important services in the homelab.

It checks whether services such as:

- Home Assistant
- DNS
- Reverse proxy
- Internet connectivity
- Docker applications

are reachable.

This is especially useful because the monitoring system is separate from the primary server.

If the main server goes down, the secondary device can still report that something is wrong.

---

## 🌐 Secondary DNS

The Raspberry Pi also provides a secondary DNS service.

This prevents the entire network from losing DNS resolution if the main DNS server or primary homelab server is temporarily unavailable.

The general design is:

```text
Clients
   │
   ├──── Primary DNS
   │
   └──── Secondary DNS
```

This simple redundancy has proven very useful.

---

# 🌍 Network Design

A simplified version of the network looks like this:

```text
                        Internet
                           │
                     ISP Router / ONT
                           │
                           ▼
                      Home Network
                           │
          ┌────────────────┴────────────────┐
          │                                 │
          ▼                                 ▼
 Primary Homelab Server             Raspberry Pi Server
 Ubuntu Server                      Raspberry Pi OS
 CasaOS                             Docker
 Docker                             Uptime Kuma
 Home Assistant                     Secondary DNS
 Pi-hole
 Nginx Proxy Manager
 Frigate
 Node-RED
 Jellyfin
 etc.
          │
          ├──── Cloudflare Tunnel
          │
          └──── Twingate Remote Access
```

The exact internal addressing and firewall configuration are intentionally not published.

---

# 🛡️ Security Approach

Security is treated as several layers rather than relying on one product.

At a high level, the server uses:

- Host firewall
- Restricted network access
- Private remote-access services
- Reverse proxy
- File-integrity monitoring
- System auditing
- Log monitoring
- Automated threat detection
- Regular operating-system updates
- Docker isolation
- Service-specific access controls
- Local network segmentation where practical

Detailed firewall rules, detection rules, internal addressing, credentials, API tokens, and security configuration are intentionally excluded from this repository.

A public homelab repository should explain the **design**, not publish a blueprint for bypassing it.

---

# 🔄 Remote Access Strategy

I currently use two main methods.

## Public web access

For selected services:

```text
User
  │
  ▼
Cloudflare
  │
  ▼
Cloudflare Tunnel
  │
  ▼
Reverse Proxy
  │
  ▼
Approved Service
```

## Private access

For administrative or private services:

```text
User Device
    │
    ▼
Twingate
    │
    ▼
Private Home Network
    │
    ▼
Homelab Services
```

The second method allows services to remain private rather than exposing everything to the Internet.

---

# 🧰 Basic Build Guide

This is not intended to be an exact copy-and-paste deployment guide.

The purpose is to give beginners an idea of the order in which I built the homelab.

## 1. Install Ubuntu Server

Install the current Ubuntu Server LTS release on the machine.

During installation:

- Create a normal user
- Enable SSH only if required
- Install security updates
- Give the server a stable LAN address

---

## 2. Update the system

```bash
sudo apt update
sudo apt upgrade
```

Reboot if required.

---

## 3. Install Docker

Use Docker's official installation documentation for the current Ubuntu release.

Verify Docker:

```bash
docker --version
```

and:

```bash
docker compose version
```

---

## 4. Install CasaOS

CasaOS provides a convenient management interface for a beginner homelab.

After installation, use the CasaOS dashboard to deploy simple applications and Docker Compose for applications requiring more customization.

---

## 5. Deploy core network services

I recommend starting with only a few services:

1. DNS
2. Home Assistant
3. Uptime monitoring
4. Reverse proxy
5. Remote access

Once those are stable, add additional applications gradually.

---

## 6. Add a secondary monitoring device

A Raspberry Pi or other low-power computer is ideal.

Run services such as:

- Uptime Kuma
- Secondary DNS

on the secondary device rather than putting every service on the main server.

---

## 7. Configure remote access

Avoid exposing every application directly to the Internet.

Instead consider solutions such as:

- Cloudflare Tunnel
- Twingate
- WireGuard
- Tailscale

Choose whichever solution best fits your requirements.

---

## 8. Add security monitoring

At minimum, consider:

- A host firewall
- Strong authentication
- Regular updates
- Log monitoring
- File-integrity monitoring
- Backups

Do not blindly copy someone else's firewall or security configuration.

Every home network is different.

---

# 💡 Lessons Learned

Some of the biggest lessons from building this homelab have been:

### Start small

Running twenty containers immediately makes troubleshooting difficult.

Install services one at a time and understand how each one works.

### Docker makes experimentation easier

Containers make it much easier to test, update, remove, and rebuild applications without heavily modifying the host operating system.

### Monitoring should be independent

If your monitoring system runs only on the server being monitored, it disappears when that server fails.

A small Raspberry Pi is perfect for external monitoring.

### DNS redundancy matters

When your only DNS server goes offline, it can make the entire network appear to have lost Internet access.

A secondary DNS server prevents this.

### Do not expose everything

Most administrative dashboards do not need direct Internet access.

Private remote-access tools are usually a better option.

### Older hardware is still useful

A modest Intel i3 system with 8 GB RAM can run many useful homelab services when workloads are chosen carefully.

---

# 🚧 Future Plans

Possible future improvements include:

- Better backup automation
- Additional storage
- Improved monitoring dashboards
- Container configuration backups
- Infrastructure documentation
- UPS monitoring
- More network segmentation
- Additional Home Assistant integrations
- Automated configuration deployment

---

# ⚠️ Disclaimer

This repository documents my personal homelab for educational purposes.

It is **not** intended to be a production security guide.

Never copy passwords, API keys, private certificates, internal addresses, firewall rules, or access tokens into a public GitHub repository.

Always review the official documentation for each project before deploying it.

---

# ⭐ Why I Built This

My homelab started as a way to experiment with networking and self-hosted applications.

Over time it became a platform for learning:

- Linux administration
- Docker
- Networking
- DNS
- Reverse proxies
- Home automation
- Monitoring
- Cybersecurity fundamentals
- IoT
- Troubleshooting

Hopefully this repository helps someone else realize that you can learn a huge amount from a simple second-hand computer and a Raspberry Pi.

---

## 📚 Main Technologies

- Ubuntu Server
- Raspberry Pi OS
- Docker
- Docker Compose
- CasaOS
- Home Assistant
- ESPHome
- MQTT / Mosquitto
- Node-RED
- Frigate
- go2rtc
- Pi-hole
- Nginx Proxy Manager
- Cloudflare Tunnel
- Twingate
- Portainer
- Jellyfin
- qBittorrent
- Uptime Kuma

---

If you are building your first homelab, my biggest recommendation is:

> **Start with one machine, one service, and one problem you want to solve. Then grow from there.**
