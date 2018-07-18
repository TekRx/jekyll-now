---
layout: post
title: Palo Alto VTI to AWS CSR
tags: [ vti, vpn, configuration, paloalto, AWS ]
excerpt_separator: <!--more-->
---

Connect your Palo Alto to AWS CSR with VTI tunnel

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
