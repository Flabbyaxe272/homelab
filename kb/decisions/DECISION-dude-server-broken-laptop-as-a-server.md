# Why use a broken laptop as a server?

> Decision for [dude-server](homelab_Public/dude-server/README.md)

When I started, the dude-server was the only computer I had, and the budget was zero. Being pushed 
to use what we had without spending more money, I figured I'd learn a few things with it. 
And oh boy, did I learn a few things:

- Repairing a laptop keyboard
- Installing Ubuntu Server
- Managing Docker without a GUI
- Configuring network rules with iptables and ufw
- Getting comfortable with the command line (and consequently looking up commands in Google)
- How Traefik (and ultimately reverse-proxies) work
- Learning that I love IaC (infrastructure as code) with Traefik v3 config files.

But it doesn't come all good and dandy. Because of this computer, I'm stuck with: 

- Aging hardware like battery wear and thermal limits for something not supposed to run
24/7.
- One network interface
- No redundancy
- Limited resources, like CPU and RAM.