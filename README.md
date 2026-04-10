# 🏠 flabbyaxe's Homelab

**A production-grade home infrastructure built to solve real problems, not to collect hardware.**

> *Started with a NAS to cut cloud storage costs. Grew into a zero-trust, SSO-protected, family-serving platform after my mom's memories were sitting on 10-year-old dying hard drives.*

---

## 👤 About

| **Handle**   | flabbyaxe                                                         |
| ------------ | ----------------------------------------------------------------- |
| **GitHub**   | [Flabbyaxe272](https://github.com/Flabbyaxe272)                   |
| **LinkedIn** | [justincfarris272](https://www.linkedin.com/in/justincfarris272/) |
| **Email**    | justin@farrisfam.org                                              |

---

## 🧠 Philosophy

Every service in this homelab exists because it solved a real problem. Nothing is here for show.

This started in September 2024 with a formal Change Request to my wife (yes, really) to justify replacing cloud storage subscriptions costing $200+/year with a self-hosted NAS. The math was clear. The proposal was approved. The homelab was born.

What followed was a cascading series of real problems demanding real solutions:
- Family photos on aging hard drives → **Immich + TrueNAS**
- Non-technical family needing access → **Authentik SSO**
- Secure remote access without exposing home IP → **WireGuard + VPS**
- DDoS attack on public services → **nginx + iptables geo-IP blocking**
- Password sprawl across services → **Authentik OpenID Connect for everything**

The result is a zero-trust home infrastructure serving real non-technical users, maintained by one person, documented like a production environment.

---

## 🏗️ Architecture as current

```
Internet
    │
    ▼
Cloudflare (DNS)
    │
    ▼
DigitalOcean VPS (Ubuntu Server)
├── nginx reverse proxy (entry point)
├── WireGuard tunnel (encrypted back-channel)
└── iptables (geo-IP blocking — US only, DDoS mitigation)
    │
    ▼ (via WireGuard tunnel)
HP Laptop — Ubuntu Server (Edge Device / Traefik RP)
├── Traefik (reverse proxy + TLS termination)
├── AdGuard Home DNS1
└── Authentik (Identity Provider / SSO)
    │
    ├──▶ TrueNAS Server (Main Services)
    │     ├── Immich (photos.farrisfam.org)
    │     ├── NextCloud + Collabora (cloud.farrisfam.org)
    │     ├── Navidrome (music.farrisfam.org)
    │     ├── Jellyfin (local only)
    │     ├── Gitea (local only, mirrored to GitHub)
    │     ├── Syncthing (local only)
    │     └── AdGuard Home DNS2 (redundant)
    │
    └──▶ Cloudflare Pages (offsite)
          └── Recipebook (recipes.farrisfam.org)
```

**Tailscale** provides secure remote access to local-only services (like dashboards) without exposing them publicly.

---

## 🖥️ Hardware

### TrueNAS — Main Service Server
| Component | Spec |
|---|---|
| CPU | AMD Ryzen 5 4560 Pro (ECC support) |
| RAM | 32GB DDR4 ECC |
| Storage | 30TB raw / 15.5TB usable with redundancy |
| OS | TrueNAS SCALE |

*Started with a 3-way mirror at 3TiB. Expanded with 2×10TB drives (Christmas 2025) to support whole-family photo storage.*

### HP Laptop — Edge Device
| Component | Spec |
|---|---|
| OS | Ubuntu Server |
| Role | Network edge, Traefik reverse proxy, Authentik IdP, AdGuard DNS1 |

*Originally a Docker experimentation device. Repurposed as the network edge device due to its limited compute — keeping heavy services off the critical path.*

### Clients
| Name | OS | Purpose |
|---|---|---|
| dude-client | Windows | Gaming / HTPC / emulation |
| second-dude | Windows 11 | School / one-off projects |
| fourth-dude | Fedora 43 | Main daily driver |

---

## ⚙️ Services

### 🔐 Identity & Access
| Service | URL | Notes |
|---|---|---|
| **Authentik** | auth.farrisfam.org | Central IdP — OpenID Connect SSO for all services |

Every public-facing service authenticates through Authentik. One account, one login, every service. Non-technical family members (parents, siblings) can access Immich and Navidrome without managing multiple credentials.

### 🌐 Networking & Proxy
| Service | Access | Notes |
|---|---|---|
| **Traefik** | Internal | Reverse proxy + automatic TLS |
| **AdGuard Home** | Internal only | DNS blocklisting, redundant across both servers |
| **Tailscale** | Remote | Zero-trust remote access to internal services |

AdGuard runs on both servers (DNS1 on edge device, DNS2 on TrueNAS), physically separated for redundancy. DNS-over-HTTPS is a work in progress.

### 📁 Storage & Files
| Service                   | URL                 | Notes                                               |
| ------------------------- | ------------------- | --------------------------------------------------- |
| **NextCloud + Collabora** | cloud.farrisfam.org | Document storage, digital file backup, live editing |
| **Syncthing**             | Internal only       | Note syncing across phone and laptops               |
| **TrueNAS**               | Internal            | 15.5TB usable, redundant storage                    |

### 📸 Media
| Service | URL | Notes |
|---|---|---|
| **Immich** | photos.farrisfam.org | Family photo platform — primary family service |
| **Navidrome** | music.farrisfam.org | Personal music collection streaming |
| **Jellyfin** | Internal only | Film library |

### 🛠️ Development
| Service | URL | Notes |
|---|---|---|
| **Gitea** | Internal only | Self-hosted git, redundant, mirrors to GitHub |
| **Recipebook** | recipes.farrisfam.org | MkDocs site, built in Gitea, deployed via Cloudflare Pages |

---

## 🔒 Security Model

Public services never directly expose the home network. The full traffic path for an external request:

```
User → Cloudflare DNS → DigitalOcean VPS
    → nginx (entry point) + iptables (geo-block non-US IPs)
    → WireGuard encrypted tunnel
    → Traefik (reverse proxy + TLS)
    → Authentik (auth check)
    → Service
```

The VPS was added after a DDoS incident on public services. Response: spun up a DigitalOcean droplet, configured nginx as a proxy, implemented iptables rules to block non-US traffic. Incident resolved. Geo-blocking has been in place since.

**Secret management:** All sensitive values (passwords, API keys, tokens) are kept in `.env` files excluded from version control via `.gitignore`. Repos contain `.env.example` files with placeholder values.

---

## 📋 Change Requests

This homelab is managed with formal Change Requests - documented proposals written before implementing significant changes. Each CR covers the problem being solved, alternatives considered, implementation plan, and risks.

| CR                                              | Date      | Summary                                                        |
| ----------------------------------------------- | --------- | -------------------------------------------------------------- |
| CR-001                                          | Sept 2024 | Initial NAS deployment (replacing cloud storage subscriptions) |
| *(additional CRs coming in `/change-requests`)* |           |                                                                |

---

## ⚠️ Known Limitations & Next Steps

Documenting gaps is part of operating honestly.

| Limitation                          | Impact                                                      | Planned Solution                    |
| ----------------------------------- | ----------------------------------------------------------- | ----------------------------------- |
| Consumer router as primary firewall | Limited network segmentation                                | Replace with OPNsense or pfSense    |
| No managed switch                   | Can't enforce VLAN separation between IoT, servers, clients | Managed switch + VLAN configuration |
| AdGuard not yet on DNS-over-HTTPS   | DNS queries unencrypted in transit                          | DoH configuration (in progress)     |

---

## 📁 Repo Structure (WIP)

```
homelab/
├── README.md
├── change-requests/
│   └── CR-001-nas-deployment.md
├── architecture/
│   └── network-diagram.drawio
│   └── network-diagram.png
├── authentik/
│   ├── docker-compose.yml
│   ├── .env.example
│   └── README.md
├── traefik/
│   ├── docker-compose.yml
│   ├── .env.example
│   └── README.md
├── adguard/
├── nextcloud/
├── immich/
├── navidrome/
├── jellyfin/
├── gitea/
└── syncthing/
```

---

*Built for my family. Documented for anyone who finds it useful.*
