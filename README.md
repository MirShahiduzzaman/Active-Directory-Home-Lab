# Active-Directory-Home-Lab

## Diagram: 
![alt text][overview]

[overview]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/overview.png "Project Overview Diagram"

## Technologies
Microsoft Active Directory, PowerShell, VirtualBox Virtual Machine, Windows Server, Windows 10, Adapters, DHCP, DNS, IP, Scripting

## Overview
In this project, I worked with Active Directory and Powershell to create default profiles for 1000+ users. I ensured each user can log in to a domain and make use of the internet through their accounts.

## Procedure
1. I installed the Windows 10 and Windows 10 Server 2019 ISO files for the VirtualBox VM.

2. I configured settings for the Windows Server 2019 VM based on my machine specs. I also named it 'DC'

![alt text][windows_server_2019_name]

[windows_server_2019_name]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/server_2019_name_DC.png "Windows Server 2019"

3. I set up 2 NICS for the VM - Adapter 1 is attached to NAT and will connect to our home internet and Adapter 2 is attached to the internal network

4. Upon starting the VM, it prompted me to upload the Server 2019 ISO file, so I fulfilled the request

5. Set Up Settings (Needed to select one of the Desktop Experience Operating Systems or else it will not include a GUI - I chose Standard option)

6. Set up Administrator Account with password

7. Note Ctrl+Alt+Delete will not work - need to press host key + delete (my host key was right ctrl)

8. Also note upon logging in, we installed Guest Additions and restart the machine

9. Open Adapter Settings then Click on Change Adapter Options. Find out which is the one connected to our home internet and which is the one for internal network. To do so, we opened the network connection details for each. Upon inspection, I found one adapter had an IPv4 address of 10.x.x.x (x to replace numbers) whereas the other one had IPv4 address of 169.254.x.x (meaning it has no IP assigned and is using an APIPA address)

10. Rename the 10.x.x.x adapter to "\_INTERNET_" and the 169.254.x.x adapter to "X_Internal_X"

![alt text][adapter_names]

[adapter_names]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/renamed_adapters.png "Adapter Names"

11. Rename PC to "DC" and restart the VM

12. EXPECT NEXT STEPS SOON
