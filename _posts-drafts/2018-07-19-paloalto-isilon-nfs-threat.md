---
layout: post
title: Mount an NFS mount through Palo Alto
tags: [ isilon, storage, nfs, vulnerability ]
excerpt_separator: <!--more-->
---

Mounting NFS through Palo Alto

<!--more-->
Similarly to the Palo Alto VPN configuration, setting up a VPN between Palo Alto and an AWS CSR router, there will only be minor changes required.

AWS essentially has majority of the configuration setup for you, but there will be some tweaks that you would need to make.

## Checklist for configuration
- Network
  - Zone
  - Interfaces -> Tunnel
  - Ike Crypto
  - IPSec Crypto
  - Ike Gateways
  - IPSec Tunnels
  - Virtual Routers
- Policies
  - Security
  - NAT (optional)
  - Policy Based Forwarding (optional)
