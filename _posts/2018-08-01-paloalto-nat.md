---
layout: post
title: Palo Alto NAT configuration
tags: [ pan, nat, configuration, paloalto ]
excerpt_separator: <!--more-->
---

5 Step configuration of a NAT on Palo Alto

Covers:
- Default/Dynamic NAT (Inside > Outside) - [Many to One]
- Inbound NAT (Outside > Inside) - [One to One]
- Bidirectional NAT (Outside > Inside or Inside > Outside) - [One to One]

![NAT Summary Diagram](/assets/paloalto/nat/natsummary.jpg)

<!--more-->

IMPORTANT: Please remember to add approrpiate policies to permit traffic between Zones. Example for Inbound NAT: Allow Untrust Zone to have acess to Trust Zone from Any IP to Specfic Server IP address and any associated applications/ports.

## Default/Dynamic NAT
This nat configuration will NAT the entire LAN network [_192.168.1.10/24_] to a singular IP address [_20.20.20.20_] when going out to the Internet.

![NAT Default/Dynamic](/assets/paloalto/nat/defaultnat.jpg)

1. Navigate to `Policies` > `NAT` then click the `+ADD` button at the bottom
![NAT Page](/assets/paloalto/nat/defaultnat0.jpg)
2. Enter Name of Default NAT Policy
![General](/assets/paloalto/nat/defaultnat1.jpg)
3. Fill in Source Zone, Destination Zone, Destination Interface, Source Addresses
    1. *Source Zone: Zone* which traffic will originate
    2. *Destination Zone*: Zone which traffic will go to
    3. *Destination Interface*: Interface which traffic will go to
    4. *Source Address*: IP or Subnet that of which initiates the traffic
![Original Packet](/assets/paloalto/nat/defaultnat2.jpg)
4. Under Source Address Translation, select in the dropdown `Dynamic IP and Port` and add the desired IP address
![Translated Packet](/assets/paloalto/nat/defaultnat3.jpg)
5. Default NAT policy completed
![Finished Default NAT](/assets/paloalto/nat/defaultnat4.jpg)

## Inbound NAT

This NAT configuration allows users from the Internet to hit a public IP address [_30.30.30.30_] and the traffic would be routed to your desired server[_192.168.1.10_]. An example of this would be hosting a webpage in your network and allowing clients from the internet to connect to it.

![NAT Default/Dynamic](/assets/paloalto/nat/inboundnat.jpg)

1. Navigate to `Policies` > `NAT` then click the `+ADD` button at the bottom
![NAT Page](/assets/paloalto/nat/defaultnat0.jpg)
2. Enter Name of Inbound NAT Policy
![General](/assets/paloalto/nat/inboundnat1.jpg)
3. Fill in Source Zone, Destination Zone, Destination Interface, Destination Addresses.
_Note: This is a simple example of two zones. If you are exposing a device to the Internet, it would be best security practice to keep it isolated in an alternate zone such as a DMZ._
![Original Packet](/assets/paloalto/nat/inboundnat2.jpg)
4. Check the box `Destination Address Translation` and in the drop down, select the destination server. In this case, I want users from the Internet to be able to access "my internal server"
![Translated Packet](/assets/paloalto/nat/inboundnat3.jpg)
5. Inbound NAT policy completed
![Finished Default NAT](/assets/paloalto/nat/inboundnat4.jpg)

## Bi-directional NAT

This NAT configuration allows access to an internal server [_192.168.1.10_] to a designated Internet IP [_40.40.40.40_] and in the same way, the traffic going out to the Internet from that internal server, appears as the same designated Internet IP.

![NAT Default/Dynamic](/assets/paloalto/nat/bidirnat.jpg)

1. Navigate to `Policies` > `NAT` then click the `+ADD` button at the bottom
![NAT Page](/assets/paloalto/nat/defaultnat0.jpg)
2. Enter Name of Bidirectional NAT Policy
![General](/assets/paloalto/nat/bidirnat1.jpg)
3. Fill in Source Zone, Destination Zone, Destination Interface, Source Addresses
![Original Packet](/assets/paloalto/nat/bidirnat2.jpg)
4. Under Source Address Translation, select in the dropdown `Static` input the desired Static Public IP address, and ensure that you check the `Bi-directional` box
![Translated Packet](/assets/paloalto/nat/bidirnat3.jpg)
5. Bidirectional NAT policy completed
![Finished Default NAT](/assets/paloalto/nat/bidirnat4.jpg)



Hopefully this has helped you understand NAT on the Palo Alto better.

If you find any errors or have suggestions to improve this article, please feel free to contact Jon at <blog@tekrx.ca>

---

_All applications mentioned in this article are not endorsed by TekRx Solutions and are to be used at your own discretion. TekRx Solutions does not take responsibility for any liability in the use of any application mentioned in the article._
