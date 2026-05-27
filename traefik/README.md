# Traefik Reverse Proxy - Homelab

A self-hosted Traefik (v3) reverse proxy setup using Docker Compose. This repo documents not just the configuration, but the _why_ behind decisions and the problems encountered along the way.

---

## Table of Contents

- [[#Why Traefik]]
- [[#How It Works and Getting Started]]
- [[#Project Structure]]
- [[#Configuration Breakdown]]
- [[#Lessons Learned & Troubleshooting]]
- [[#References]]

---

## Why Traefik

When choosing a reverse proxy for my homelab, the main contenders were **Nginx Proxy Manager** and **Traefik**. 

I ruled out NGINX Proxy Manager early in my setup of my homelab. The GUI would have been convenient, for a number of reasons. However, I'm not building my home lab for convenience. I'm building it for reliability and recoverability. So issues arose in my mind: 

- relying on steps for a GUI that may update over time
- Not consistently saving any backups 
- Recovery would be a long process to rebuild.

However, with Traefik, I can do version control and I can understand what changes were made when.

**The core reason:** everything is declared in config files. No clicking, no state drift, no wondering what was changed last Tuesday.

---

## How It Works and Getting Started

### How It Works

Traefik's mental model has four layers:

```
Entrypoints -> Routers -> Middlewares -> Services
```

- **Entrypoints**: the ports Traefik listens on (i.e., `websecure` on port 443)
- **Routers**: rules that match incoming requests (e.g. `Host('app.example.com')`)
- **Middlewares**: transformations applied before the request hits a service (redirects, auth, headers)
- **Services**: the actual upstream containers receiving traffic

Static config (`traefik.yml`) defines entrypoints, providers, and certificate resolvers, and other things that require a restart to change. Dynamic config (`config/`) defines routers, middlewares, and services, able to be hot-reloaded without a restart.

### Getting Started

I would strongly suggest that you take some time and try out the Traefik examples to start out. It's the best way you'll be able to tailor to your needs, and if you need help, I've found that AI models usually get it wrong. So it's best that you attempt to find and gather your resources like stackexchange. 

---

## Project Structure

```
traefik/
├── docker-compose.yml       # Traefik service definition
├── traefik/
│   ├── traefik.yml          # Static config (entrypoints, providers, ACME)
│	└── config/
│	    ├── middlewares.yml  # Reusable middleware definitions
│	    └── services.yml     # Dynamic router + service definitions
└── certs/                   # TLS certs example
```

---

## Configuration Breakdown

### Static Config (`traefik.yml`)

```yaml

# -- Change EntryPoints here...
entryPoints:
  web:
    address: ":80"
    # Redirect all HTTP to HTTPS
    http:
      redirections:
        entryPoint:
          to: websecure
          scheme: https
  websecure:
    address: ":443"
    proxyProtocol:
      trustedIPs:
        - "10.0.0.1" # Example WireGuard IP to a different network
    http:
      tls: {}
      middlewares:
        - rate-limit-Global@file
    transport:
      respondingTimeouts: # increasing timeouts due to Immich/Nextcloud file upload time [GBs of data (videos) can take a while]
        readTimeout: 600s
        idleTimeout: 600s
        writeTimeout: 600s

# -- Configure your CertificateResolver here...
certificatesResolvers:
  cloudflare:                 # Name of your certresolver
    acme:
      email: example@testy.net
      storage: /var/traefik/certs/acme.json
      caServer: "https://acme-v02.api.letsencrypt.org/directory"
      dnsChallenge:
        provider: cloudflare  # <-- (Optional) Change this to your DNS provider
        resolvers:
          - "1.1.1.1:53"
          - "8.8.8.8:53"

providers:
  docker:
    exposedByDefault: false   # containers must opt-in with traefik.enable=true
    network: proxy
  file:
    directory: /etc/traefik/config/
    watch: true               # hot-reload dynamic config on change

```

> `exposedByDefault: false` is important. Without it, every container on the Docker network is automatically exposed, which a security risk in a homelab with multiple services.

### Docker Labels (per service)

```yaml
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.myapp.rule=Host(`myapp.testy.net`)"
      - "traefik.http.routers.myapp.entrypoints=websecure"
      - "traefik.http.routers.myapp.tls=true"
      - "traefik.http.routers.myapp.tls.certresolver=cloudflare"
      - "traefik.http.routers.myapp.service=myapp"
      - "traefik.http.services.myapp.loadBalancer.server.port=8080"
```

### Middlewares (`config/middlewares.yml`)

```yaml
http:
  middlewares:
    secure-headers:
      headers:
        stsSeconds: 31536000
        forceSTSHeader: true
        contentTypeNosniff: true
        browserXssFilter: true

    redirect-to-https:
      redirectScheme:
        scheme: https
        permanent: true
```

---

## Lessons Learned & Troubleshooting

These are real problems hit during setup, not edge cases from documentation.

### 1. Containers not being discovered

**Symptom:** Traefik dashboard showed no routers, services returned 404.

**Cause:** The service container was not on the same Docker network as Traefik. Docker isolates networks by default — a container on `bridge` cannot be reached by a container on a custom network.

**Fix:** Define a shared external network and attach both Traefik and the target service to it.

```yaml
networks:
  proxy:
    external: true
```

Create it once: `docker network create proxy`

---

### 2. TLS cert not provisioning

**Symptom:** Browser showed untrusted cert / ACME errors in logs.

**Cause:** Used HTTP challenge behind a NAT — port 80 wasn't reachable from the internet for Let's Encrypt to verify.

**Fix:** Switched to DNS challenge via Cloudflare. This works without exposing port 80, and also supports wildcard certs.

Required adding Cloudflare API token as an environment variable:

```yaml
environment:
  - CF_DNS_API_TOKEN=${CF_DNS_API_TOKEN}
```



---

### 3. Middleware not applying

**Symptom:** Defined a middleware in `middlewares.yml` but it wasn't being applied to a router.

**Cause:** Middleware references must include the provider prefix. A middleware defined in a file provider needs to be referenced as `middlewarename@file`, not just `middlewarename`.

**Fix:**

```yaml
# Wrong
- "traefik.http.routers.myapp.middlewares=secure-headers"

# Correct
- "traefik.http.routers.myapp.middlewares=secure-headers@file"
```

---

## References

- [Traefik v3 Documentation](https://doc.traefik.io/traefik/)
- [Let's Encrypt DNS Challenge](https://doc.traefik.io/traefik/https/acme/#dnschallenge)
- [Traefik Docker Provider](https://doc.traefik.io/traefik/providers/docker/)
