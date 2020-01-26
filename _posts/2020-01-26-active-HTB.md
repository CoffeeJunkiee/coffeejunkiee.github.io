---
title:  "Hack The Box / Active"
date: 2020-01-26
tags: [Penetration Testing, Hacking, Hack The Box, Jeeves, OSCP, Offensive Security]
header: 
  image: "assets/images/active/active-header.png"
---
Active is our fourth machine in the OSCP list provided by [NetSec Focus](https://www.netsecfocus.com/)! This machine was a great learning experience where SMB enumeration and some knowledge about [kerberos](https://en.wikipedia.org/wiki/Kerberos_(protocol))  were essential in order to root this machine. This is the fourth blog before my third attempt to the OSCP exam, so let's get to it!

## Information Gathering

### Nmap Scan
So, we start with a nmap scan to check what ports are open and what services are running in this machine. 

```
nmap -sC -sV -p- -oN scan.nmap 10.10.10.59
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/active/nmap.png" alt="nmap scan">

Do not panic! That sounds like a lot of enumeration to do, and it is, but remember some of the important services in this nmap scan, which means that the important services for this machine are SMB, kerberos, and HTTP. But after doing some discovering and extense enumeration the services that will give a straight path to root this machine are SMB and kerberos.

### SMB Enumeration
There are different tools to make SMB enumeration, you can use [enum4linux](https://highon.coffee/blog/enum4linux-cheat-sheet/), [nmap-scripts](https://nmap.org/nsedoc/scripts/smb-enum-users.html), but my preference is [smbmap](https://tools.kali.org/information-gathering/smbmap) due to its acurate results. 

```
smbmap -H 10.10.10.100
```
where:
- ```-H``` specifies the host to scan. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/active/smbmap.png" alt="nmap scan">
