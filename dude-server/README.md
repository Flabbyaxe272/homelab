# Dude-server

## Specs

```
flabbyaxe@the-dude-server:~$ neofetch
            .-/+oossssoo+/-.
        `:+ssssssssssssssssss+:`
      -+ssssssssssssssssssyyssss+-
    .ossssssssssssssssssdMMMNysssso.
   /ssssssssssshdmmNNmmyNMMMMhssssss/
  +ssssssssshmydMMMMMMMNddddyssssssss+
 /sssssssshNMMMyhhyyyyhmNMMMNhssssssss/
.ssssssssdMMMNhsssssssssshNMMMdssssssss.   flabbyaxe@the-dude-server
+sssshhhyNMMNyssssssssssssyNMMMysssssss+   -------------------------
ossyNMMMNyMMhsssssssssssssshmmmhssssssso   OS: Ubuntu 24.04.5 LTS x86_64
ossyNMMMNyMMhsssssssssssssshmmmhssssssso   Host: HP EliteBook 840 G3
+sssshhhyNMMNyssssssssssssyNMMMysssssss+   Kernel: 6.8.0-139-generic
.ssssssssdMMMNhsssssssssshNMMMdssssssss.   Uptime: 4 days, 6 hours, 32 mins
 /sssssssshNMMMyhhyyyyhdNMMMNhssssssss/    Packages: 1239 (dpkg), 7 (snap)
  +sssssssssdmydMMMMMMMMddddyssssssss+     Shell: bash 5.2.21
   /ssssssssssshdmNNNNmyNMMMMhssssss/      Resolution: 1366x768
    .ossssssssssssssssssdMMMNysssso.       Terminal: /dev/pts/0
      -+sssssssssssssssssyyyssss+-         CPU: Intel i5-6300U (4) @ 3.000GHz
        `:+ssssssssssssssssss+:`           GPU: Intel Skylake GT2 [HD Graphics 520]
            .-/+oossssoo+/-.               Memory: 2176MiB / 7820MiB

```


## Key Decisions

> Why use a broken laptop as a server?

Initially, it was the only spare computer I had. Being pushed to use what we had
without spending more money, I figured I'd learn a few things with it. And oh boy, did 
I learn a few things. Because of this computer, I learned: 

- How to repair a laptop keyboard
- How to install Ubuntu Server
- How to manage Docker without a GUI
- Get comfortable managing networking within a system with iptables and ufw
- Getting comfortable with the command line (and consequently looking up commands in Google)
- How Traefik (and ultimately reverse-proxies) work
- Learning that I love IaC (infrastructure as code) with Traefik v3 config files.

> Why Docker labels for local containers but file providers for TrueNAS services?

I found that when using Traefik, it doesn't do well trying to discover services 
outside it's own box. So, I use static files to declare where my services are. The \
downside to this is now I have two places to look for when a route needs debugging.

## Story

I started my tech career in a ISP. We used Microtik for some of the hardware, and I 
thought to myself: "I think that name 'The Dude Server' is a cool name".

I still think it's a cool name, but this server is not a Microtik device. 