<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of Active Directory within Azure Virtual Machines.<br />


<!--- <h2>Video Demonstration</h2>(coming soon!) -->

<!--- - ### [YouTube: How to Deploy Active Directory within Azure Compute](https://www.youtube.com)(coming soon!) -->

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Computer)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Setup resources in Azure.
- Ensure Connectivity between client and domain.
- Install Active Directory (AD).
- Create admin and normal user.
- Join client to domain.
- Setup remote desktop for non-admins.
- Create additional users.

<h2>Deployment and Configuration Steps</h2>

#### Part 1 (Setting Up Resources)
- Setup up VMs in Azure.
    - Create the Domain Controller VM (Windows Server 2022) named “DC-1”.
        - Take note of the Resource Group and Virtual Network (Vnet) that get created at this time.
    - Set Domain Controller’s NIC Private IP address to be static.
    - Create the Client VM (Windows 10) named “Client-1”. Use the same Resource Group and Vnet that was created in Step 1.a.
    - Ensure that both VMs are in the same Vnet (you can check the topology with Network Watcher.
- Check Connectivity between client and domain controller.
    - Login to Client-1 with Remote Desktop and ping DC-1’s private IP address with ping -t <ip address> (perpetual ping).
    - Login to the Domain Controller and enable ICMPv4 in on the local windows Firewall.
    - Check back at Client-1 to see the ping succeed.
- Install Active Directory (AD)
    - Login to DC-1 and install Active Directory Domain Services.
    - Promote as a DC: Setup a new forest as mydomain.com (this can be named anything, just remember what it is.).
    - Restart and log back into DC-1 as user: mydomain.com\labuser. (or whatever it is you named the domain and your user.).
</br>

#### AD Resources Created.
<p>
<img src="https://i.imgur.com/apEK4rZ.png" height="50%" width="50%" alt="AD Resources."/>
</p>

#### DC-1 Private IP set to Static.
<p>
<img src="https://i.imgur.com/55khESV.png" height="50%" width="50%" alt="DC-1 Static IP."/>
</p>

#### ICMPv4 Enabled on DC-1.
<p>
<img src="https://i.imgur.com/0M1voUT.png" height="50%" width="50%" alt="ICMPv4 DC-1."/>
</p>

#### Client Perpetual Ping to DC-1 Checking Connectivity.
<p>
<img src="https://i.imgur.com/oeDzEoD.png" height="50%" width="50%" alt="Connectivity established between Client and DC-1."/>
</p>

#### Getting Ready to Install AD.
<p>
<img src="https://i.imgur.com/qpwHG2t.png" height="50%" width="50%" alt="Installing AD."/>
</p>

#### AD Installed.
<p>
<img src="https://i.imgur.com/jPsS05b.png" height="50%" width="50%" alt="AD Installed."/>
</p>

#### DC-1 Promoted.
<p>
<img src="https://i.imgur.com/Cv6dleq.png" height="50%" width="50%" alt="DC-1 Promoted."/>
</p>

</br>

#### Part 2 (Setting up AD Environment)
- Create Admin User account in AD.
    - In Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called "_EMPLOYEES".
    - Create a new OU named "_ADMINS".
    - Create a new employee in "_ADMINS" (set the name and password to something you'll remember)
        - A note when setting the password, remember to uncheck the box requiring employee to change the password on login. 
    - Add new employee to the "Domain Admins" Security Group.
    - Logout/close the Remote Desktop connection to DC-1 and log back in as newly created admin.(e.g. mydomain.com\jane_admin).
    - Use this new use as your admin from now on.
- Join Client to your domain.
    - From Azure Portal, set Client-1's DNS setting to the DC's Private IP address.
    - From Azure Portal, restart Client-1.
    - Login to Client-1 (Remote Desktop) as the original local admin (whatever you set it up as originally) and join it to the domain. (computer will restart.).
    - Login to the Domain Controller (Remote Desktop) and verify Client-1 shows up in the Active Directory Users and Computers (ADUC) inside the "Computers" container on the root of the domain.
    - Create a new OU named "_CLIENTS" and drag Client-1 into there.
- Setup Remote Desktop for non-administrative users on Client-1.
    - Log into Client-1 as (whatever admin you made for the domain) and open system properties.
    - Click "Remote Desktop".
    - Allow "domain users" access to the remote desktop.
    - You can now log into Client-1 as a normal, non-administrative user.
    - Normally you'd want to do this with Group Policy that allows you to change MANY systems at once, however, this is merely a sample.
</br>

#### New Employee Created and Added to Domain Admin Group.
<p>
<img src="https://i.imgur.com/KifVqdH.png" height="50%" width="50%" alt="User created and added to domain admin group."/>
</p>

#### Showing the Newly Created Admin Logged into DC-1.
<p>
<img src="https://i.imgur.com/gZEhjz3.png" height="50%" width="50%" alt="newly created admin logged in."/>
</p>

#### Client-1 DNS Set to DC-1 Private IP address.
<p>
<img src="https://i.imgur.com/3O3Xd9e.png" height="50%" width="50%" alt="Client-1 using DC-1 Private IP as DNS."/>
</p>

#### ipconig /all displaying Client-1 Using DC-1 Private IP address as DNS.
<p>
<img src="https://i.imgur.com/zJHYCV4.png" height="50%" width="50%" alt="CMD line interface of Client-1 using DC-1 Private IP as DNS."/>
</p>

#### Client-1 Showing up as being linked to DC-1.
<p>
<img src="https://i.imgur.com/1CZGHpX.png" height="50%" width="50%" alt="Client-1 and DC-1 Linked."/>
</p>

#### Newly Created Admin from DC-1 is logged into Client-1.
<p>
<img src="https://i.imgur.com/tJYS2OC.png" height="50%" width="50%" alt="New Admin logged into Client-1."/>
</p>

#### All Domain Users Being Granted Access to Client-1.
<p>
<img src="https://i.imgur.com/ksPU4IR.png" height="50%" width="50%" alt="Domain Users Granted Access to Client-1."/>
</p>

</br>

#### Part 3 (Creating Users and Logging in as them)
- Create a bunch of additional users and attempt to log into client-1 with them.
    - Login to DC-1 as the Admin you created.
    - Open PowerShell_ise (as an administrator).
    - Create a new File and paste the contents of the script into it.
        - (https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)
    - Run the script and observe the accounts being created.
    - When finished, open ADUC and observe the accounts in the appropriate OU.
    - Attempt to login to Client-1 with one of the accounts. (take note of what you set the password to be in the script).
</br>

#### Logged into DC-1 with Powershell_ise Opened.
<p>
<img src="https://i.imgur.com/XmN5vtr.png" height="50%" width="50%" alt="Powershell opened with script ready to go."/>
</p>

#### Script in Powershell Running/New Users Being Generated.
<p>
<img src="https://i.imgur.com/HSv4n6R.png" height="50%" width="50%" alt="New Users Being Generated."/>
</p>

#### New Users Showing Up in ADUC.
<p>
<img src="https://i.imgur.com/JkDYpSp.png" height="50%" width="50%" alt="New Users."/>
</p>

#### Newly Created User Logged into Client-1.
<p>
<img src="https://i.imgur.com/nn8KrhA.png" height="50%" width="50%" alt="New User Login."/>
</p>

#### Admin View of New Users Account Control.
<p>
<img src="https://i.imgur.com/uev8cfT.png" height="50%" width="50%" alt="Account Control of New Users."/>
</p>








