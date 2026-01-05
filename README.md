# Active-Directory-Home-Lab

## Diagram: 
![alt text][overview]

[overview]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/overview.png "Project Overview Diagram"

## Technologies
Microsoft Active Directory, PowerShell, VirtualBox Virtual Machine, Windows Server, Windows 10, Adapters, DHCP, DNS, IP, Scripting

## Overview
In this project, I worked with Active Directory and Powershell to create default profiles for 1000+ users. I ensured each user can log in to a domain and make use of the internet through their accounts.

This simulates a corporate network where many employees were hired, leading to the development and scheduled run of an accounts creation batch file for the new employees. 

It also simulates a corporate laptop, where employees log in using their corporate credentials.

## Procedure
1. I installed the Windows 10 and Windows 10 Server 2019 ISO files for the VirtualBox VM.

2. I configured settings for the Windows Server 2019 VM based on my machine specs. I also named it 'DC'

![alt text][windows_server_2019_name]

[windows_server_2019_name]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/server_2019_name_DC.png "Windows Server 2019"

3. Set up 2 NICS for the VM - Adapter 1 is attached to NAT and will connect to our home internet and Adapter 2 is attached to the internal network

4. Upon starting the VM, it prompted me to upload the Server 2019 ISO file, so I fulfilled the request

5. Set up settings (Needed to select one of the Desktop Experience Operating Systems or else it will not include a GUI - I chose Standard option)

6. Set up administrator account including password

7. Note Ctrl+Alt+Delete will not work - need to press host key + delete (my host key was right ctrl)

8. Also note upon logging in, I installed Guest Additions then restarted the machine

9. Open Adapter Settings then click on 'Change Adapter Options'. To distinguish between the adapter connected to our home internet and the adapter for internal network, I opened the network connection details for each. Upon inspection, I found one adapter had an IPv4 address of 10.x.x.x (x to replace numbers) whereas the other one had IPv4 address of 169.254.x.x (meaning it has no IP assigned and is using an APIPA address)

10. Rename the 10.x.x.x adapter to "\_INTERNET_" and the 169.254.x.x adapter to "X_Internal_X"

![alt text][adapter_names]

[adapter_names]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/renamed_adapters.png "Adapter Names"

11. Rename PC to "DC" and restart the VM

12. After logging in, go to the network settings > Change Adapter Options. Then go to the properties for the Internal Adapter and set the IPv4 address to 172.16.0.1 with subnet mask 255.255.255.0. Keep Default Gateway empty because the domain controller itself will serve as the default gateway. Also, when Active Directory is installed, it automatically installs DNS, so I put its own IP address (172.16.0.1) as the DNS IP (alternatively, I could have used the loopback address - 127.0.0.1)

13. On the Server Manager dashboard, click 'Add Roles and Features'. After clicking next a few times, I reach the destination server selection, where I select itself. On the role selection section, I checked the 'Active Directory Domain Services' box. Finally, I hit 'next' then 'install'.

![alt text][add_active_directory_role]

[add_active_directory_role]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/add_active_directory_role.png "Active Directory Domain Services"

14. After installed, I noticed a yellow sign on the flag icon in the dashboard, prompting me to promote this server to domain controller, so I click the icon and add new forest that I named 'mydomain.com' for testing purposes.

![alt text][root_domain_name]

[root_domain_name]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/root_domain_name.png "Root Domain Name"

15. After clicking next, I created a password, then moving on to the next screen a few times, I finally pressed install. (Note this will restart the virtual computer)

16. Afterwards, I created a domain admin account to use instead of the built in admin account. I navigated to Start > Windows Administrative tools > Active Directory Users and Computers

17. Right click mydomain.com > new > Organizational Unit. Name it _ADMINS, unchecking the protect container from accidental deletion box. Now, the _ADMINS folder is added

18. Right click on _ADMINS folder > New > User. Fill out fields for Mir Shahiduzzaman admin account. I will use the naming scheme of a-(initial of first name)(last name). Then, I set the password on the next screen, unchecking 'User must change password at next login' and checking 'Password never expires'. Finally, I head to next screen and press 'finish'.

19. The account is not admin yet. To make it admin, go to the user properties and add it to the Domain_Admins group. The account is now admin

20. To test the new account, log out then log in as other user, inputing a-mshahiduzzaman along with the password.

![alt text][domain_added]

[domain_added]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/MYDOMAIN_a-mshahiduzzaman_login.png "Other User in MYDOMAIN Domain"

21. Upon log in, I wanted to add RAS/NAT to allow windows 10 clients to be on a virtual private network while still being able to access internet through the domain controller. While on the account, I used the server manager to add the 'Remote Access' role to the server. I also installed routing (as prompted on the next screen) and finally hit 'install'.

22. I navigated to 'Routing and Remote Access' through the dashboard and selected the '\_INTERNET_' adapter to connect to the internet and hit 'finish'.

23. Then, I wanted to set up the DHCP server to allow windows 10 clients to attain IP addresses, so I added the 'DHCP Server' role

![alt text][dhcp_role]

[dhcp_role]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/add_DHCP_server_role.png "DHCP Role"

24. Set the DHCP scope to 172.16.0.100-200 with subnet mask 255.255.255.0, gateway 172.168.0.1 (the domain controller's IP address), and DNS 172.16.0.1, keeping the default value for lease time. Then, authorize the DHCP server and refresh.

25. For lab purposes, disable 'Internet Explorer Enhanced Security Configuration'

26. For creating 1000 users using a script, I had two files: one containing randomized names (I added my own name to the top as well) and another file containing the script we will run. I then opened PowerShell ISE and for lab purposes, run 'Set-ExecutionPolicy Unrestricted' to allow the VM to run the script, and finally run the script file. This resulted in about 1000 newly created user accounts, which can be found by opening 'Active Directory Users and Computers' and clicking the '_USERS' folder

![alt text][1000_users]

[1000_users]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/_USERS_folder_created_and_populated.png "(About) 1000 Users Created"

27. Last thing: Creating and running Windows 10 VM. In VirtualBox main menu, configure Windows 10 VM initial settings, ensuring adapter 1 is set to the internal network and to allow bidirectional clipboard.

28. Opening the Windows 10 VM, select 'I don't have a product key' and choose Windows 10 Pro (Note Windows 10 Home would likely not allow you to join the domain) and continue with set up

29. Create the username and password (I created a user with username 'user' and with no password) and disable all the options in the following screen

30. To check if internet is working, open cmd line and run 'ipconfig'. Here, I saw the ip address, subnet mask, and default gateway. (Note that if the default gateway info was missing, I would have to go back to the Windows Server 2019 VM, navigate to the DHCP settings, select 'Server Options', and configure the router settings, ensuring to set the ip address to 172.16.0.1. Then, I would restart the DHCP server, go back to the Windows 10 client VM, and run 'ipconfig /renew' in cmd line. The info would be there)

![alt text][ipconfig]

[ipconfig]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/ipconfig.png "Running ipconfig"

31. To check proper internet connectivity, I pinged google.com and it showed response times. I also pinged mydomain.com, which also showed response times.

32. Go to 'Rename this PC (advanced)' settings, changing computer name to 'CLIENT1' and domain to 'mydomain.com' and upon being prompted, enter the account info for an account associated with the 'mydomain.com' domain. Finally, select to restart the PC.

33. In Windows Server 2019, I went to the DHCP settings. Then, navigating to IPv4 address leases within the scope, I saw the leased ip address for the Windows 10 client VM.

![alt text][leased_ip_address]

[leased_ip_address]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/ip_address_leases.png "Successfully leased IP Address"

34. Opening 'Active Directory Users and Computers', it showed 'CLIENT1' has been successfully added as a part of the 'mydomain.com' domain. Note that if 'CLIENT1' is deleted from this list, I wouldn't be able to log in to the computer using one of the accounts on the 'mydomain.com' domain.

35. Finally, to test if I can in fact log into an account from the 'mydomain.com' domain, I logged out, then selected 'other user' on the sign in screen, inputting the details for one user in our domain. Once logged in, I opened cmd line and run 'whoami' to view the account's username!

![alt text][whoami]

[whoami]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/mshahiduzzaman_whoami.png "running the 'whoami' cmd"
