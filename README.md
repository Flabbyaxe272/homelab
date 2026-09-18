# flabbyaxe's Homelab

**A home infrastructure built to solve real problems, not to collect hardware.**

> *Started with a NAS to cut cloud storage costs. Grew into a zero-trust, SSO-protected, family-serving platform over the course of two years.*

---

## About

| **Handle**   | flabbyaxe                                                         |
| ------------ | ----------------------------------------------------------------- |
| **GitHub**   | [Flabbyaxe272](https://github.com/Flabbyaxe272)                   |
| **LinkedIn** | [justincfarris272](https://www.linkedin.com/in/justincfarris272/) |
| **Email**    | justin@farrisfam.org                                              |

---

## Philosophy

I realized something recently: 

> No one will take you seriously if you don't type it out yourself. 

So, I'm starting my documentation fresh. For people actually understand it. For 
myself to understand it.

This all started out with me wanting to save a few dollars in 2024. 
After drafting up a Change Request form for my wife (ya, I'm a nerd), 
I found that she actually agreed with me, and so the homelab was born. 

A few screws later, I'm running a photo storage service, a music server, a document
server, audiobooks, movies, shows, archiving documentation, even dabbling in locally
run AI models. 

So none of this is for show. It's all for solutions to problems I've seen and grown
to take advantage for the last 2 years. And I hope you find this documentation useful.

---

## Architecture as current

```
Internet
    │
    ▼
Cloudflare (DNS)
    │
    ├──▶ Cloudflare Pages (offsite)
    |     ├── Blog/portfolio (justin.farrisfam.org)
    |     └── Recipebook (recipes.farrisfam.org)
    |
    ▼
DigitalOcean VPS (Ubuntu Server)
├── nginx stream (entry point)
├── iptables (geo-IP blocking - US only, DDoS mitigation)
└── WireGuard tunnel (encrypted back-channel)
    │
    ▼ 
Dude-server (Edge Device)
├── Traefik (reverse proxy + TLS termination)
├── AdGuard Home DNS1
└── Authentik (Identity Provider / SSO)
    │
    └──▶ TrueNAS Server (Main Services)
          ├── Immich (photos.farrisfam.org)
          ├── NextCloud + Collabora (cloud.farrisfam.org)
          ├── Navidrome (music.farrisfam.org)
          ├── Jellyfin (local only)
          ├── Gitea (local only, mirrored to GitHub)
          ├── Syncthing (local only)
          └── AdGuard Home DNS2 (redundant)

```

---

## 📁 Repo Structure

```
homelab/
├── README.md
├── dude-server/
│   ├── README.md
├── truenas/
│   └── README.md
├── vps/
│   └── README.md
└── change-requests/
```

---

*Built for my family. Documented for anyone who finds it useful.*

Documentation is licensed under CC BY 4.0 — https://creativecommons.org/licenses/by/4.0/
