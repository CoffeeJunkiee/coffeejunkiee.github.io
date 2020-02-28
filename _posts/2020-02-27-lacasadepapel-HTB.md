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

Visiting the website running in port 80, there is this website related to La Casa de Papel show on Netflix.

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/casa/browser.png" alt="nmap2 scan">

Curiously, there is this some kind of OTP token in order to get authenticated with it, but after searching around it was not very helpful. 

### Exploiting  vsftpd 2.3.4

 vsftpd 2.3.4 is a FTP server running in the target machine, this FTP server does not allow Anonymous login, but there are a couple of exploits that can be helpful. 
```
searchsploit vsftpd 2.3.4
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/casa/searchsploit.png" alt="nmap2 scan">

According ```searchsploit``` metasploit has a module related to this FTP server and its vulnerability, but in the OSCP exam, the usage of metasploit has certain limitations, so in this case I found more information related to the server and its vulnerability in this [website](https://metalkey.github.io/vsftpd-v234-backdoor-command-execution.html)
