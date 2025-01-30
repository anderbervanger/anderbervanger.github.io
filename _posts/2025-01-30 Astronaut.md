---
layout: post
title: Astronaut - Walkthrough
date: 30-01-2025
categories: [writeups]
tags: [Proving Grounds, Linux, Easy, walkthrough]
---

# Astronaut - Walkthrough

Astronaut is an easy box on OFFSEC’s Proving Grounds and the community agrees that it is easy difficulty. 
- [Astronaut - Walkthrough](#astronaut---walkthrough)
  - [Recon](#recon)
  - [Enumeration](#enumeration)
  - [Exploitation](#exploitation)
  - [Privilege Escalation](#privilege-escalation)


## Recon
Start scanning all ports:
```
nmap -p- 22,80 192.168.60.12 -v
```
![Scan de todas as portas](/assets/images/Astronaut/1.png)

Scanning only the open doors we found:

```
nmap -sC -sV -Pn -p 22,80 192.168.60.12 -v
```

![Scan portas abertas](/assets/images/Astronaut/2.png)

## Enumeration

Accessing the page on port 80 there is a folder called grav-admin. I couldn't access it using the browser:

![port80](/assets/images/Astronaut/3.png)

I tried putting /grav-admin/ after the IP and accessed a new page:

![port80](/assets/images/Astronaut/4.png)

I used feroxbuster to find more directories on the site, finding the directory *grav-admin/admin* 

```
┌──(kali㉿kali)-[~/Desktop/pen200/labs/Astronaut]
└─$ feroxbuster -u http://192.168.160.12/grav-admin/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt -s 200
```
![port80](/assets/images/Astronaut/5.png)

## Exploitation

I used searchsploit to look for Grav exploits:
`searchsploit -s Grav`

![searchsploit](/assets/images/Astronaut/6.png)

The GravCMS 1.10.7 - Arbitrary YAML Write/Update (Unauthenticated) exploit looks promising:

![searchsploit](/assets/images/Astronaut/15.png)

I downloaded the file with searchsploit:
`seachsploit -m 49973`


I changed the target field with the IP of the laboratory and changed the shell in base64 to the one with the ip of my machine
*target= "http://192.168.160.12/grav-admin*
![searchsploit](/assets/images/Astronaut/16.png)

![searchsploit](/assets/images/Astronaut/7.png)

I started a listerner on port 4444 and ran the exploit and got a shell:
```
┌──(kali㉿kali)-[~/Desktop/pen200/labs/Astronaut]
└─$ python3 49973.py 
```

![shell](/assets/images/Astronaut/8.png)

## Privilege Escalation
I set up a webserver on my host to host linpeas.sh and download it to the target:

![webserver](/assets/images/Astronaut/9.png)

![webserver](/assets/images/Astronaut/10.png)

I used chmod +x to give the file execute permission and ran linpeas.sh:

![linpeas](/assets/images/Astronaut/11.png)

Among the SUIDs found by linpeas, php7.4 had the message (unknown SUID binary) so I went to GTFOBins and looked for the php suid - https://gtfobins.github.io/gtfobins/php/#suid

I followed the instructions on the site replacing "./php" to the path of php7.4 binary for escalating privileges:

![suid](/assets/images/Astronaut/12.png)

![suid](/assets/images/Astronaut/13.png)

After that, I went into the root directory and got the proof.txt flag:

![flag](/assets/images/Astronaut/14.png)


