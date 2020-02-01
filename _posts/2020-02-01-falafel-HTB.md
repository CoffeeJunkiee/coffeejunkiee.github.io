---
title:  "Hack The Box / Falafel"
date: 2020-02-01
tags: [Penetration Testing, Hacking, Hack The Box, Jeeves, OSCP, Offensive Security]
header: 
  image: "assets/images/falafel/falafel-header.png"
---
Falafel is our sixth machine in the OSCP list provided by [NetSec Focus](https://www.netsecfocus.com/)! This machine was quite exotic due to its vulnerabilities and privilege escalation. Definetely some advance knowledge about php, MySQl, and linux was required, but the most of the learning came from [ipssec's](https://www.youtube.com/watch?v=CUbWpteTfio&t=) video about solving this machine. I have to say that there were easier ways to do it, but I rather to make it manually to learn how some technologies work. This is the sixth blog before my third attempt to the OSCP exam, so let's get to it!

## Information Gathering

### Nmap Scan
So, we start with a nmap scan to check what ports are open and what services are running in this machine. 

```
nmap -sC -sV -p- -oN scan.nmap 10.10.10.73
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/nmap.png" alt="nmap scan">

Here we have two useful ports and services such as SSH running in port 22 and HTTP running in port 80. There is some information in port 80 from ```robots.txt``` where it disallows all entries with a ```.txt``` extension. So, we might want to specify the extension at the time to do directory brute force.

### Directory Brute Force
This is what we have in our browser for now.

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/browser-capure.png" alt="nmap scan">

Running [dirsearch](https://github.com/maurosoria/dirsearch) we have some results!
```
./dirsearch.py -u http://10.10.10.73 -w /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt -f -e txt
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/dirsearch.png" alt="nmap scan">

Let's see what we find at ```http://10.10.10.73/cyberlaw.txt```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/cyberlaw.png" alt="nmap scan">

Great, seems that some of the vector attack is related with the upload feature, here there is a username which is chris, so let's find more usernames. 

### Username Brute Force with Wfuzz
[Wfuzz](https://github.com/xmendez/wfuzz) is a pretty handy tool that can be helpful for a variety of things. In this case let's use it to do some username brute force.
```
wfuzz -c -w /usr/share/seclists/Usernames/Names/names.txt -d "username=FUZZ&password=abcd" -u http://10.10.10.73/login.php --hh 7074
```
Where:
- ```-w``` specifies the wordlist.
- ```-d``` the parameter to brute force
- ```-u``` URL of the target
- ```-hh``` hide results with code 7074

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/wfuzz.png" alt="nmap scan">
