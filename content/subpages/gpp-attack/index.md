---
title: "Group Policy Preferences Attacks"
_build:
  list: never
---

a.k.a cPassword Attack  

Group Policy Preferences (GPP) allowed admins to create policies using embedded credentials  

These Credentials were encrypted and placed in a "cPassword"  

The key was accidentally released to this "cPassword"  

Patched in MS14-025, but it doesn't prevent previous users  

STILL RELEVANT ON PENTESTS (according to Heath Adams)  

From a real pentest: (msfconsole - inverted colors)  
![Pasted image 20251222193454](images/Pasted-image-20251222193454.png)  

![Pasted image 20251222193802](images/Pasted-image-20251222193802.png)

---
## Process  
1. use msfconsole
2. RUN smb_enum_gpp after srtting apt OPTIONS
3. this will look for the Groups.xml -> if found it will look for cPasswords -> if found it will decrypt and print the password  

![Pasted image 20251222202337](images/Pasted-image-20251222202337.png)


---

## Mitigation

1. PATCH! Fixed in KB29624886
2. In reality: delete the old GPP xml files stored in the SYSVOL
