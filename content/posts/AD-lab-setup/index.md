---
title: "Building and Breaking the Active Directory"
date: 2025-10-08
read: "2 Hr read"
tags: ["cybersecurity", "Active Direectory"]
---

[! WARNING]
INCOMPLETE


note: if learning about the attacks on AD directory is the goal, check out: https://github.com/Orange-Cyberdefense/GOAD

According to microsoft's website, Active Directory (AD) provides the methods for storing directory data and making this data available to network users and administrators.
In simple words, it's a phone book that can be accessed over a network which contains information on computers, user, printers etc. Authentication on windows is done through Kerberos and Kerberos Tickets. For other systems (linux, mac) it uses protocols like RADIUS, LDAP (Lightweight Directory Access Protocol).


<img width="868" height="461" alt="image" src="https://github.com/user-attachments/assets/68443969-8544-47bf-b8b5-f4043ae3fe96" />


## Requirements
Right, now that we understand what AD is....what do we need to make this lab?

As AD most common for windows systems. We need to download a few ISO files. 
Specifically these:
  - For Domain Controller: https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2022
  - For User Machines: https://www.microsoft.com/en-us/evalcenter/evaluate-windows-10-enterprise


