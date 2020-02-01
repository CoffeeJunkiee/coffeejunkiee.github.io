---
title:  "Hack The Box / Falafel"
date: 2020-02-01
tags: [Penetration Testing, Hacking, Hack The Box, Jeeves, OSCP, Offensive Security]
header: 
  image: "assets/images/falafel/falafel-header.png"
---
Falafel is our sixth machine in the OSCP list provided by [NetSec Focus](https://www.netsecfocus.com/)! This machine was quite exotic due to its vulnerabilities and privilege escalation. Definetely some advance knowledge about php, MySQl, and linux was required, but the most of the learning came from [ipssec's](https://www.youtube.com/watch?v=CUbWpteTfio&t=) video about solving this machine. I have to say that there were easier ways to do it, but I rather to make it manually to learn how some technologies work. This is the sixth blog before my third attempt to the OSCP exam, so let's get to it!

### Nmap Scan
So, we start with a nmap scan to check what ports are open and what services are running in this machine. 

```
nmap -sC -sV -p- -oN scan.nmap 10.10.10.73
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/nmap.png" alt="nmap scan">