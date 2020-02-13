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

### Enumerating LDAP

There are different techniques to enumerate this service where one of them was using different nmap scripts that are going to look for a service and enumerate it according the scripts given, or the usage of [ldapsearch](https://linux.die.net/man/1/ldapsearch) which is also good and clear. This time we are going to enumerate LDAP with nmap scripts.
```
nmap -p 389 10.10.10.119 --script=ldap-brute.nse,ldap-novell-getpass.nse,ldap-rootdse.nse,ldap-search.nse
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/nmap1.png" alt="nmap scan">
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/nmap2.png" alt="nmap scan">

Ok, according the results given by nmap, there are different things to look, such as the users which are ```ldapuser1 and ldapuser2```, and there are some hashes, but unfortunately we are not able to crack them.

### Directory Brute Force

After enumerating LDAP, it's time to see what is to find in HTTP, but trying [gobuster](https://github.com/OJ/gobuster) didn't give many results because the machine was protected against multiple requests from the same IP, which means that we got banned multiple times because of trying to do directory brute force. In spite of the protection of the website, we could get some results that were good enough to continue with the process. 
```
gobuster dir -u http://10.10.10.119/ -w /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt -t 5 -x .php, .txt, .py
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/godir.png" alt="nmap scan">
