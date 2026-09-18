# Dude-server

## Reason for existing

## Services

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

> [Why use a broken laptop as a server?](kb/decisions/DECISION-dude-server-broken-laptop-as-a-server)

> Why Docker labels for local containers but file providers for TrueNAS services?

I found that when using Traefik, it doesn't attempt to discover services 
outside it's own box. So, I use static files to declare where my services are. The
downside to this is now I have two places to look for when a route needs debugging.

> 

## Story

I started my tech career in a ISP. We used Microtik for some of the hardware, and I 
thought to myself: "I think that name 'The Dude Server' is a cool name".

I still think it's a cool name, but this server is not a Microtik device. 