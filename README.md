# Raspberry Pi Lab - Secure Pi Router
In this project I'm turning a Raspberry Pi 5 into a secure wireless network router

<p align="center">
<img src="https://images2.imgbox.com/49/c6/2eoozQu9_o.png" height="50%" width="50%"/>

<h2>Description</h2>

Project consists in: <br> 

- Installing OpenWRT which is an open source Linux-based router
- Using PowerShell to SSH into the Pi and configure the network
- Connect it to the internet
- Setup the USB Wireless Adapter (coming soon)
- Setup VPN (coming soon)
<h2>Environments and Technologies Used</h2>

- <b>Raspberry Pi</b> 
- <b>OpenWRT</b>
- <b>PowerShell</b> 
- <b>Windows 11</b>
- <b>usb adapter</b>

<h2>Deployment and configuring walk-through:</h2>


First we are starting with the creation of our network named Labnetwork with an address range of 192.168.0.0/23 which will give our network a total of 512 addresses. We will split our network in 2 subnets. The first range 192.168.0.0/24 will be dedicated to the servers and the other one 192.168.1.0/24 to the hosts.<br/>
 <br/> 
<p align="center">
<img src="https://imgur.com/nFREDwV.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
 <br />
<br />
We then created our virtual machines: Labserver running Windows Server 2022, and Labuser running Windows 10, each auto-configured with their respective public and private IP addresses. :  <br/> <br>
<p align="center">
<img src="https://imgur.com/g73k5Io.png" height="80%" width="80%" /><img src="https://imgur.com/1v2bNZP.png" height="80%" width="80%" />
</p>
<br />
<br />
Next, we'll configure the server's private IP address to use a static assignment instead of a dynamic one. <br/> <br>
<p align="center">
<img src="https://imgur.com/Xz2d4oW.png" height="80%" width="80%" />
</p>
<br />
<br />
We’ll do this by creating a new network interface (NIC) for the Labserver machine. We'll select the intended subnet, set the IP assignment to 'Static', and assign the address 192.168.0.5.<br/><br>
<p align="center">
<img src="https://imgur.com/hKZi2DZ.png" height="80%" width="80%" />
</p>
<br />
<br />
After creating it, we'll edit the IP configuration of our new NIC to associate a public IP address provided by Azure.  <br/><br>
<p align="center">
<img src="https://imgur.com/CwqSzsx.png" height="80%" width="80%" />
<p/>
<br />
<br />
Next, we’ll access each VM and run ipconfig to verify their network configurations. <br/><br>
<p align="center">
<img src="https://imgur.com/8ceD2Oq.png" height="80%" width="80%" /><img src="https://imgur.com/XZ8GLlW.png" height="80%" width="80%" />
</p>
<br />
<br />
To ensure connectivity we will try to ping Server from the Client. At first the ping will not work correctly. We need to create a new firewall rule on Labserver-DC to allow ICMPv4 traffic, both inbound and outbound, and then repeat the same for the Client VM. Now we can ping Labserver-DC successfully from Labuser. <br/><br>
<p align="center">
<img src="https://imgur.com/t5loLK2.png" height="80%" width="80%" /><img src="https://imgur.com/RkefCsw.png" height="80%" width="80%" /><img src="https://imgur.com/lLF6vrL.png" height="80%" width="80%" />
</p>
<br />
<br />
Next, we'll configure the IP settings on the Server machine and set the DNS server to point to itself. And we will also change the DNS on our VNET in Azure. <br/><br>
<p align="center">
<img src="https://imgur.com/bz6NnBn.png" height="40%" width="40%" /> <br> <img src="https://imgur.com/iNnAHp7.png" height="40%" width="40%" />
</p>
<br />
<br />


At this stage, we’ll go on the Server machine, open an elevated PowerShell and we will use the command **Install-WindowsFeature AD-Domain-Services -IncludeManagementTools** to install Active Directory Domain Services. And then we will use **Install-ADDSForest -DomainName Labdomain.loc -InstallDNS** to set up a new forest. As you see we named our domain **Labdomain.loc". Once these steps are completed, we will reboot the system and log back in. Afterward, we can run the **Get-ADDomain** command to verify that our new domain has been successfully created. Additionally, we can confirm its presence in Active Directory Users and Computers.<br/><br>
<p align="center">
<img src="https://imgur.com/FYyEF6R.png" height="80%" width="80%" /><img src="https://imgur.com/3MLi6Yb.png" height="80%" width="80%" /><img src="https://imgur.com/IE21nLp.png" height="80%" width="80%" /><img src="https://imgur.com/RqAkBxL.png" height="80%" width="80%" /><img src="https://imgur.com/Dmo7KvE.png" height="80%" width="80%" />
</p>


<br />
<br />
 
Before we move to the client machine we will create a new user named Jimmy. <br/><br>
<p align="center">
<img src="https://imgur.com/bLbF68X.png" height="80%" width="80%" /><img src="https://imgur.com/76eDBgZ.png" height="40%" width="40%" />
</p>
<br />
<br />

Now, returning to the client machine, we will configure its IPv4 settings. This machine will continue to receive its private IP via DHCP, but we have to change its DNS server IP address to point to the right DNS server in order to find the domain (192.168.0.5). To confirm everything is working, we’ll ping Labdomain.loc, and it should respond with the Domain Controller's IP address like in the image below. <br/><br>
<p align="center"> 
<img src="https://imgur.com/4NXP4bp.png" height="40%" width="40%" /><img src="https://imgur.com/KDewTdv.png" height="80%" width="80%" />
</p>
<br />
<br />



We’re now ready to join the domain. In System Properties, under the Computer Name tab, we’ll click *Change*, select ‘Domain,’ and enter **Labdomain.loc**. We need credentials that have the permission to join the domain, so in this case we will use our administrator account from the Server machine. We will reboot the machine after. Excellent! The server will now be able to see this client Computer.<br/> <br>
<p align="center">
<img src="https://imgur.com/Crlzbk5.png" height="40%" width="40%" /><img src="https://imgur.com/eSDVpvO.png" height="40%" width="40%" /><img src="https://imgur.com/SLF0zBt.png" height="40%" width="40%" /><img src="https://imgur.com/VEoEEUB.png" height="80%" width="80%" />
</p>
<br />
<br />

In the final stage of our project, after restarting, we’ll log in with our Server Admin account and add Jimmy to the Remote Desktop Users group, allowing us to log through him remotely. We'll open Computer Management and navigate to *Local Users and Groups* > *Groups* > *Remote Desktop Users*. Then, click *Add*, search for Jimmy (the user we created in the domain), and click *OK* > *Apply* > *OK*. Finally, we'll sign out and attempt to log back in using Jimmy's account.<br/> <br>
<p align="center">
<img src="https://imgur.com/RL1lFIq.png" height="40%" width="40%" /><img src="https://imgur.com/wxOokOL.png" height="80%" width="80%" /><img src="https://imgur.com/gorMkr0.png" height="80%" width="80%" /><img src="https://imgur.com/EZWMn1N.png" height="80%" width="80%" />
</p>
<br />
<br />

Wonderful! As you can see, we successfully logged in remotely using Jimmy from the Labuser machine. This marks the completion of our domain setup — everything is up and running smoothly. 🚀<br/><br>
<p align="center">
<img src="https://imgur.com/vu0lbTm.png" height="40%" width="40%" /><img src="https://imgur.com/meT6j2P.png" height="80%" width="80%" /><img src="https://imgur.com/MAPR82K.png" height="80%" width="80%" />
</p>
<br />
<br />

**THE END**
 <!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
