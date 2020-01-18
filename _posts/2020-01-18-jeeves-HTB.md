---
title:  "Hack The Box / Jeeves"
date: 2020-01-18
tags: [Penetration Testing, Hacking, Hack The Box, Jeeves, OSCP, Offensive Security]
header: 
  image: "assets/images/jeeves/jeeves-rooted.jpg"
---

Welcome! So, before starting with couple ways of getting this box, I want to explain the goal of this and the following posts. After two attempts to pass my OSCP exam (which both attemps failed) I looked the need to practice and explain some of the learning obtained with different machines in Hack The Box, so I decided to make some challenging boxes before my third attempt to the OSCP exam. I chose this list from [NetSec Focus](https://www.netsecfocus.com/) in order to improve my penetration testing skills. From this list Jeeves is the first box, so let's get to it. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/list.jpg" alt="oscp list">

## Information Gathering

### Nmap Scan
So, we start with a nmap scan to check what ports are open and what services are running in this machine. 

```
nmap -sC -sV -p- -oN scan.nmap 10.10.10.63
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/nmap-scan.png" alt="nmap scan">

Looks like we have some HTTP services running in port 80 and 50000. Also we can deduce that the machine is running Windows due to the IIS server. There is a good chance to scan the port 445 which is running a SMB service, but unfortunately the results are not helpful. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/nmap-scripts.png" alt="smb scan">

Then, having HTTP service running in port 80 and 50000 and running [dirsearch](https://github.com/maurosoria/dirsearch) in both ports, we get some good results.

Results from browser and dirsearch running in port 80. 
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/browser-80.png" alt="browser">
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/dir-80.png" alt="dirsearch">