---
title:  "Hack The Box / Netmon"
date: 2020-02-12
tags: [Penetration Testing, Hacking, Hack The Box, Jeeves, OSCP, Offensive Security]
header: 
  image: "assets/images/netmon/netmon-header.png"
---
Hawk is our ninth machine in the OSCP list provided by [NetSec Focus](https://www.netsecfocus.com/)! This machine was fairly easy, but interesting. The exploitation in the web technology was quite interesting due to its exploit that requires RCE and it creates a user with administrative privileges! The learning acquired from here shows some common exposed files in protocols such as FTP, and unpatched technologies. This is the ninth blog before my third attempt to the OSCP exam, so let's get to it!

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/netmon/list.jpg" alt="nmap scan">

## Information Gathering


### Nmap Scan
So, we start with a nmap scan to check what ports are open and what services are running in this machine.
```
nmap -sC -sV -p- -oN scan.nmap 10.10.10.152
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/netmon/nmap.png" alt="nmap scan">

Great, here in the scan we can see couple common ports such as FTP, HTTP and SMB services, there are also other ports, but this time I enumerate the most common services which gave us results to own this machine. 

