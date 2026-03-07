# Raspberry Pi Lab - Secure Pi Router


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
Now I'm ready to start configure the router, so I open PowerShell (CMD will work just as fine) on my PC and establish an SSH connection to the Raspberry Pi by running the command ssh root@192.168.1.1, accepting the host key fingerprint if prompted for the first time.
 <br/><br>
<p align="center">
<img src="https://i.imgur.com/1IN6lQf.png" height="80%" width="80%" />
</p>
<br />
<br />
I follow up by securely set a new root password for the OpenWrt system by running the passwd command, entering a strong password twice as prompted, which replaces the default insecure credentials and enhances initial access protection.
<br/><br>
<p align="center">
<img src="https://i.imgur.com/SnRbPUe.png" height="80%" width="80%" />
</p>
<br />
<br />
I navigate to the configuration directory by running cd /etc/config in the SSH session, then use the ls command to list and display the contents, revealing the standard OpenWrt configuration files such as network, dhcp, firewall, wireless, and system that control the device's core settings and services.
<br>
<p align="center">
<img src="https://i.imgur.com/Vb8esvE.png" height="80%" width="80%" />
</p>
<br />
<br />
I'm going to back up some files using the cp command followed by the file I want to backup and then the name of the file that I want my backup to be named after. Checking my backup work with ls thereafter.<br/>
<p align="center"> 
<img src="https://i.imgur.com/lA5BmTr.png" height="80%" width="80%" />
</p>
<br />
<br />
I proceed to edit the network configuration file by navigating to /etc/config and launching vi on the network file with the command vi network, preparing to modify the LAN interface settings for customized IP addressing and connectivity.
<br/> <br>
<p align="center">
<img src="https://i.imgur.com/l8WHzar.png" height="80%" width="80%" />
</p>
<br />
<br />
I enter insert mode in vi by pressing the i key, then modify the LAN interface section for enhanced security by changing the IP address to 10.155.155.60, add the line option force_link '1' to ensure the interface remains active even without a physical link, subsequently append a new WWAN interface configuration with config interface 'wwan', option proto 'dhcp', option peerdns '0', and option dns '1.1.1.1 9.9.9.9' to enable upstream Internet access via DHCP with custom DNS servers, and finally add a VPN client interface stub using config interface 'vpnclient', option ifname 'tun0', and option proto 'none' to prepare for future tunneled routing.
<p>To save and exit vi I'm goinng to press ESC followed by writing :wq  and hit Enter</p> <br>
<p align="center">
<img src="https://i.imgur.com/H5AL0V7.png" height="50%" width="50%" /><img src="https://i.imgur.com/nM9ob8l.png" height="50%" width="50%" />
</p>
<br />
<br />
I proceed to edit the firewall configuration file by launching 'vi on /etc/config/firewall' and, within the WAN zone section, change the option input 'reject' directive to option input 'accept' to permit incoming traffic on the WAN interface for better accessibility during the initial setup. I'm using the same combo to save and exit i.e. ESC :wq Enter, after which I'm going to reboot the Pi by writing reboot and hit Enter.
<br/><br>
<p align="center">
<img src="https://i.imgur.com/SbUVl8I.png" height="50%" width="50%" /><img src="https://i.imgur.com/qvLm7B6.png" height="50%" width="50%" />
</p>
<br />
<br />
We note that the updated LAN IP address (now 10.155.155.60) and the newly added WWAN interface with DHCP support will alter future connection methods—requiring me to switch my Windows Ethernet adapter back to automatic (DHCP) settings for seamless access via the Pi's dynamic upstream link.
<br/><br>
<p align="center">
<img src="https://i.imgur.com/eqm15M3.png" height="80%" width="80%" />
</p>
<br />
<br />
I verify the updated network configuration on my Windows PC by running the 'ipconfig' command, confirming that DHCP from the Raspberry Pi has assigned it the IP address 10.155.155.170 within the new subnet, then establish a fresh SSH connection to the Pi by running ssh root@10.155.155.60 using the updated gateway address.
<br/><br>
<p align="center">
<img src="https://imgur.com/K3f65Zk.png" height="50%" width="50%" /><img src="https://i.imgur.com/TEohuEo.png" height="50%" width="50%" />
</p>
<br />
<br />

Next I open the wireless configuration file by running 'vi /etc/config/wireless' (if you're not in /etc/config already) and change the line option disabled '1' to option disabled '0' to enable the WiFi radio on the Raspberry Pi's built-in wireless adapter. Then ESC :wq ENTER. After that I write 'uci commit wireless' and 'wifi' and after this if I check my phone I can see that the Pi is broadcasting OpenWRT.
<br/><br>
<p align="center">
<img src="https://i.imgur.com/giL1maZ.png" height="50%" width="50%" /><img src="https://i.imgur.com/wPZJ8bw.png" height="50%" width="50%" /><img src="https://i.imgur.com/QVlZtet.png" height="20%" width="20%" />
</p>
<br />
<br />

At this point I'm navigating to the GUI (luci) by opening a browser and typing the IP Address 10.155.155.60 and hit Enter then log in with the same password. 
I need to mention that all our configs can be set here in this GUI but most of the time I prefer to use the command line.
Here I will navigate to Network > Wireless and click SCAN. From here I will pick my home wireless network to connect the Raspberry Pi to the internet with it.
<br/><br>
<p align="center">
<img src="https://i.imgur.com/5JJhtrt.png" height="50%" width="50%" /><img src="https://i.imgur.com/PA1wssk.png" height="50%" width="50%" /><img src="https://imgur.com/BHPq8VB.png" height="50%" width="50%" /><img src="https://imgur.com/biDfXcy.png" height="50%" width="50%" />
</p>
<br />
<br />

I check the box to replace the wirless configuration and then I Enter my credentials and click submit and then save.
<br/><br>
<p align="center">
<img src="https://i.imgur.com/B7ferwf.png" height="80%" width="80%" /><img src="https://i.imgur.com/gBViPTA.png" height="80%" width="80%" />
</p>
<br />
<br />

Now I can see that my interface is connected to the SSID
<br/><br>
<p align="center">
<img src="https://i.imgur.com/lVtLM6U.png" height="80%" width="80%" /><img src=".png" height="80%" width="80%" />
</p>
<br />
<br />
<br/><br>
<p align="center">
<img src=".png" height="80%" width="80%" /><img src=".png" height="80%" width="80%" />
</p>
<br />
<br />
<br/><br>
<p align="center">
<img src=".png" height="80%" width="80%" /><img src=".png" height="80%" width="80%" />
</p>
<br />
<br />
<br/><br>
<p align="center">
<img src=".png" height="80%" width="80%" /><img src=".png" height="80%" width="80%" />
</p>
<br />
<br />
<br/><br>
<p align="center">
<img src=".png" height="80%" width="80%" /><img src=".png" height="80%" width="80%" />
</p>
<br />
<br />
<br/><br>
<p align="center">
<img src=".png" height="80%" width="80%" /><img src=".png" height="80%" width="80%" />
</p>
<br />
<br />
<br/><br>
<p align="center">
<img src=".png" height="80%" width="80%" /><img src=".png" height="80%" width="80%" />
</p>
<br />
<br />

**TO BE CONTINUED**
 <!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
