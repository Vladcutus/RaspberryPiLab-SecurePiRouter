# Raspberry Pi Lab - Secure Pi Router


<p align="center">
<img src="https://images2.imgbox.com/49/c6/2eoozQu9_o.png" height="80%" width="80%"/>

<h2>Description</h2>

Project consists in: <br> 

- Installing OpenWRT which is an open source Linux-based router
- Using PowerShell to SSH into the Pi and configure the network
- Connect it to the internet
- Setup the USB Wireless Adapter
- Setup VPN
<h2>Environments and Technologies Used</h2>

- <b>Raspberry Pi</b> 
- <b>OpenWRT</b>
- <b>PowerShell</b> 
- <b>Windows 11</b>
- <b>NETGEAR WiFi Adapter (A7500)</b>

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
Now I'm ready to start configure the router, so I open PowerShell (CMD will work just as fine) on my PC and establish an SSH connection to the Raspberry Pi by running the command 'ssh root@192.168.1.1', accepting the host key fingerprint if prompted for the first time.
 <br/><br>
<p align="center">
<img src="https://i.imgur.com/1IN6lQf.png" height="80%" width="80%" />
</p>
<br />
<br />
I follow up by securely set a new root password for the OpenWrt system by running the 'passwd' command, entering a strong password twice as prompted, which replaces the default insecure credentials and enhances initial access protection.
<br/><br>
<p align="center">
<img src="https://i.imgur.com/SnRbPUe.png" height="80%" width="80%" />
</p>
<br />
<br />
I navigate to the configuration directory by running 'cd /etc/config' in the SSH session, then use the 'ls' command to list and display the contents, revealing the standard OpenWrt configuration files such as network, dhcp, firewall, wireless, and system that control the device's core settings and services.
<br>
<p align="center">
<img src="https://i.imgur.com/Vb8esvE.png" height="80%" width="80%" />
</p>
<br />
<br />
I'm going to back up some files using the 'cp' command followed by the file I want to backup and then the name of the file that I want my backup to be named after. Checking my backup work with 'ls' thereafter.<br/>
<p align="center"> 
<img src="https://i.imgur.com/lA5BmTr.png" height="80%" width="80%" />
</p>
<br />
<br />
I proceed to edit the network configuration file by navigating to /etc/config and launching 'vi' on the network file with the command 'vi network', preparing to modify the LAN interface settings for customized IP addressing and connectivity.
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

Now we can see that my interface is connected to my home internet. And in the following image we can see that my wireless home router gave it an IP address of 10.10.255.214 with a subnet mask of 255.255.0.0.
<br/><br>
<p align="center">
<img src="https://i.imgur.com/lVtLM6U.png" height="80%" width="80%" /><img src="https://i.imgur.com/HiG5QzI.png" height="80%" width="80%" />
</p>
<br />
<br />

To prove that it works I'm going to ping cisco.com from the SSH window:
<br/><br>
<p align="center">
<img src="https://i.imgur.com/eAo8PN6.png" height="80%" width="80%" />
<br />
<br />

For the second WI-FI interface I will use the NETGEAR Nighthawk WiFi 6 USB 3.0 Adapter (A7500) plugged into the Raspberry Pi, which uses the Mediatek MT7921AU chipset fully supported in-kernel since Linux 5.18 (running kernel 6.6), allowing OpenWrt to recognize and activate the adapter for wireless operation.
<br/><br>
<p align="center">
<img src="https://i.imgur.com/Y82Z3j7.png" height="80%" width="80%" />
</p>
<br />
<br />

I continue by installing the required packages by running the command: 'opkg update' then 'opkg install kmod-mt7921u usbutils openvpn-openssl luci-app-openvpn nano' which pulls in the in-kernel driver for the MT7921AU WiFi 6 adapter, USB utilities, OpenVPN with OpenSSL support, the LuCI web interface for OpenVPN, and the nano text editor for easier configuration.
<br/><br>
<p align="center">
<img src="https://i.imgur.com/JJCZy0f.png" height="80%" width="80%" />
</p>
<br />
<br />

Checking what is going on at the USB side with the command 'lsusb -t' and confirm that the MT7921AU WiFi 6 adapter has been successfully recognized and bound to the kmod-mt7921u driver, showing the expected driver attachment under the USB bus.
<br/><br>
<p align="center">
<img src="https://i.imgur.com/amJ9hmf.png" height="80%" width="80%" />
</p>
<br />
<br />
Here I ran into an error when I tried to bring it up and running, namely "SIOCGIFFLAGS: No such device". I checked with 'ifconfig' and it wasn't there. After a short research using AI, I realised that a new interface had to be created manually. So I typed 'iw phy phy1 interface add wlan1 type managed' and I checked with 'ip link show wlan1'.
<p></p>Afterwards I typed 'ip link set wlan1 up' to make it alive and after I checked again I could see the word 'UP' in the details.</p>
<br/><br>
<p align="center">
<img src="https://i.imgur.com/rTmuvjU.png" height="80%" width="80%" /><img src="https://i.imgur.com/HYZOKeS.png" height="80%" width="80%" />
</p>
<br />
<br />

I also found this cat command on the internet to make my work survive reboot:
cat > /etc/rc.local << 'EOF'
#!/bin/sh
modprobe mt7921u
sleep 2
echo '0846 9065' > /sys/bus/usb/drivers/mt7921u/new_id
sleep 3
iw phy phy1 interface add wlan1 type managed
ip link set wlan1 up
exit 0
EOF
<p>After that I ran 'chmod +x /etc/rc.local' to make the file executable. And then rebooted.</p>
<p align="center">
<img src="https://i.imgur.com/eCOVeLs.png" height="80%" width="80%" />
</p>
<br />
<br />
After the rebbot I went to edit the wireless config file using the 'nano' instead of 'vi' this time: 'nano /etc/config/wireless'
<p>And inside I changed the band from 6g to 5g, htmode to VHT80, disabled to '0', replaced that openwrt ssid with my custom ssid and setup my custom password with psk2 encryption</p>
<p>To save, type CTRL+X Y ENTER</p>
<p align="center">
<img src="https://i.imgur.com/cXofSnv.png" height="50%" width="50%" /><img src="https://i.imgur.com/xJlzIvE.png" height="50%" width="50%" />
</p>
<br />
<br />
<p>Then I have to write 'uci commit wireless' and 'wifi up radio1' to bring it up and running.</p>
<img src="https://i.imgur.com/nASWM1A.png" height="80%" width="80%" />





To demonstrate that it's working and it's broadcasting I'm checking my phone and I cand see my custom ssid. I put my designated password in and then I could see that I got an IP Address of 10.155.155.204 on my particular subnet.
<br/><br>
<p align="center">
<img src="https://i.imgur.com/uMgW4Xa.png" height="20%" width="20%" /><img src="https://i.imgur.com/TkG2Vui.png" height="33%" width="33%" /><img src="https://i.imgur.com/INo63On.png" height="33%" width="33%" />
</p>
<br />
<br />

The FINAL STEP in this project is to set up VPN, so I navigate to vpnbook.com, select a United Kingdom OpenVPN server, and download the corresponding .ovpn configuration file to enable full-tunnel routing of all traffic through the VPN.
<p align="center">
<img src="https://i.imgur.com/2TaQ3EE.png" height="50%" width="50%" /><img src="https://i.imgur.com/sn0XTMX.png" height="50%" width="50%" />
</p>
<br />
<br />

I open Command Prompt, navigate to my Downloads folder, and execute the command 'scp -O vpnbook-uk205-udp53.ovpn root@10.155.155.60:/etc/openvpn/client.conf' to securely copy the downloaded OpenVPN configuration file to the Raspberry Pi, using the -O flag to force the legacy SCP protocol after encountering an SFTP-related error due to OpenWrt’s minimal SSH server. I used AI help to find the solution, tested it and it worked.
<br/><br>
<p align="center">
<img src="https://i.imgur.com/C1E11a1.png" height="80%" width="80%" /><img src="https://i.imgur.com/CwQCQ8T.png" height="80%" width="80%" />
</p>
<br />
<br />

Next, I made sure I have the packages installed by running 'opkg update'   'opkg install luci-app-openvpn' and '/etc/init.d/rpcd restat'.
<p>Then I configured 4 parameters for openvpn, specifically DIR, ID, USER, PASS. Vpnbook provided me with the username and password that I put in here</p>
<p align="center">
<img src="https://i.imgur.com/2RWqTA1.png" height="50%" width="50%" /><img src="https://imgur.com/iPZXk0y.png" height="50%" width="50%" />
</p>
<br />
<br />

Immediately after I copy and pasted 5 sets of commands from the notepad. [Dropbox link](https://www.dropbox.com/scl/fi/vpy2ce3qcfqlaet836dj1/OpenVPN-OpenWRT-setup-commands.txt?rlkey=13yl6jg60xvz13hktokdphqus&st=87pdab4b&dl=0).
<br/><br>
<p align="center">
<img src="https://i.imgur.com/JDB4o5J.png" height="80%" width="80%" />
</p>
<br />
<br />

I'm navigating to the GUI in the web browser. To demonstrate that VPN is working I'm going to navigate to VPN > OpenVPN dropdown menu and Im going to stop the client and looking which is the public IP address.
<br/><br>
<p align="center">
<img src="https://i.imgur.com/Hqt0si2.png" height="80%" width="80%" /><img src="https://i.imgur.com/ZXgbvvC.png" height="20%" width="20%" />
</p>
<br />
<br />
And then turning the client back on and checking the public IP address again. We can see that VPN worked.
<br/><br>
<p align="center">
<img src="https://i.imgur.com/yGoCfBx.png" height="80%" width="80%" /><img src="https://i.imgur.com/whV6Lhg.png" height="20%" width="20%" />
</p>
<br />
<br />
Now I have a Raspberry Pi Wi-Fi router with 2 Wi-FI adapters -a client and an access point- that filters traffic through VPN.
<br/><br>
<p align="center">
<img src="https://i.imgur.com/TuTG8e6.png" height="80%" width="80%" />
<br />
<br />

 <!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
