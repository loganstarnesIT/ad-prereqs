<p align="center">
<img src="https://www.31west.net/wp-content/uploads/2022/11/what-is-active-directory-and-why-is-it-used.png" width="60%" alt="active directory logo"/>
</p>

<h1>Active Directory - Prerequisites and Installation</h1>
This tutorial outlines the prerequisites and installation of Windows Active Directory.<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Windows Server Manager

<h2>Operating Systems Used </h2>

- Windows 11 Pro x64 / Windows Server 2022 Datacenter Azure Edition </b> 

<h2>List of Prerequisites</h2>

- 1 Virtual Machine acting as "Client"
- 1 Virtual Machine acting as "Domain Controller"
- Internet Connection
- Remote Desktop Connection

<h2>Installation Steps</h2>

<h3> Configuring VMs </h3>
<p float="left">
  <img align="center" src="https://i.imgur.com/cGfb5wq.png" width="49%" />
  <img align="center" src="https://i.imgur.com/6FGHnGV.png" width="49%" />
</p>
  <img align="center" src="https://i.imgur.com/20yoGPc.png" width="49%" />
</p>
<p>
1. The first step is to configure both of our Virtual machines with the correct Windows Images, Size, Region, and Resource Group. 
  
  For our client VM here, we'll set the Windows Image to "Windows 11 Pro x64", the size to "2 VCPUs & 8 GB of memory", the region to "US-East 2", and ensure
the resource group is correct. We'll do the same for the Domain Controller VM while changing the Windows Image to "Windows Server 2022 Datacenter Azure edition"
and also ensuring the correct resource group. 

  When setting up a Resource Group, it's important to set up a VNet within it for the Virtual Machines to use. Once the VNet has been created within the group, the
VMs will often automatically get assigned to that VNet, but it's important to ensure they are assigned manually by checking the "Network" tab of the VM we're creating.
</p>
<br />

<h3> Setting a Static IP </h3>
<p float="left">
  <img align="center" src="https://i.imgur.com/TlX8xqs.png" width="49%" /> 
  <img align="center" src="https://i.imgur.com/UPBfyC8.png" width="49%" />
  <img align="center" src="https://i.imgur.com/ZaQaXcG.png" width="49%" />
</p>
<p>
2. Next, we are going to set the Private IP address of the Domain Controller to a Static IP. This is important because the client computer
will be utilizing the Domain Controller as a DNS server, as shown. If the private IP were to change frequently, it would cause problems for the client
when searching.

Then we'll go to the "Network Interface" of the Client system and under "Settings > DNS Servers", we'll set it to "Custom" and then enter the private IP of DC-1 (Domain Controller)
</p>
<br />

<p>
<h3> Turning off DC-1's Firewall </h3>
<p float="left">
  <img align="center" src="https://i.imgur.com/ayjWGsv.png" width="69%" /> 
</p>
<p>
3. In order to properly test if connectivity between both DC-1 and the Client works, we'll disable the firewall on DC-1 for the time being. Remote into the DC-1 system using
"Windows Remote Desktop Connection" with the public IP of the VM and the credentials that were set during the creation of the VMs. Once you are connected and logged in, you will open the Start
menu and type "run" (or CTRL + R as a shortcut from the desktop) and type "fw.msc" to open the firewall settings.

Within the firewall menu, click the option titled "Windows Defender Firewall Properties," and in each profile section of the window that appears, hit "o" on your keyboard to disable each firewall.
Now hit Apply + OK, and we'll move on to the next step.
</p>
<br />

<p>
<h3> Remoting into Client & Checking DNS </h3>
<p float="left">
  <img align="center" src="https://i.imgur.com/iGBv0NA.png" width="49%" /> 
  <img align="center" src="https://i.imgur.com/M98QaAm.png" width="49%" />
</p>
<p>
4. Now that we have the firewall disabled on DC-1, we can now remote into the Client system using "Windows Remote Desktop Connection" with the 
public IP and the credentials that were set during the creation of the VM. 
  
  Once connected and logged into the client VM, we'll open PowerShell as an administrator to run the command "ipconfig /all". We should be able to see next to "DNS Servers" that it is properly 
using the Static IP address from DC-1. If it is showing a different DNS, restart the client VM, reconnect, and run the ipconfig command again.

  Now that the DNS server is showing in PowerShell, we can test that we can connect to it by running the command "ping 10.0.0.5" or whichever DNS you have set. It should work as shown above
in Image 2 and ping DC-1 successfully.
</p>
<br />

<p>
<h3> Installing A.D. Domain Services </h3>
<p float="left">
  <img align="center" src="https://i.imgur.com/bRuFKNA.png" width="49%" /> 
  <img align="center" src="https://i.imgur.com/lXPz1zl.png" width="49%" />
</p>
<p>
5. Next, we'll install Active Directory on DC-1 using the "Windows Server Manager" that opens when we first log in to the system. On the front page, we'll click the "Add Roles and Features"
option and it will load a menu. We'll click next until we reach "Server Roles", then select "Active Directory Domain Services" and click "Add Features" on the pop-up. 
  
Continue to click next until you reach Confirmation and elect "Restart the destination server automatically," and then 
finally click install and wait for it to fully install. Once it does, we'll move to the next step.
</p>
<br />

<p>
<h3> Promoting DC-1 as a Domain Controller </h3>
<p float="left">
  <img align="center" src="https://i.imgur.com/MV4CyAT.png" width="49%" /> 
  <img align="center" src="https://i.imgur.com/ZEmbv7R.png" width="49%" />
</p>
<p>
6. After the Domain Services are installed, we'll now promote DC-1 to an actual "Domain Controller". From the same front page of the Server manager, you'll spot a flag at the top right with a 
caution symbol next to it. Once clicked, we see an option we'll select called "Promote this server to a Domain Controller".

A new configuration window will appear, and we'll select the option named "Add a new forest" and underneath that, next to "Root domain name", we'll put our domain name in. For this project, we'll use 
"mydomain.com" as the name and click next. For the next section, under "Directory Services Restore Mode Password", we'll enter something for the time being like "Password1" and click next. Uncheck the "DNS Delegation" 
option and move to the "Prerequisites Check" and install. The system will restart not long after completing, and once it does, we'll move to the next step.
</p>
<br />

<p>
<h3> Opening Active Directory </h3>
<p float="left">
  <img align="center" src="https://i.imgur.com/MVzPcXz.png" width="49%" /> 
  <img align="center" src="https://i.imgur.com/S4hAyP9.png" width="41%" />
  <img align="center" src="https://i.imgur.com/QUFMFv8.png" width="49%" />
</p>
<p>
6. If we try to connect back to DC-1 with the same credentials we used before, it more than likely won't allow it. This is because the user is now under a new context, and we have to specify what context we would
like to log in as. When we enter our username, we'll need to put the new domain name with a backslash (not a forward slash) before the username.

Example: "mydomain.com\newuser"

Once logged in, we can access Active Directory. Open the Start menu on Windows and find a folder called "Windows Administrative Tools" in the menu. Once opened, we can find and click "Active Directory Users and Computers".

It should open and now we have officially installed Active Directory.
</p>
<br />

