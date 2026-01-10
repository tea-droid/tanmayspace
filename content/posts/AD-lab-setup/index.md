---
title: "Building and Breaking the Active Directory"
author: "Tanmay Sharma"
date: 2025-10-08
tags: ["cybersecurity", "Active Direectory"]
---

[! WARNING]
INCOMPLETE


note: if learning about the attacks on AD directory is the goal, check out: https://github.com/Orange-Cyberdefense/GOAD

According to microsoft's website, Active Directory (AD) provides the methods for storing directory data and making this data available to network users and administrators.
In simple words, it's a phone book that can be accessed over a network which contains information on computers, user, printers etc. Authentication on windows is done through Kerberos and Kerberos Tickets. For other systems (linux, mac) it uses protocols like RADIUS, LDAP (Lightweight Directory Access Protocol).


<img width="868" height="461" alt="image" src="https://github.com/user-attachments/assets/68443969-8544-47bf-b8b5-f4043ae3fe96" />

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


[The Building Process](the-building-process/) | [The Breaking Process](the-breaking-process/)
