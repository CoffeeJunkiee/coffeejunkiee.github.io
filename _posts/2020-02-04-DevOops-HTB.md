---
title:  "Hack The Box / DevOops"
date: 2020-02-04
tags: [Penetration Testing, Hacking, Hack The Box, Jeeves, OSCP, Offensive Security]
header: 
  image: "assets/images/devops/devops-header.png"
---
DevOops is our seventh machine in the OSCP list provided by [NetSec Focus](https://www.netsecfocus.com/)! This machine was something new to me due to its [XXE](https://owasp.org/www-community/vulnerabilities/XML_External_Entity_(XXE)_Processing)vulnerability and its privileges escalation using certain commands from [git](https://git-scm.com/book/en/v2/Getting-Started-The-Command-Line). There were many ways to do it where the usage of different language scripts could have been a good tool, but this time I decided to use BurpSuite as a must use tool. This is the seventh blog before my third attempt to the OSCP exam, so let's get to it!

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/devops/list.jpg" alt="nmap scan">