---
author: philiphendry
comments: true
date: 2009-07-07 20:01:46+00:00
layout: post
slug: adding-a-boot-menu-option-for-a-vhd-in-windows-7
title: Adding a Boot Menu Option for a VHD in Windows 7
wordpress_id: 231
tags:
- windows-7
---

Having already created and installed a a VHD using VirtualPC I now wanted to use the great new feature available in both Windows 7 and Windows Server 2008 which allows you to boot the machine from the VHD instead of the physical partition. In order to achieve this I just had to run a few commands at a command prompt running as admin:

 

 
    
    C:\Windows\system32>bcdedit /copy {current} /d "Windows 7 Visual Studio 2010 VHD"
    
    
    The entry was successfully copied to {fc7d293a-666d-11de-bd7a-001bdc0fdad5}.
    
    
    C:\Windows\system32>bcdedit /set {fc7d293a-666d-11de-bd7a-001bdc0fdad5} osdevice vhd=[c:]\VMs\Windows7VisualStudio10.vhd
    
    
    The operation completed successfully.





The first command returns a GUID which must be copied into the second command but that’s all that’s required to add a new entry to the boot menu (which in Windows 7 is no longer a boot.ini file but a ‘mini-database’.)
