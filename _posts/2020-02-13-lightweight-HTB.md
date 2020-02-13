---
title:  "Hack The Box / Lightweight"
date: 2020-02-13
tags: [Penetration Testing, Hacking, Hack The Box, Jeeves, OSCP, Offensive Security]
header: 
  image: "assets/images/light/light-header.png"
---
Lightweight is our tenth machine in the OSCP list provided by [NetSec Focus](https://www.netsecfocus.com/)! Very interesting machine due to the enumeration given through [LDAP](https://en.wikipedia.org/wiki/Lightweight_Directory_Access_Protocol), its HTTP services, and the privilege escalation with capabilities. This is the tenth blog before my third attempt to the OSCP exam, so let's get to it!

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/list.jpg" alt="nmap scan">

## Information Gathering


### Nmap Scan
So, we start with a nmap scan to check what ports are open and what services are running in this machine.
```
nmap -sC -sV -p- -oN scan.nmap 10.10.10.119
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/nmap.png" alt="nmap scan">

There are couple services running such as SSH, HTTP, and LDAP. Sounds like the enumeration is going to be quiet interesting this time. 

