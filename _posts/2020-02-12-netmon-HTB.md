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

### FTP enumeration & User Flag

According the results from nmap, we are allowed to login in FTP as anonymous users. At first, there was not something very interesting, but there was the user flag located in ```/Public```

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/netmon/ftp-user.png" alt="nmap scan">

Once we transfer the file to our localhost through ftp, we can read the flag! So, the user has been owned. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/netmon/user-flag.png" alt="nmap scan">

### FTP enumeration & Obtaining Credentials

So, after visiting with our browser there was a login entry to [PRTG](https://www.paessler.com/prtg) which is a Network Monitor, but to get access we need it credentials, and to exploit this technology, we need to have credentials and got loged in. So, looking around in FTP I could not find anything interesting about credentials, then I tried to google to see where the credentials can be found, and fortunately I found this [website](https://kb.paessler.com/en/topic/463-how-and-where-does-prtg-store-its-data)! The website explains that the credentials or backups are located in ```\programdata\paessler```. So, having access to FTP, let's find it!

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/netmon/backup.png" alt="nmap scan">

Nice, so after transfering the files with the backup information and reading it, there is information about the user and password. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/netmon/pass-found.png" alt="nmap scan">

According to this file, the user is ```prtgadmin``` and the password is ```PrTg@dmin2018```, but with this password we can't login, so remember that this is a backup and the password might have changed. So, the idea was to change the password to ```PrTg@dmin2019```, and effectively, we loged in!

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/netmon/loged-in.png" alt="nmap scan">

