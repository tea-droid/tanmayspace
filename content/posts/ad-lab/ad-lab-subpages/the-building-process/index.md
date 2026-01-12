---
title: "The Building Process"
_build:
  list: never
---


## Requirements
Right, now that we understand what AD is....what do we need to make this lab?

As AD most common for windows systems. We need to download a few ISO files. 
Specifically these:
  - For Domain Controller: https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2022
  - For 2 User Machines: https://www.microsoft.com/en-us/evalcenter/evaluate-windows-10-enterprise

I followed the PEH course by TCM Security to build this lab, which is why I installed an older version of windows. (You may try the newer 2025 version which you can find [here](https://www.microsoft.com/en-us/evalcenter), as far as I know it won't matter much)

**Also, this setup would approximately require a 60 GB of disk space for each virtual machine (180 GB total) and atleast 16 GB of RAM.**


## Procedure
We will be building this entire lab on virtual machines so open your favourite hypervisor software, keep all your necessary ISOs downloaded, brew that coffee and let's get started.


### Domain Controller
We will start with setting up the Domain Controller first. This is a server that uses the active directory to authenticate and authorize domain access active directory.
