---
title: "Ldapdomaindump"
_build:
  list: never
---

ldapdomaindump was automatically used in the mitm6 attack but what if ipv6 was not possible and somehow I had a compromised machine..

then we have to implicitly use this


## Process

Step 1: 
```
mkdir marvel.local
cd marvel.local
```

Step 2:
`sudo ldapdomaindump ldaps://192.168.138.136 -u 'MARVEL\fcastle' -p Password1 -o`

![Pasted image 20251130154734](images/Pasted-image-20251130154734.png)
