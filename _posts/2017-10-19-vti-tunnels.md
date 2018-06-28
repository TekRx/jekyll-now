---
layout: post
title: Ditch the ACL on the Legacy Crypto VPN
tags: [ crypto, ipsec, security, vpn, tunneling ]
img: /assets/oldwaynewway.png
---

![oldnewway](/assets/vti-oldwaynewway.jpg)

One of our clients had ran into an interesting issue where they were unable to setup multiple GRE tunnels with IPSEC encryption to two different destinations while having the same source interface.

In theory this should work but for some reason the router was able to only encrypt/decrypt traffic on one tunnel.


Although there are multiple options that we can use to resolve this issue, such as DMVPN or Site to Site tunneling, we ended up altering the design slightly to move away from the legacy crypto configuration and to use the next generation crypto configuration to keep things simple.

## Benefits of Virtual Tunnel Interface (VTI) over Legacy Crypto:
- Cleaner configuration
  - The legacy way of configuring crypto requires the use of crypto Access-Lists to define the encryption domain.
- Routable interface
  - The VTI interface is a fully routable interface for both static and dynamic routing (such as OSPF, BGP, and EIGRP)
- Smaller overhead
  - No need for GRE encapsulation to save 4 bytes per packet!
- Supports Unicast/Multicast traffic (Broadcast traffic supported with GRE over IPSEC)

## Comparison between Legacy and VTI:

< insert comparison >

#### Diagram for illustration:

![diagram](/assets/vti-diagram.png)

### Setup:

- 2 Spoke Routers
- 2 Hub Routers (VRF enabled)
- IPSec over GRE

### Crypto Configuration:

##### HUB1 (in VRF)

```
crypto isakmp profile ISAKMP-Profile-Spoke1
   keyring KEYRING-SPOKE1
   match identity address 30.30.30.30 255.255.255.0 YOURVRFHERE
   local-address 10.10.10.10 YOURVRFHERE
crypto isakmp profile ISAKMP-Profile-Spoke2
   keyring KEYRING-SPOKE2
   match identity address 40.40.40.40 255.255.255.0 YOURVRFHERE
   local-address 10.10.10.10 YOURVRFHERE
!
crypto ipsec transform-set TRSET esp-3des esp-md5-hmac
 mode transport
!
crypto ipsec profile IPSEC-Profile-Spoke1
 set transform-set TRSET
 set isakmp-profile ISAKMP-Profile-Spoke1
crypto ipsec profile IPSEC-Profile-Spoke2
 set transform-set TRSET
 set isakmp-profile ISAKMP-Profile-Spoke2
!
!
interface Tunnel1
 description *** VTI to Spoke1 ***
 ip vrf forwarding YOURVRFHERE
 ip address 192.168.10.1 255.255.255.252
 ip mtu 1400
 ip tcp adjust-mss 1360
 tunnel source 10.10.10.10
 tunnel mode ipsec ipv4
 tunnel destination 30.30.30.30
 tunnel vrf YOURVRFHERE
 tunnel protection ipsec profile IPSEC-Profile-Spoke1
!
interface Tunnel2
 description *** VTI to Spoke2 ***
 ip vrf forwarding YOURVRFHERE
 ip address 192.168.30.1 255.255.255.252
 ip mtu 1400
 ip tcp adjust-mss 1360
 tunnel source 10.10.10.10
 tunnel mode ipsec ipv4
 tunnel destination 40.40.40.40
 tunnel vrf YOURVRFHERE
 tunnel protection ipsec profile IPSEC-Profile-Spoke2
 ```

 ##### HUB2 (in VRF)

 ```
 crypto isakmp profile ISAKMP-Profile-Spoke1
   keyring KEYRING-SPOKE1
   match identity address 30.30.30.30 255.255.255.0 YOURVRFHERE
   local-address 20.20.20.20 YOURVRFHERE
crypto isakmp profile ISAKMP-Profile-Spoke2
   keyring KEYRING-SPOKE2
   match identity address 40.40.40.40 255.255.255.0 YOURVRFHERE
   local-address 20.20.20.20 YOURVRFHERE
!
crypto ipsec transform-set TRSET esp-3des esp-md5-hmac
 mode transport
!
crypto ipsec profile IPSEC-Profile-Spoke1
 set transform-set TRSET
 set isakmp-profile ISAKMP-Profile-Spoke1
crypto ipsec profile IPSEC-Profile-Spoke2
 set transform-set TRSET
 set isakmp-profile ISAKMP-Profile-Spoke2
!
!
interface Tunnel1
 description *** VTI to Spoke1 ***
 ip vrf forwarding YOURVRFHERE
 ip address 192.168.20.1 255.255.255.252
 ip mtu 1400
 ip tcp adjust-mss 1360
 tunnel source 20.20.20.20
 tunnel mode ipsec ipv4
 tunnel destination 30.30.30.30
 tunnel vrf YOURVRFHERE
 tunnel protection ipsec profile IPSEC-Profile-Spoke1
!
interface Tunnel2
 description *** VTI to Spoke2 ***
 ip vrf forwarding YOURVRFHERE
 ip address 192.168.40.1 255.255.255.252
 ip mtu 1400
 ip tcp adjust-mss 1360
 tunnel source 20.20.20.20
 tunnel mode ipsec ipv4
 tunnel destination 40.40.40.40
 tunnel vrf YOURVRFHERE
 tunnel protection ipsec profile IPSEC-Profile-Spoke2
 ```

 ##### Spoke1

 ```
 crypto keyring KEYRING-HUB1
  local-address 30.30.30.30
  pre-shared-key address 10.10.10.10 key SECRETKEY
crypto keyring KEYRING-HUB2
  local-address 30.30.30.30
  pre-shared-key address 20.20.20.20 key SECRETKEY
!
crypto isakmp profile ISAKMP-Profile-Hub1
   keyring KEYRING-HUB1
   match identity address 10.10.10.10 255.255.255.0
   local-address 30.30.30.30
crypto isakmp profile ISAKMP-Profile-Hub2
   keyring KEYRING-HUB2
   match identity address 20.20.20.20 255.255.255.0
   local-address 30.30.30.30
!
crypto ipsec transform-set TRSET esp-3des esp-md5-hmac
 mode transport
!
crypto ipsec profile IPSEC-Profile-HUB1
  set transform-set TRSET
  set isakmp-profile ISAKMP-Profile-Hub1
crypto ipsec profile IPSEC-Profile-HUB2
 set transform-set TRSET
 set isakmp-profile ISAKMP-Profile-Hub2
!
interface Tunnel1
 description *** VTI to HUB1 ***
 ip address 192.168.10.2 255.255.255.252
 ip mtu 1400
 ip tcp adjust-mss 1360
 tunnel source 30.30.30.30
 tunnel mode ipsec ipv4
 tunnel destination 10.10.10.10
 tunnel protection ipsec profile IPSEC-Profile-HUB1
!
interface Tunnel2
 description *** VTI to HUB2 ***
 ip address 192.168.20.2 255.255.255.252
 ip mtu 1400
 ip tcp adjust-mss 1360
 tunnel source 30.30.30.30
 tunnel mode ipsec ipv4
 tunnel destination 20.20.20.20
 tunnel protection ipsec profile IPSEC-Profile-HUB2
 ```

 ##### Spoke 2

 ```
 crypto keyring KEYRING-HUB1
  local-address 40.40.40.40
  pre-shared-key address 10.10.10.10 key SECRETKEY
crypto keyring KEYRING-HUB2
  local-address 40.40.40.40
  pre-shared-key address 20.20.20.20 key SECRETKEY
!
crypto isakmp profile ISAKMP-Profile-Hub1
   keyring KEYRING-HUB1
   match identity address 10.10.10.10 255.255.255.0
   local-address 40.40.40.40
crypto isakmp profile ISAKMP-Profile-Hub2
   keyring KEYRING-HUB2
   match identity address 20.20.20.20 255.255.255.0
   local-address 40.40.40.40
!
crypto ipsec transform-set TRSET esp-3des esp-md5-hmac
 mode transport
!
crypto ipsec profile IPSEC-Profile-HUB1
  set transform-set TRSET
  set isakmp-profile ISAKMP-Profile-Hub1
crypto ipsec profile IPSEC-Profile-HUB2
 set transform-set TRSET
 set isakmp-profile ISAKMP-Profile-Hub2
!
interface Tunnel1
 description *** VTI to HUB1 ***
 ip address 192.168.30.2 255.255.255.252
 ip mtu 1400
 ip tcp adjust-mss 1360
 tunnel source 40.40.40.40
 tunnel mode ipsec ipv4
 tunnel destination 10.10.10.10
 tunnel protection ipsec profile IPSEC-Profile-HUB1
!
interface Tunnel2
 description *** VTI to HUB2 ***
 ip address 192.168.40.2 255.255.255.252
 ip mtu 1400
 ip tcp adjust-mss 1360
 tunnel source 40.40.40.40
 tunnel mode ipsec ipv4
 tunnel destination 20.20.20.20
 tunnel protection ipsec profile IPSEC-Profile-HUB2
 ```

### Verify Connectivity:

- show crypto session detail
  - ​You want to see the following:
    - `Session Status: UP-ACTIVE`
    - Inbound & Outbound dec'ed and enc'ed values incrementing

##### Sample Output from Spoke1:

```
SPOKE1#show crypto session detail
Crypto session current status

Code: C - IKE Configuration mode, D - Dead Peer Detection
K - Keepalives, N - NAT-traversal, T - cTCP encapsulation
X - IKE Extended Authentication, F - IKE Fragmentation
R - IKE Auto Reconnect

Interface: Tunnel1
Profile: ISAKMP-Profile-Hub1
Uptime: 1d22h
Session status: UP-ACTIVE
Peer: 10.10.10.10 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 10.10.10.10
      Desc: (none)
  Session ID: 0
  IKEv1 SA: local 30.30.30.30/4500 remote 10.10.10.10/4500 Active
          Capabilities:N connid:1095 lifetime:02:24:55
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 17886 drop 0 life (KB/Sec) 4607978/982
        Outbound: #pkts enc'ed 21558 drop 0 life (KB/Sec) 4607975/982

Interface: Tunnel2
Profile: ISAKMP-Profile-Hub2
Uptime: 1d22h
Session status: UP-ACTIVE
Peer: 20.20.20.20 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 20.20.20.20
      Desc: (none)
  Session ID: 0
  IKEv1 SA: local 30.30.30.30/4500 remote 20.20.20.20/4500 Active
          Capabilities:N connid:1096 lifetime:02:28:24
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 17876 drop 0 life (KB/Sec) 4607982/1559
        Outbound: #pkts enc'ed 17875 drop 0 life (KB/Sec) 4607982/1559
```

And now you're done! This configuration is simple and basic but you can now enable dynamic routing such as OSPF, BGP, EIGRP across the tunnel and have a simpler and optimized router crypto configuration.


If you find any errors or have suggestions to improve this article, please feel free to contact Jon at <blog@tekrx.ca>

---

All applications mentioned in this article are not endorsed by TekRx Solutions and are to be used at your own discretion. TekRx Solutions does not take responsibility for any liability in the use of any application or configuration mentioned in the article.
