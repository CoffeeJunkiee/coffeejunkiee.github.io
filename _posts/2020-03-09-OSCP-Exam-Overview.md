---
title:  "OSCP Exam Overview"
date: 2020-03-9
tags: [Penetration Testing, Hacking, Hack The Box, Jeeves, OSCP, Offensive Security]
header: 
  image: "assets/images/oscp-exam/oscp-exam.jpg"
---

After going through the ten "hard bug good practice" machines recommended by [NetSec Focus](https://www.netsecfocus.com/), I decided to put countless hours behind the screen and practice things such as information gathering (professional googling), exploitation, privilege escalation, and documentation. The practice, successes, failures, and persistence gave good results due to I was able to earn my OSCP certification after failing the exam twice. This post will be related to things to look for if you decide to go for the exam, and giving a brief overview about the experience. This is the list provided by [NetSec Focus](https://www.netsecfocus.com/).

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/oscp-exam/list.jpg" alt="nmap scan">

## Beginning of the exam.

After Offensive Security provided me all the instructions related to the exam, the usage of tools to automate some of the information gathering was essential. The tool [AutoRecon](https://github.com/Tib3rius/AutoRecon?utm_source=share&utm_medium=ios_app&utm_name=iossmf) was very useful because it provided specific results for tools recon tools, the good part is that it saves all the results and commands in a file so you can paste them in your report after finishing the exam. Most of the time spent during the exam was enumerating the machines in order to find a good attack vector.

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/oscp-exam/recon.gif" alt="nmap scan">
