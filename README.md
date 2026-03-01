# Raspberry Pi Lab - Secure Pi Router
In this project I'm turning a Raspberry Pi 5 into a secure wireless network router

<p align="center">
<img src="https://images2.imgbox.com/49/c6/2eoozQu9_o.png" height="80%" width="80%"/>

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


I'm going to initialise this project by connecting the Raspberry Pi to my PC through an RJ-45 Ethernet cable.
 <br/> 
<p align="center">
<img src="https://i.imgur.com/1rsHriM.jpeg" height="50%" width="50%"/><img src="https://i.imgur.com/EqfRjAd.jpeg" height="50%" width="50%"/>
</p>
 <br />
<br />
Next, I prepare the microSD card by inserting it into my PC.
<br/> <br>
<p align="center">
<img src="https://i.imgur.com/l8Vht60.jpeg" height="50%" width="50%" />
</p>
<br />
<br />
I navigate to the official OpenWrt website, locate and download the appropriate firmware (OpenWRT image) compatible with my specific Raspberry Pi model. https://firmware-selector.openwrt.org/?version=24.10.5 Factory EXT 4 is the one we need.
<br/> <br>
<p align="center">
<img src="https://i.imgur.com/55JV5p7.png" height="80%" width="80%" />
</p>
<br />
<br />
Now I'm ready to flash the firmware onto the microSD card using a tool such as DaemonTools (Raspberry Pi Imager or balenaEtcher), ready for insertion into the Raspberry Pi.
<p>
I launch the flashing tool, select "Write to USB drive," choose the downloaded OpenWrt image file, verify that the target device is my microSD card, initiate the write process by clicking Start, and clicking "Close" once the flashing operation completes successfully.
<p/>
<p align="center">
<img src="https://i.imgur.com/WDJb7cf.png" height="48%" width="48%" /><img src="https://i.imgur.com/e3h5Naf.png" height="52%" width="52%" /><img src="https://i.imgur.com/Ma5nO90.png" height="60%" width="60%" /><img src="https://i.imgur.com/7Sc0C7u.png" height="40%" width="40%" />
</p>
<br />
<br />
Next I insert the prepared microSD card into the Raspberry Pi and power it on to initiate the boot process with the flashed OpenWrt firmware.
The Pi will have, by default, an IP Address of 192.168.1.1. To be able to access it I'm going to give my PC's ethernet port an IP address of 192.168.1.12.
<p>To do this I'm opening Control Panel, select "View network status and tasks", find my Ethernet port (Network 20 in my case) and select "Local Area Network".</p>
<p align="center">
<img src="https://i.imgur.com/F6tLLxk.png" height="80%" width="80%" />
<p/>
<br />
<br />
Here I'm going to click "Properties", select IPv4(TCP/IPv4) and open its Properties. <br/><br>
<p align="center">
<img src="https://i.imgur.com/ThhSHkx.png" height="40%" width="40%" /><img src="https://i.imgur.com/6AM0Edb.png" height="40%" width="40%" />
</p>
<br />
<br />
I'm switching from automatic to manual and I'm typing the IP address I previously designated for this adapter, the subnet mask and the default gateway which is the PI's IP Address. OK>OK>Close.<br/><br>
<p align="center">
<img src="https://i.imgur.com/ZjiJgph.png" height="50%" width="50%" />
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
