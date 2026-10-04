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
 |   Matches request with rules predetermined
 V
Service by port
```

You could have several services on various ports of the same machine and have different names. Like:

```
music.example.com -> 10.0.0.1:3000
camera.example.com -> 10.0.0.1:3050
cloud.example.com -> 10.0.0.2:3000
```


## Traefik vs other options

I like Traefik for the static/dynamic file configuration options. Recently has been renamed as install (startup) and routing configurations. When you start up Traefik, there's no GUI dashboard unless you turn it on explicitly in the static (startup) `traefik.yml` file via api. It is managed from files that are managed with text editing. 

## How Traefik works

When Traefik receives a URL request, it runs through a path of 4 different components: 

1. Entrypoint: The declared port in which Traefik expects the traffic to be coming in from. Most often is 443 or 80 for web traffic, other ports for various other programs and uses, like games. 
2. Routers: The matching program to read the request and match it to a specific path.
3. Middleware: Checkpoints along the path to the service to ensure the request is valid. Usually auth or rate-limiting.
4. Service: the endpoint where the request is passed onto, the actual service. 

