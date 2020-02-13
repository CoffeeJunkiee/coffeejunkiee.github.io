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

After enumerating LDAP, it's time to see what is to find in HTTP, but trying [gobuster](https://github.com/OJ/gobuster) didn't give many results because the machine was protected against brute force, which means that we got banned multiple times because of trying to do directory brute force. In spite of the protection of the website, we could get some results that were good enough to continue with the process. 
```
gobuster dir -u http://10.10.10.119/ -w /usr/share/dirbuster/wordlists/directory-list-lowercase-2.3-medium.txt -t 5 -x .php, .txt, .py
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/godir.png" alt="nmap scan">

After checking with the browser the content from the webpage this is what we found. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/browser.png" alt="nmap scan">

And, after going to ```user.php```, we found information saying that we can login through SSH using our IP as a username and password, great!

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/brow-info.png" alt="nmap scan">

### SSH Login & Privileges Escalation

Effectively, the login was successful providing credentials as our IP as username and password.

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/kinda.png" alt="nmap scan">

So, at the time to escalate privileges, the usage of [LinEnum](https://github.com/rebootuser/LinEnum) was crucial and useful because we found some capabilities that we could execute with our user. The capability was related with usage of [tcpdump](https://www.tcpdump.org/) which means that we can capture traffic in the target machine. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/privesc.png" alt="nmap scan">

To escalate privileges, we execute tcpdump, wait some time while it captures some traffic packets, and once the packets have been captured, it's time to analyze the traffic in our localhost with [wireshark](https://www.wireshark.org/). This is the command to capture the traffic within the target machine.

```
tcpdump -i any -w captured.pcap 
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/tcpdump.png" alt="nmap scan">


Once the traffic has been captured, we can transfer the file through [SCP](https://linuxize.com/post/how-to-use-scp-command-to-securely-transfer-files/) and analyze it with wireshark.

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/wireshark.png" alt="nmap scan">

Great, we found credentials in LDAP protocol. So, ```user:ldapuser2``` and ```password:8bc8251332abe1d7f105d3e53ad39ac2```

#### User Flag 

After obtaining the credentials, it's time to log in and get the user flag!

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/user-flag.png" alt="nmap scan">

The user has been owned. 

### Privilege Escalation To ldapuser1

In the home directory from user ```ldapuser2``` there is a file called ```backup.7z```, interesting name, right? So, we can transfer this file through base64.

- From target machine: ``` base64 backup.7z ```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/base.png" alt="nmap scan">
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/base2.png" alt="nmap scan">

Copy the result in the localhost, and name it ```backup.7z.b64```, and decode it. After decoding it it's time to crack the password because we are required to use a password to read the file.

#### Cracking backup.7z

To crack backup.7z there is a tool-kit from John that can extract the hash from a 7z file and cracked. 

```
 perl 7z2john.pl backup.7z  > backup
 john backup -w=/usr/share/wordlists/rockyou.txt
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/7z-crack.png" alt="nmap scan">

and we got the password which is ```delete```.

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/decom.png" alt="nmap scan">

Once we extract the files from backup.7z we see a file called ```status.php``` which contains the passwords to get ```ldapuser1```. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/status-password.png" alt="nmap scan">

Great, so we just ```su ldapuser2``` using the password found which is ```f3ca9d298a553da117442deeb6fa932d```.

### Privileges Escalation to Root

Using LinEnum we found other capability which was related to openssl, and there was a openssl binary in the home folder from ```ldapuser1``` which means that we might read, and write files through openssl. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/priv-esc.png" alt="nmap scan">

Now, having this flaw, we can extract the file ```/etc/shadow``` from root
```
./openssl aes-256-cbc -d -a -in shadow.enc -out shadow
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/cat-shadow.png" alt="nmap scan">

Knowing that we can modify the hash from root in the shadow file, set our password and write it, so we can login to root with our own password.
```
./openssl aes-256-cbc -a -salt -in shadow -out shadow.enc
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/coffee-pass.png" alt="nmap scan">

And, we get to root with the password that we have set!

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/root.png" alt="nmap scan">

And let's read the flag.

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/light/root-flag.png" alt="nmap scan">

We have owned Lightweight!

## To Conclude

To be honest the machine was challenging and there was a lot to learn. The privileges escalation process was exotic in my own experience due to my lack of knowledge of capabilities in windows. Saying that, the learning experience was fruitful with this machine. 




