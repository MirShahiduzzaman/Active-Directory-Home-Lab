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

12. After logging in, go to the network settings > Change Adapter Options. Then go to the properties for the Internal Adapter and set the IPv4 address to 172.16.0.1 with subnet mask 255.255.255.0. Keep Default Gateway empty because the domain controller itself will serve as the default gateway. Also, when active directory is installed, it automatically installs DNS, so we put its own IP address (172.16.0.1) as the DNS IP (alternatively, we can use the loopback address as well - 127.0.0.1)

13. On the Server Manager dashboard, click add roles and features. After clicking next a few times, we reach the destination server selection, where we select itself and on the role selection, we check the 'Active Directory Domain Services' box. Finally, we can continue hitting 'next' then 'install'.

![alt text][add_active_directory_role]

[add_active_directory_role]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/add_active_directory_role.png "Active Directory Domain Services"

14. After installed, we notice a yellow sign on the flag icon in the dashboard, prompting us to promote this server to domain controller, which we click. We add new forest that we name 'mydomain.com' for testing purposes.

![alt text][root_domain_name]

[root_domain_name]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/root_domain_name.png "Root Domain Name"

15. After clicking next, we create a password, then moving on to the next screen a few times, we finally hit install. Note this will restart the computer.

16. We will now create a domain admin account to use instead of the built in admin account. Start > Windows Administrative tools > Active Directory Users and Computers

17. Right click mydomain.com > new > Organizational Unit. Name it _ADMINS, unchecking the protect container from accidental deletion box. Now, the _ADMINS folder is added

18. Right click on _ADMINS folder > New > User. Fill out fields for Mir Shahiduzzaman admin account. We will use the naming scheme of a-(initial of first name)(last name). Then, we can set the password on the next screen, unchecking 'User must change password at next login' and checking 'Password never expires'. Finally, we head to next screen and select 'finish'.

19. The account is not admin yet. To make it admin, go to the user properties and add it to the Domain_Admins group. The account is now admin

20. To test our new account, log out then log in as other user, inputing a-mshahiduzzaman along with the password.

![alt text][domain_added]

[domain_added]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/MYDOMAIN_a-mshahiduzzaman_login.png "Other User in MYDOMAIN Domain"

21. Upon log in, we want to add RAS/NAT to allow windows 10 clients to be on a virtual private network while still being able to access internet through the domain controller. While on the account, we use the server manager to ad the 'Remote Access' role to the server. We will also install routing (as prompted on the next screen) and finally install.

22. We head to routing and remote access through the dashboard and select our \_INTERNET_ adapter to connect to the internet and finish.

23. Now, we want to set up our DHCP server to allow windows 10 clients to attain IP addresses, so we add the 'DHCP Server' role

![alt text][dhcp_role]

[dhcp_role]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/add_DHCP_server_role.png "DHCP Role"

24. Now, we set the DHCP scope to 172.16.0.100-200 with subnet mask 255.255.255.0, gateway 172.168.0.1 (the domain controller's IP address), and DNS 172.16.0.1, keeping the default value for lease time. Then, Autorize the DHCP server and refresh.

25. TO BE UPDATED - Project Complete Just Documenting
