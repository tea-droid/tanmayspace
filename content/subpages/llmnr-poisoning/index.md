
-> A general idea of how LLMNR is leveraged for an attack:  
![llmnr](llmnr.png)

---

## Basic Steps:
1. Run Responder - `sudo responder -I tun0 -dwP`
	1. responder responds to traffic
	2. A good time to run is - early in moring or after lunch, basically after a break when people are logging in into their computers and are GENERATING A LOT OF TRAFFIC
	3. Do not run vuln scans (eg nessus) at the same time
2. An event occurs...
   ![Pasted image 20251124133806](Pasted-image-20251124133806.png)
3. Get Hashes on responder (NTLMv2 mostly)
4. Crack the hash using something like hashcat 


----

## Mitigation

- Best defense - disable LLMNR and NBT-NS (older LLMNR). To disable... 
	1. LLMNR: select "Turn OFF Multicast Name Resolution" under Local Computer Policy > Computer Configuration > Administrative Templates > Network  > DNS Client in the Group Policy Editor.
	2. NBT-NS: navigate to Network Connections > Network Adapter Properties > TCP/IPv4 Properties > Advanced tab > WINS tab and select "Disable NetBIOS over TCP/IP".
- If LLMNR/NBT-NS is required and/or cannot be disabled
	1. Require Network Access Control.
	2. Require strong user passwords (e.g., >14 characters and limit common word usage). The more complex and long the password, the harder it is for an attacker to crack the hash.


---

## Lab

1.  `nmap `
