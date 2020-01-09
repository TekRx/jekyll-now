---
layout: post
title: Mount an NFS mount through Palo Alto
tags: [ isilon, storage, nfs, vulnerability ]
excerpt_separator: <!--more-->
---

Mounting NFS through Palo Alto, permitted traffic but doesn't mount?

Ports required to be opened:
- Portmapper
- RPC
- Mount
- NFS

This is what the policy should look like:
![NFS Mount Policy](/assets/paloalto/)

<!--more-->

One odd issue that I had ran into was that portmapper requests were considered to be a "threat" by Palo Alto based on the signature inspection.

To see if this traffic is being filtered, go to `Monitor` > `Logs` > `Threat`



Hopefully this has helped with mounting NFS shares across the Palo Alto

If you find any errors or have suggestions to improve this article, please feel free to contact Jon at <blog@tekrx.ca>

---

_All applications mentioned in this article are not endorsed by TekRx Solutions and are to be used at your own discretion. TekRx Solutions does not take responsibility for any liability in the use of any application mentioned in the article._
