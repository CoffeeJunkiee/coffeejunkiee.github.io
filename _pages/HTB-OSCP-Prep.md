---
title:  "HTB-OSCP Prep"
permalink: /HTB-OSCP-Prep/
header:
  image: "/assets/images/oscp-prep/header.jpg"

---

OSCP is one of the most wanted and demanded certification related to Offensive Security industry. The preparation, content, and exam contains a bast amount of time and information to study and comprehend, but still one of the basic knowledge learned during the cert due to the fast advance of offensive security. The mindset earned is priceless because it introduces you to the persistence, and mantra "Try Harder", which is something that drives people to the success and to overcome frustation during the OSCP journey. 

In this site, there are eleven posts related to [Hack The Box](https://www.hackthebox.eu/) and some machines that are similar to the OSCP lab. The posts published here had the purpose to practice, learn, and understand some of the basic methodology related to penetration testing, so if you're looking for some practice and preparation, you might find this useful. 

## Preparation and Final Goal

The preparation was to achieve 10 machines that were provided by [NetSec Focus](https://www.netsecfocus.com/) in order to obtain practice prior my third attempt to the OSCP exam. I have to say that before taking the OSCP I have had prior experience with several penetration testing and programming courses as also practice in platforms such as [Vuln Hub](https://www.vulnhub.com/) and [Hack The Box](https://www.hackthebox.eu/). After going through the OSCP learning material, and failing the OSCP exam twice, I decided to change my way of practice by learning and explaining machines from Hack The Box in this website. The final goal was achieved which was to pass the OSCP exam and earn the OSCP certification. 

### Write Ups OSCP Like

In the following content I will explain the eleven machines that were published in this website in order to earn the OSCP certification. 

**Note:** All the machines showed here were write ups from retired machines from [Hack The Box](https://www.hackthebox.eu/)

1. [Jeeves:](https://coffeejunkie.me/jeeves-HTB/)
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/oscp-prep/jeeves.png" alt="picture">
Jeeves is an interesting and fun box to root due to the command script located in the web server and different ways to escalate privileges without the need of metasploit. If you're new with [Rotten or Juicy Potato](https://github.com/ohpe/juicy-potato) this box might be a good place to start. 

2. [Bart:](https://coffeejunkie.me/bart-HTB/)
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/oscp-prep/bart.png" alt="picture">
If you're curious about word list creation and bruteforcing, [Bart](https://coffeejunkie.me/bart-HTB/) is the perfect box to root. In this box, the skills related to bruteforcing are going to be shape up once you try it! And the usage of different tools such as [Burpsuite](https://portswigger.net/burp) and [Hydra](https://tools.kali.org/password-attacks/hydra) are going to be essential here. The privilege escalation cover curious misconfigurations with Autologon credentials. 

3. [Tally:](https://coffeejunkie.me/tally-HTB/)
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/oscp-prep/tally.png" alt="picture">
If you struggle with enumeration for a certain technology, Tally will make you Google all the things! Its SharePoint Web technology will make you see how you can find different credentials in documents. Enumeration is key with this fun to learn box. Also, if you wanna try some windows enumeration this box can be a good candidate.

4. [Active:](https://coffeejunkie.me/active-HTB/)
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/oscp-prep/active.png" alt="picture">

Windows enumeration and some cryptography are the cool things to see in this box! Also, get familia with some of the [impacket](https://github.com/SecureAuthCorp/impacket) tools if you want to root this box. 

5. [Jail:](https://coffeejunkie.me/jail-HTB/)
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/oscp-prep/jail.png" alt="picture">
Jail is a pretty long box, but amazing to learn some of buffer overflow, and how to work with the debugger. The exploitation and privilege escalation is crucial due tot the importance of permissions with shares given by nfs services. Creativity and enumeraion is key for the privilege escalation. If you want some kind of new learning and challenge, this box is for you. 

6. [Falafel:](https://coffeejunkie.me/falafel-HTB/)
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/oscp-prep/falafel.png" alt="picture">
The usage of [Burpsuite](https://portswigger.net/burp) and a clear enumeration is going to be useful for this machine. Also, you're going to see how some credentials are stored in clear text in different databases, besides this, the privilege escalation is quite interesting due to the user groups. Creativity and research are required for this box. 

7. [DevOops:](https://coffeejunkie.me/DevOops-HTB/)
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/oscp-prep/devops.png" alt="picture">
If you're trying to get familiar with [OWASP TOP 10](https://sucuri.net/guides/owasp-top-10-security-vulnerabilities-2020/) this box is for you due to the way to obtain information in order to get a reverse shell. Also, if you wanna get familiar with [git](https://confluence.atlassian.com/bitbucketserver/basic-git-commands-776639767.html) and its commands this box will be a good candidate for you. The privilege escalation will be very well related to git so check it out!

8. [Hawk:](https://coffeejunkie.me/hawk-HTB/)
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/oscp-prep/hawk.png" alt="picture">
This box contains information that will be fun to learn such as how to crack SSL Salted Passwords. It's enumeration and research process about the technologies that this box uses is quite useful. It's privilege escalation will be related to different misconfiguration and passwords found in files within the machine. 

9. [Netmon:](https://coffeejunkie.me/netmon-HTB/)
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/oscp-prep/netmon.png" alt="picture">
Enumeration related to a certain technology used in this box is going to be crucial at the time to get the attack vector. Besides the usage of [impacket](https://github.com/SecureAuthCorp/impacket) is pretty handy in order to get a reverse shell. If you wanna check network monitors that are related with windows systems, this box is a good candidate for you. 

10. [Lightweight](https://coffeejunkie.me/lightweight-HTB/)
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/oscp-prep/light.png" alt="picture">
Enumeration of different services, nmap scripts, and the usage of [tcpdump](https://www.tcpdump.org/) are very helpful in this machine. Privilege escalation was a new learning path to check. 

11. [La Casa De Papel:](https://coffeejunkie.me/lacasadepapel-HTB/)
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/oscp-prep/casa.png" alt="picture">
Web application testing will be something good to practice with this machine. Things such as path traversal, and ssl certificates are important here as well. Privilege escalation is related to some script that you can manipulate. This machines is "straight forward" as long the enumeration is well made. 

- Final Goal -- [OSCP exam overview](https://coffeejunkie.me/OSCP-Exam-Overview/)

Here there are different kind of things that helped me to go through the exam. The hole practice helped me out to prepare and succeed during the OSCP journey. 

## Thanks

All the readers and people who encourage me during the journey.
