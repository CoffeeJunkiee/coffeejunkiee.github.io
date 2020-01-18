---
title:  "Hack The Box || Jeeves"
date: 2020-01-18
tags: [Penetration Testing, Hacking, Hack The Box, Jeeves, OSCP, Offensive Security]
header: 
  image: "assets/images/jeeves/jeeves-rooted.jpg"
---

Welcome! So, before starting with couple ways of getting this box, I want to explain the goal of this and the following posts. After two attempts to pass my OSCP exam (which both attemps failed) I looked the need to practice and explain some of the learning obtained with different machines in Hack The Box, so I decided to make some challenging boxes before my third attempt to the OSCP exam. I chose this list from [NetSec Focus](https://www.netsecfocus.com/) in order to improve my penetration testing skills. From this list Jeeves is the first box, so let's get to it. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/list.jpg" alt="oscp list">

## Information Gathering

### Nmap Scan
So we start with a nmap scan to check what ports are open and what services are running in this machine. 

```
nmap -sC -sV -p- -oN scan.nmap 10.10.10.63
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/nmap-scan.png" alt="nmap scan">
