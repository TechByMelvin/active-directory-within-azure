<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1> Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)



<h2>Deployment and Configuration Steps</h2>

<p>
<img src="https://imgur.com/xgk1PSB.jpg" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
1. Install Active Directory Domain Services (AD DS)
  
1.Login to DC-1 (Domain Controller virtual machine).

2.This machine will serve as your domain controller.
Open Server Manager, click on Add Roles and Features, and select Active Directory Domain Services.

3.After installation, click on Promote this server to a domain controller.

4.Set up a new forest, naming the domain as mydomain.com (or another domain name of your choice).

-Example: mydomain.com.

-Follow the prompts, configure the domain, and then Restart the server.
</p>
<br />

<p>
<img src="https://imgur.com/SgKqpDo.jpg" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
2. Create a Domain Admin User.
  
1.Open Active Directory Users and Computers (ADUC).

-You can find this by searching for ADUC or opening it from the Server Manager.

2.In ADUC, create the following Organizational Units (OUs):

_EMPLOYEES: This OU will store all employee user accounts.

_ADMINS: This OU will store the administrative accounts.

3.Create a new user in the _ADMINS OU:

Name:
Username: 
Password: 
Add  to the Domain Admins Security Group to grant administrative privileges.

Log out of mydomain.com\username and log back in as mydomain.com\jane_admin.

From now on, use the admin account as your primary admin user.
</p>
<br />

<p>
<img src="https://imgur.com/moMOWT6.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
3. Join Client-1 to the Domain
  
1.Open the Azure Portal and find the Client-1 virtual machine.

2.In the Networking section, set Client-1’s DNS settings to point to the Private IP address of the domain controller (DC-1).

3.This ensures that Client-1 can communicate with DC-1 for domain-related services.
Restart Client-1 from the Azure portal.

4.Login to Client-1 as the local admin user (labuser) and join Client-1 to the domain:

-Right-click on This PC > Properties > Change Settings under Computer name, domain, and workgroup settings.

5.Join the computer to the domain mydomain.com.

6.After the computer restarts, verify that Client-1 appears in Active Directory Users and Computers (ADUC).

7.In ADUC, create a new Organizational Unit (OU) named _CLIENTS and move Client-1 into this OU for organizational purposes.
</p>
<br />


<p>
<img src="https://imgur.com/z9v3ziW.jpg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
5. Create Additional Users and Log Into Client-1
  
1.Login to DC-1 as mydomain.com\jane_admin.

2.Open PowerShell ISE as an administrator.

3.Create a new PowerShell script file, and paste the following script to create multiple user accounts:

powershell
Copy code
# Script to create bulk users
Import-Module ActiveDirectory

For ($i=1; $i -le 10; $i++) {
    $Username = "User" + $i
    $Password = "P@ssword" + $i
    New-ADUser -Name $Username -AccountPassword (ConvertTo-SecureString $Password -AsPlainText -Force) `
    -PasswordNeverExpires $true -Enabled $true `
    -Path "OU=_EMPLOYEES,DC=mydomain,DC=com" -UserPrincipalName $Username@mydomain.com
}
Run the script and observe the user accounts being created in Active Directory.

4.Once the script has completed, open Active Directory Users and Computers (ADUC) to verify that the new accounts have been created in the _EMPLOYEES OU.

5.Attempt to log into Client-1 using one of the newly created accounts.

6.Remember to use the password assigned to that user account in the script (e.g., P@ssword1, P@ssword2, etc.).
</p>


