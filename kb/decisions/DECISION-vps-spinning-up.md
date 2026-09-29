---
tags:
  - vps
created: 2026-09-28
last-edited: 2026-09-28
---


# Why does the VPS Exist?

## Reason

To prevent attacks from hitting my hardware directly from an open port, slowing down the network.

------------------

## Story

When I was running my services off my public IP, everything was great. Local IP could connect quickly, didn't need to worry about opening too much up, just a couple of ports on my consumer Netgear router I had at the time, for port-forwarding to my various services. 

Until one day when I found myself being DDOS'd. 

It was a random Tuesday, and I found my internet on my phone being slow. With that, I check my services, and everything is being slow. I check my router, thinking that it's given up the ghost in the machine, and I can convince my wife to get a new router.

I check the logs, and found that a big range of IP addresses from Brazil were all attempting to access my network, attempting various ports. It was logging about 10 per second, enough to slow down the router and make internal requests go slow. 

I immediately removed all the rules for port forwarding, and attempted to grab a new IP address. I found my internet provider doesn't lease out new IPs very often, which makes it like a static, but subject to change. Usually great for hosting services, terrible in this instance when I wanted immediate change.

After doing some searching and finding a few articles, I found the general consensus was to have a Virtual Private Server (VPS) run with a public IP and have a Wireguard tunnel to your network, provided you have the correct precautions set up, like firewalls and the like.

----

## Alternatives Considered

1. Reverse Proxy with VPN (Tailscale/WireGuard)
2. Cloudflare Tunnel (Zero Trust)

> Direct Port Forwarding with Traefik 
> I used to do this: punching a hole through my router to allow WireGuard back into my home network.

### Reverse Proxy with VPN

Pros: 
- No ports exposed to the internet
- End-to-end encrypted tunneling
- Network-level security (Device needs to have been authenticated)
- No dependency on third parties
- Low latency for local clients
- Works with having a dynamic IP

Cons:
- Another app to install, for every client wanting to access my services (i.e., extended fam).
- Mobile device battery drainage from always on VPN.
- VPN may conflict with other VPNs (work especially)
- Hard to troubleshoot with non-techy users.
- Split-tunnel config needed for optimal performance (don't want all requests coming my way anyways.)

### Cloudflare Tunnel (Zero Trust)

Pros: 
- No ports exposed to internet from router
- DDoS protection included
- Free 💰
- Works with having a dynamic IP
- TLS termination from Cloudflare, guaranteed to be accepted on modern devices.
- Access policies from Cloudflare can compliment OAuth.
- Works without another app installed on the clients device.

Cons:
- Traffic routes and is decrypted by Cloudflare (privacy consideration)
- Dependency on third-party availability ^[Previous months have shown that when Cloudflare goes down, so does the rest of the world.]
- Cloudflare ToS prohibits video streaming on free tier (impacts Immich and home video watching)
- Upload limits would affect large file uploads (100 MB file upload MAX)
- Less control over TLS configuration

---