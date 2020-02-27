---
title:  "Hack The Box / La Casa de Papel"
date: 2020-02-13
tags: [Penetration Testing, Hacking, Hack The Box, Jeeves, OSCP, Offensive Security]
header: 
  image: "assets/images/casa/casa-header.png"
---
La Casa de Papel is our last machine in the OSCP list provided by [NetSec Focus](https://www.netsecfocus.com/)! Very interesting machine due to the enumeration and usage of different SSL certificates. Also, the foothold for the intrusion is very nice!. This is the last blog after my third attempt to the OSCP exam, I'm gonna publish my exam result email even if I failed it, and try to analize what I did and how I can improve it. The exam will be the end of this OSCP series, it will be published in the following 10 days. Let's get to La Casa de Papel!

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/casa/list.jpg" alt="nmap scan">

## Information Gathering


### Nmap Scan
So, we start with a nmap scan to check what ports are open and what services are running in this machine.
```
nmap -sC -sV -p- -oN scan.nmap 10.10.10.131
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/casa/nmap.png" alt="nmap scan">
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/casa/nmap.png" alt="nmap2 scan">

There are couple services running such as SSH, HTTP. Sounds like the enumeration is going to be quiet interesting this time due to the SSL enumeration. 

