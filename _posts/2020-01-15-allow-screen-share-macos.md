---
layout: post
title: Allow Screen Share on MacOS
tags: [ macos, screenshare, mojave, catalina ]
excerpt_separator: <!--more-->
---

To allow Screen Sharing for you or others to connect to your mac remotely, you can follow the steps below to allow access. This process is valid for Mojave and Catalina.

## Steps to enable screen sharing

1. Open Spotlight and type in `System Preferences` or top left click the `Apple icon` and go to `System Preferences`
  1. ![System Preferences](/assets/2020-01-15-ScreenShare-1.png)
2. Click `Sharing`
  1. ![Sharing](/assets/2020-01-15-ScreenShare-2.png)
3. Check the box beside `Screen Sharing`
  1. ![Check Screen Sharing](/assets/2020-01-15-ScreenShare-3.png)
4. Done!

<!--more-->

## How to connect from remote machine

Assuming you're on the same network, or if you already have port forwarding setup (port 5900) you can connect in two ways.

### Method 1 - Screen Share
1. Open Spotlight and type in `Screen Sharing`
2. Connect to: your `IP address` of the machine you had just enabled Screen Sharing on.
  1. IP address of the **Destination machine**
  2. Go to the **Destination machine**
  3. Open Spotlight and type in `Terminal`
  4. Type in `ifconfig`
  5. Note the numbers beside **inet** `192.168.1.123`

  ```bash
  en0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
	options=b<RXCSUM,TXCSUM,VLAN_HWTAGGING>
	ether e0:d5:5e:a5:bd:06
	inet6 fe80::ff:5a5:23e:3168%en0 prefixlen 64 secured scopeid 0x5
	inet 192.168.1.123 netmask 0xffffff00 broadcast 192.168.1.255
	nd6 options=201<PERFORMNUD,DAD>
	media: autoselect (1000baseT <full-duplex>)
	status: active
  ```
3. Login with the credentials of the **Destination Machine**

### Method 2 - Command Line
You can pre-program the computer login information into the line itself if you wish, or just open screen share to be prompted with the credentials to login to the **Destination Machine**

1. Open `Terminal`
  1. Type in `open vnc://<ip address of destination machine>`
  2. Example: `open vnc://192.168.1.123`
2. Open `Terminal`
  1. Type in `open vnc://"username":"password"@<ip address of destination machine>`
  2. Example: `open vnc://"my username":"my password"@192.168.1.123"`
  3. Note: with the `" "`, allows special characters in your username and password.

#### Advanced Port Forwarding
If you are connecting to a public ip address and have setup multiple Machines, you can configure port forward from `5901` to `5900`

**Example:**

`<public_ip>:5901` to `<internal_ip_of_destination_machine>:5900`

`open vnc://"my username":"my password"@80.80.80.80:5901"`


If you find any errors or have suggestions to improve this article, please feel free to contact Jon at <blog@tekrx.ca>

---

_All applications mentioned in this article are not endorsed by TekRx Solutions and are to be used at your own discretion. TekRx Solutions does not take responsibility for any liability in the use of any application mentioned in the article._
