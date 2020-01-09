---
layout: post
title: Palo Alto VPN configuration
tags: [ pan, vpn, configuration, paloalto ]
excerpt_separator: <!--more-->
---

Straight Forward configuration of a VPN tunnel on Palo Alto

<!--more-->

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
