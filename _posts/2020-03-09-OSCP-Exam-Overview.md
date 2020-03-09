---
title:  "OSCP Exam Overview"
date: 2020-03-9
tags: [Penetration Testing, Hacking, Hack The Box, Jeeves, OSCP, Offensive Security]
header: 
  image: "assets/images/oscp-exam/oscp-exam.jpg"
---

After going through the ten "hard bug good practice" machines recommended by [NetSec Focus](https://www.netsecfocus.com/), I decided to put countless hours behind the screen and practice things such as information gathering (professional googling), exploitation, privilege escalation, and documentation. The practice, successes, failures, and persistence gave good results due to I was able to earn my OSCP certification after failing the exam twice. This post will be related to things to look for if you decide to go for the exam, and giving a brief overview about the experience. This is the list provided by [NetSec Focus](https://www.netsecfocus.com/).

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/oscp-exam/list.jpg" alt="nmap scan">

## Time Management

You have 23 hours and 45 minutes to root 5 machines in a controlled environment provided by Offensive Security. There are machines that are worth 25, 20, and 10 points. Offensive security will provide information about the machines and how many points are worth. You have to get at least 70 points in order to pass the exam certification. Having that information in mind, it is a good idea try to manage your time to eat, take naps, and rest. Personally, I spent around 4-5 hours in each machine enumerating and looking for attack vectors, after that I took 20 minutes to rest. Rest is important to clear your mind and find the correct path to root the machine. I started with the Buffer Overflow machine which it usually takes around 1 hour to resolve, after that I spent most of the time in enumeration.  

**Buffer Overflow resources for practice**
- [Buffer](https://zero-day.io/buffer-overflow-introduction/?utm_source=share&utm_medium=ios_app&utm_name=iossmf) Overflow instructions. 
- [Vulnerable](https://github.com/justinsteven/dostackbufferoverflowgood) server to practice. 



## Enumerate All The Things!

After Offensive Security provided me all the instructions related to the exam, the usage of tools to automate some of the information gathering was essential. The tool [AutoRecon](https://github.com/Tib3rius/AutoRecon?utm_source=share&utm_medium=ios_app&utm_name=iossmf) was very useful because it provided specific results for tools recon tools, the good part is that it saves all the results and commands in a file so you can paste them in your report after finishing the exam. Most of the time spent during the exam was enumerating the machines in order to find a good attack vector.

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/oscp-exam/recon.gif" alt="nmap scan">

Remember, if you think you're loosing something during your enumeration probably you are. Take your time to breath, and read your steps to see where you missed some valuable information. 

## Google All The Things!

As humans, we cannot know everything computer related, that is why tools such as the search engine Google exists. There were moments of confussion related to exploits, services, and servers that I did not understand at all. Going through the enumeration phase, and reading some articles related to vulnerable servers, what it does, and how it works, really helped me to get a couple shells back. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/oscp-exam/recon2.gif" alt="nmap scan">

**Note:** Take the time to read and understand what the exploit works and how you can actually change some part of the code in order to make it work with the target machine. Some exploits can work perfectly just by reading to the code and making a slight change in some variable. 

## Handling Rabbit Holes

Some moments you might find your brain executing the same commands, obtaining the same results, and obtaining the same frustation of not finding a way to get the target machines. When you see yourself in that moment, you are in a rabbit hole, get out! This usually works for me in order to get out of the rabbit holes. 

- Get away from the keyboard for a moment. 
- Take a deep breath.
- Drink some water. 
- Walk a little bit. 
- Think about the process, what information you have gotten, and how you can use the information to get different results. 
- Comeback with new ideas. 
- Sit down and get back to the terminal. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/oscp-exam/rabbit-hole.gif" alt="nmap scan">

## Practice, practice, practice!

The OSCP exam is going to test your skills on penetration testing, sounds obvious, but I went the first attempt of the exam without practice some things, and it didn't go that well. After learning from my mistakes, I decided to buy more time in the OSCP labs and pay for a Hack The Box in order to make my skills sharper. There is not some "how to get practice", just do it, sit down and try new things in the labs. The methodology and mindset is something good to have in your mind in the moment of practice, information gathering and some recon tools are going to be very useful during this process. 

**Note:**
- If you don't understand the tool, read the manual.
- Be creative, the solution might be in front of your nose.
- Do not get discourage if you don't get everything in a fast pace. 
- Be open to learn new things. 
- Seek for people's advice that have a extended experience in the field. 

**Side Note:**
Do not pretend to root all 5 machines without prior understanding and practice of the technology, but this can depends if you're somebody who have had prior experience in the cybersecuity industry, or you're just giving a shoot to the penetration testing world. Do not fully believe me, I'm just some 18 y/o noob trying to earn, and practice experience while I fell in love with the offensive security industry. 

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/oscp-exam/rabbit-hole.gif" alt="nmap scan">
