---
title:  "Hack The Box / Active"
date: 2020-01-26
tags: [Penetration Testing, Hacking, Hack The Box, Jeeves, OSCP, Offensive Security]
header: 
  image: "assets/images/active/active-header.png"
---
Active is our fourth machine in the OSCP list provided by [NetSec Focus](https://www.netsecfocus.com/)! This machine was a great learning experience where SMB enumeration and some knowledge about [kerberos](https://en.wikipedia.org/wiki/Kerberos_(protocol))  were essential in order to root this machine. This is the fourth blog before my third attempt to the OSCP exam, so let's get to it!

## Information Gathering

### Nmap Scan
So, we start with a nmap scan to check what ports are open and what services are running in this machine. 

```
nmap -sC -sV -p- -oN scan.nmap 10.10.10.59
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/active/nmap.png" alt="nmap scan">

Do not panic! That sounds like a lot of enumeration to do, and it is, but remember some of the important services in this nmap scan, which means that the important services for this machine are SMB, kerberos, and HTTP. But after doing some discovering and extense enumeration the services that will give a straight path to root this machine are SMB and kerberos.

### SMB Enumeration
There are different tools to make SMB enumeration, you can use [enum4linux](https://highon.coffee/blog/enum4linux-cheat-sheet/), [nmap-scripts](https://nmap.org/nsedoc/scripts/smb-enum-users.html), but my preference is [smbmap](https://tools.kali.org/information-gathering/smbmap) due to its acurate results. 

```
smbmap -H 10.10.10.100
```
Where:
- ```-H``` specifies the host to scan. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/active/smbmap.png" alt="nmap scan">

Looking to the results given by smbmap we can onlu access the share __Replication__ anonymously. In order to access to __Replication__, we are going to need [smbclient](https://www.tldp.org/HOWTO/SMB-HOWTO-8.html), as the log in is anonymously, we don't need a password to get in.

```
smbclient //10.10.10.100/Replication
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/active/smbmap.png" alt="nmap scan">

Once we loged in to SMB there are going to be many folders and files to look at, but the folder and file that actually gave food results was ```\active.htb\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\MACHINE\Preferences\Groups\``` which contains a xml files with interesting information about groups. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/active/smb-groups.png" alt="nmap scan">

Once we get the file in our local host, this is the information from the xml file.

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/active/password-group.png" alt="nmap scan">

AAS you can see, it appears something called [cpassword](https://pentestlab.blog/tag/cpassword/) which has some kind of GPP encryption that we can decrypt with the tool called [gpp-decrypt](https://tools.kali.org/password-attacks/gpp-decrypt) which is installed in Kali Linux. This is the command to decrypt the password. 
```
gpp-decrypt edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ
```
After executing the command we got a password which is ```GPPstillStandingStrong2k18```, but what about the user? Well, in the xml file showed above appears clearly that the username is ```SVG_TGS```, then we can use this username and password to access to SMB but not anonymously, this time as a registered user. 

```
smbclient //10.10.10.100/Users -U SVC_TGS
```

### User Flag

After loging in there is a good chance to obtain the user flag where it's located in ```\SVG_TGS\Desktop``` and get can transfer it to our localhost and read it!

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/active/user-flag.png" alt="nmap scan">

wolah! We got user.

### Getting Root
Doing this on my own was complicated due to my lack of knowledge about kerberos enumeration, so I have to thank [0xRick](https://0xrick.github.io/) for his explanation about [kerberoasting](https://attack.mitre.org/techniques/T1208/). Before attempting this please add ```10.10.10.100``` in your ```/etc/hosts/``` file as ```active.htb``` in order to execute successfully this attack.

#### Get User SPNs
There is a good tool in the [impacket](https://github.com/SecureAuthCorp/impacket) tool set called [GetUserSPNs.py](https://raw.githubusercontent.com/SecureAuthCorp/impacket/master/examples/GetUserSPNs.py) which allows us to get the administrator hash using our credentials from the user. 

```
./GetUserSPNs.py -request active.htb/SVC_TGS
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/active/getuserspns.png" alt="nmap scan">

And we have a hash! It's time to use [John](https://www.openwall.com/john/) to crack this hash.

#### Cracking The Hash with John
After saving the hash we can in file where I saved it as ```admin``` we can use john and a wordlist in order to crack the hash and obtin the password with the following command.

```
john -w=/usr/share/wordlists/rockyou.txt admin
```
Where
- ```w=``` specifies the wordlist to use to crack the password. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/active/john.png" alt="nmap scan">

And out administrator password is: ```Ticketmaster1968```!

#### Using psexec to Get The Root Flag.

From impacket there are another amazing tool called [psexec.py](https://raw.githubusercontent.com/SecureAuthCorp/impacket/master/examples/psexec.py) where having the adminstrator password will allow us to connect to the machine and request the command prompt from the administrator. 

```
python3 psexec.py administrator@active.htb
```
<img src="{{ site.url }}{{ site.baseurl }}/assets/images/active/root-flag.png" alt="nmap scan">

And we got the root flag! 

## To conclude
Enumeration in the important services is a good technique to avoid rabbit holes, also I learned that SMB can be useful for getting files and information from the victim machine. Also impacket has a good set of networking tools that its usage will be beneficial at the time to do penetration testing. 
