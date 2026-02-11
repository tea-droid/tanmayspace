---
title: "Building and Breaking the Active Directory"
author: "Tanmay Sharma"
date: 2025-10-08
tags: ["cybersecurity", "Active Direectory"]
---

note: if learning about the attacks on AD directory is the goal, check out: https://github.com/Orange-Cyberdefense/GOAD. This blog is focused on demonstrates **my** knowledge of enumeration and exploitation of active directory.  

According to microsoft's website, Active Directory (AD) provides the methods for storing directory data and making this data available to network users and administrators.  
In simple words, it's a phone book that can be accessed over a network which contains information on computers, user, printers etc. Authentication on windows is done through Kerberos and Kerberos Tickets. For other systems (linux, mac) it uses protocols like RADIUS, LDAP (Lightweight Directory Access Protocol).  

![gordon-ramsay-angry](images/gordon-ramsay-angry.png)

As this blog is more of my understanding rather than an essay on AD, I will keep it short:    
AD is used for:
- Authenticating users and computers
- Locating computers by name
- Applying policies to users and computers
- Discovering and Locating services (MS Exchange, MS SQL Server, etc)
- Storing certain configuration data

And..  
Its main protocols are:
- A variant of LDAP for distributing computer and user data
- Kerberos for authentication
- DNS for resolving services and computer names to addresses
- SMB/CIFS for file replication and various other auxiliary functions.
- RPC over SMB/CIFS for various functions not covered by the other protocols.  

## The Lab  
This project is split into two parts: building the lab environment and then systematically breaking it.  

## Link >>> [The Building Process](/subpages/the-building-process/) <<<
Setting up a vulnerable AD environment from scratch. This covers spinning up a Domain Controller running Windows Server 2022, configuring Active Directory Domain Services and Certificate Services, and joining two Windows 10 workstations (THEPUNISHER and SPIDERMAN) to the domain. Everything runs on VMs, so you'll need around 180 GB of disk space and at least 16 GB of RAM.  

## Link >>> [The Breaking Process](/subpages/the-breaking-process/) <<<
Once the lab was running, I worked through attacking it end to end. The attacks are organized into four phases:  
- Initial Attack Vector — LLMNR Poisoning, SMB Relay, IPv6 attacks, and getting that first shell
- Post-Compromise Enumeration — Using tools like BloodHound, ldapdomaindump, PlumHound, and PingCastle to map out the domain
- Post-Compromise Attacks — Pass-the-Hash, Kerberoasting, Token Impersonation, credential dumping, and more
- Post-Domain Compromise — Dumping NTDS.dit, Golden Ticket attacks, and persistence
