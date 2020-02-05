---
title:  "Hack The Box / DevOops"
date: 2020-02-04
tags: [Penetration Testing, Hacking, Hack The Box, Jeeves, OSCP, Offensive Security]
header: 
  image: "assets/images/devops/devops-header.png"
---
DevOops is our seventh machine in the OSCP list provided by [NetSec Focus](https://www.netsecfocus.com/)! This machine was something new to me due to its [XXE](https://owasp.org/www-community/vulnerabilities/XML_External_Entity_(XXE)_Processing)  vulnerability and its privileges escalation using certain commands from [git](https://git-scm.com/book/en/v2/Getting-Started-The-Command-Line). There were many ways to do it where the usage of different language scripts could have been a good tool, but this time I decided to use BurpSuite as a must use tool. This is the seventh blog before my third attempt to the OSCP exam, so let's get to it!

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/devops/list.jpg" alt="nmap scan">

## Information Gathering


### Nmap Scan
So, we start with a nmap scan to check what ports are open and what services are running in this machine. 

```
nmap -sC -sV -p- -oN scan.nmap 10.10.10.91
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/devops/nmap.png" alt="nmap scan">

So, we see that the port 5000 is running as a HTTP service, which means that we might visit with our browser and check what we can find!

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/devops/browser.png" alt="nmap scan">

Ok, it seems that it still under construction. Some directory brute force doesn't seem a bad idea. 

### Directory Brute Force

Running [dirsearch](https://github.com/maurosoria/dirsearch) we have some results!
```
./dirsearch.py -u http://10.10.10.91:5000 -w /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt -f -e txt
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/devops/dirsearch.png" alt="nmap scan">

Great! Looks like we have some good chances, so going to ```/feed``` there is nothing else that is shown in the index page, but the interesting part comes when we see ```/upload```.

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/devops/upload.png" alt="nmap scan">

As the name says, we can upload a new file, it could txt or XML, but look at the elements that is asking for which are the ___Author, Subject, and Content__, so this is the actual code that we used to test the upload feauture. Check that it has the elements that the website is asking. 
~~~ xml
<payload>
  <Author>Coffee</Author>
  <Subject>Junkie</Subject>
  <Content>Devoops</Content>
</payload> 
~~~