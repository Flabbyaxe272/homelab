---
tags:
  - dude-server
  - traefik
  - service
created: 2026-09-18
last-edited: 2026-09-28
---



# Traefik and Reverse Proxy

I wanted to create a small space for my experience with Traefik, and why I would 
highly recommend it to anyone trying to build their homelab.

## Reverse Proxy

First off, I would always recommend setting up a Reverse Proxy for your homelab if you're hosting some "permanent" services. Even if you only use it a couple of times a month, and then take it down three months later, it's so much easier to type a domain name and just get to where you need to go, without remembering or bookmarking the IP:port for every little service.

How a Reverse Proxy generally works is a request comes in, and the Reverse Proxy sends the request to the right IP and right port. If it's a webpage, you could have the setup as: 

```
Client
 |   Sends the request
 V
Reverse Proxy
 |
 V
 
```