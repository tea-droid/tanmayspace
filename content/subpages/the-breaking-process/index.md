---
title: "The Breaking Process"
_build:
  list: never
---

Now I had a personally hosted AD lab and it was all mine to break.
The obvious question was where to start.

This was my first time seriously attacking Active Directory end to end, so I needed a clear structure rather than trial and error. What became apparent very quickly was that success in AD has far less to do with individual exploits and far more to do with methodical enumeration. Enumeration is not just a phase you pass through once, but something you return to after every new piece of access.

Every foothold, escalation, and lateral movement I achieved in this lab came from gradually understanding how the environment was put together. As I learned more about the domain, new attack paths became visible, and earlier assumptions often had to be revised. In that sense, Active Directory rewards careful observation more than speed or creativity.

To keep myself organized, I broke the attack process into the following phases:

I. Initial Attack Vector  
II. Post-Compromise Enumeration  
III. Post-Compromise Attacks  
IV. Post-Domain Compromise Attacks  

I also explored a set of last-resort Active Directory attacks for cases where no clear escalation paths were immediately available.

The sections below reflect how I approached each phase during this lab. Every attack listed is something I personally attempted and documented, with separate pages detailing how I performed each one and what I learned along the way.

## I. Initial Attack Vector
This phase focused on achieving my first foothold inside the domain, typically with low privileges and limited visibility.
Attacks performed:
- [LLMNR Poisoning](/subpages/llmnr-poisoning)
- [SMB Relay Attack](/subpages/smb-relay-attack)
- [Gaining Initial Shell Access](/subpages/ini-shell-access)
- [IPv6 Attacks](/subpages/ipv6)

## II. Post-Compromise Enumeration
Once I had initial access, enumeration became the most important activity. This was the phase where the environment started to make sense, and where most of my progress originated.
Enumeration performed using:
- [Ldapdomaindump](/subpages/ldapdomaindump)
- [BloodHound](/subpages/bloodhound)
- [PlumHound](/subpages/plumhound)
- [PingCastle](/subpages/pingcastle)

## III. Post-Compromise Attacks
Armed with a clearer picture of domain relationships and misconfigurations, I moved into privilege escalation and lateral movement.
Attacks performed:
- [Pass-the-Hash and Pass-the-Ticket Attacks](/subpages/passing-hash&tickets)
- [Kerberoasting](/subpages/kerberoasting)
- [Token Impersonation](/subpages/token-impersonation)
- [LNK File Attacks](/subpages/lnk-f-attack)
- [Group Policy Preferences Attacks](/subpages/gpp-attack)
- [Credential Dumping](/subpages/cred-dump)

## IV. Post-Domain Compromise Attacks
After reaching domain-level compromise, I focused on understanding what full control actually enables and how persistence is established.
Attacks performed:
- [Dumping NTDS.dit](/subpages/dump-ntds)
- [Golden Ticket Attack](/subpages/golden-ticket-attack)
- [Last Resort Active Directory Attacks](/subpages/last-resort-ad-attacks)

Finally, I explored additional techniques that can still be relevant when more direct attack paths fail.

The goal of this blog is to document how I approached breaking Active Directory for the first time, highlight the mistakes and assumptions I made along the way, and demonstrate a structured understanding of how AD attacks progress from initial access to full domain compromise.

