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

25. For lab purposes, disable internet Explorer Enhanced Security Configuration

26. Now, for creating 1000 users using a script, I had two files: one containing randomized names (I added my own name to the top for fun) and another file of the script we will run. We then open PowerShell ISE and for lab purposes, run 'Set-ExecutionPolicy Unrestricted' to allow the VM to run the script, and finally run the script file. This will result in about 1000 users, which can be found by opening 'Active Directory Users and Computers' and clicking the '_USERS' folder

![alt text][1000_users]

[1000_users]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/_USERS_folder_created_and_populated.png "(About) 1000 Users Created"

27. Last thing: Create and run Windows 10 VM. In VirtualBox main menu, configure Windows 10 VM initial settings, ensuring adapter 1 is set to the internal network and to allow bidirectional clipboard.

28. Opening the Windows 10 VM, select 'I don't have a product key' and choose Windows 10 Pro (Note Windows 10 Home would likely not allow you to join the domain) and continue with set up

29. Create the username and password (I created a user with username user and with no password) and disable all the options in the following screen

30. To check if internet is working, open cmd line and run 'ipconfig'. You should see the ip address, subnet mask, and default gateway. (Note that if you are missing the default gateway info, go back to the Windows Server 2019 VM, head to DHCP settings, select Server Options, and configure the router settings, ensuring to set the ip address to 172.16.0.1. Then restart the DHCP server, go back to the Windows 10 client VM, and run 'ipconfig /renew' in cmd line and the info should be there)

![alt text][ipconfig]

[ipconfig]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/ipconfig.png "Running ipconfig"

31. To check proper internet connectivity, ping google.com and it should show data for response times. Also ping mydomain.com, which would likewise show data for response times.

32. Go to 'Rename this PC (advanced)' settings, changing computer name to 'CLIENT1' and domain to 'mydomain.com' and upon being prompted, enter the account info for an account associated with the 'mydomain.com' domain. Finally, select to restart the PC.

33. In Windows Server 2019, go to DHCP settings. Then, navigate to IPv4 address leases within the scope and we will see the leased ip address for the Windows 10 client VM

![alt text][leased_ip_address]

[leased_ip_address]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/ip_address_leases.png "Successfully leased IP Address"

34. Open Active Directory Users and Computers and we see that 'CLIENT1' has been successfully added as a part of the 'mydomain.com' domain. Note that if 'CLIENT1' is deleted from this list, we cannot log in to the computer using one of the accounts on the 'mydomain.com' domain

35. Finally, to test if we can in fact log into an account from the 'mydomain.com' domain, we log out, then select 'other user' on sign in screen, inputting the details for one user in our domain. Once logged in, we can open cmd line and run 'whoami' to view the account's username!

![alt text][whoami]

[whoami]: https://github.com/MirShahiduzzaman/Active-Directory-Home-Lab/blob/main/mshahiduzzaman_whoami.png "running the 'whoami' cmd"
