---
layout: post
title: Ditch the ACL on the Legacy Crypto VPN
tags: [ crypto, ipsec, security, vpn, tunneling ]
img: https://raw.githubusercontent.com/TekRx/tekrx.github.io/master/assets/tunnel0.png
---



![oldnewway](/assets/oldwaynewway.jpg)

One of our clients had ran into an interesting issue where they were unable to setup multiple GRE tunnels with IPSEC encryption to two different destinations while having the same source interface.

In theory this should work but for some reason the router was able to only encrypt/decrypt traffic on one tunnel.


Although there are multiple options that we can use to resolve this issue, such as DMVPN or Site to Site tunneling, we ended up altering the design slightly to move away from the legacy crypto configuration and to use the next generation crypto configuration to keep things simple.
