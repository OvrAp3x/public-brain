---
{"dg-publish":true,"permalink":"/windows/install-windows-via-ssh/"}
---

With pictures:
[[xwiki_einar_install+windows+via+ssh_webhome.pdf]]

Welcome to my Kimsufi Windows Install Guide. OVH have some very cheap entry level dedicated servers which are branded under the Kimsufi name. The KS1 server which is currently £5 a month including VAT gets you a:

-   Atom N2800 @1.86ghz Processor (2 cores, 4 threads)
-   2GB DDR3 ram
-   500GB sata hard drive
-   100mbps connection
-   1x IP V4 address
-   Alternatively for £10 a month you can get the same server with 4GB of ram and a 1TB hard drive (KS2 Server)

The only choice of OS for these server however is limited to Linux, OVH used to let you use your own Windows licence, however this is not an option any more.

Not all is lost however, all OVH / Kimsufi servers offer a Linux based rescue system. This is where the servers hard drive is essentially mounted inside a Linux Live CD so you can perform diagnostics or troubleshoot any problems. As the OVH rescue system system gives direct access to the servers hard drive we can use this to deploy a sysprepped Windows install over the internet. Combined with an unattend.xml file this sysprepped Windows install is enough to deploy Windows Server 2012 R2 on the Kimsufi KS-1 and KS-2 servers and have it connected to the internet with Remote Desktop access enabled when the server boots. Esentially think of this tutorial as showing you how to install Windows on a server, remotely over the internet.

**In this Kimsufi Windows Install tutorial we are going to:**

1.  Install Windows Server 2012 R2 in a Virtual Machine
2.  Configure our installation in the Virtual Machine so Remote Desktop is enabled and windows firewall will allow connections to remote desktop from the internet
3.  Sysprep the Windows install, this is so Windows will boot and setup new hardware once deployed to our real Kimsufi server
4.  Still in the Virtual Machine, we will boot a Linux Live CD to image the Windows install with dd.
5.  Finally will deploy the sysprepped Windows image we made with dd over the internet to the Kimsufi server, via the OVH Linux recovery system.

This might sound quite complex, however its actually quite easy when broken down in to steps.

**Some thing to keep in mind:**

You will have to upload at least a 4gb compressed image of a Windows Server install from your home connection to your OVH server. Be aware this can take a long time depending on your internet connection’s upload speed.

For me it took around an hour to upload just over 4gb with a 12mbps upload, if you had a 1 mbps upload speed that could take you well over 8 hours, so be warned that might be the most time consuming part of this tutorial for you.

**Why Install Windows Server on an Intel Atom Based Device?**

You might think why install Windows Server on such an under powered server, especially considering the cost of a Windows Server licence?

Well Microsoft give free Windows Server 2012 R2 licences to students via [Dreamspark](https://www.dreamspark.com/), all you really need is a college / university email address and you can gain access to some great software via Dreamspark. This is where the Kimsufi server comes in, I think its the perfect server to install a copy of Windows Server on, to learn and experiment with the OS. Despite been quite low powered the Atom N2800 is more than capable for this task. The server will have it’s own unique IPv4 and IPv6 address on the internet, so you could deploy an application in the real world using IIS, or even experiment with setting a VPN up. The 2gb of ram is more than enough for this purpose, and to be honest that’s how i ended up starting with this blog. I wanted something to share with the world when I was experimenting with IIS and Windows Server. The 100mbps connection and 500gb of hard drive space are a nice bonus too!

**Before we begin:**

I have tried to make this tutorial as detailed as possible and explain the best I can, providing screenshots of what i’m doing. However some basic knowledge of Windows, Linux, Drivers, Networking and Virtual Machines will be a great benefit to you.

This guide is aimed at the [OVH / Kimsufi KS1 and KS2 Servers](http://www.kimsufi.com/uk/), however the same principles basically apply to any OVH server.

**Kimsufi Windows Install Guide – Install Windows Server 2012 R2 on OVH’s Kimsufi KS1 / KS2 Server:**

When you get the Kimsufi KS1 / KS2 server you will have to choose an OS to install on it. You will get an email with a link to log in the server manager and do this. For the OS I chose Ubuntu Server, we can easily get the hardware, such as what network card is in the server by using Ubuntu. Once Ubuntu is installed you will get an email with details to connect to the server via SSH:

Now you simply want to use [Putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) to SSH in to the server.

Once you have SSH access simply type:

lshw -C network

You should then be able to find out what network card is present in your server. Windows Server 2012 R2 has drivers out the box for the Intel 82574L so we do not need to worry about this later on. However depending on your network card you might need to find drivers for use on Windows.

With the current Kimsufi servers it’s easy to tell which network card the server has by the processor that’s in it, however by knowing the above you can easily find out in the future when the server hardware will no doubt change.

Atom N2800 = Intel 82574L (Both Windows Server 2008 R2 and Windows Server 2012 R2 have drivers natively for this)  
Atom D425 = RTL8101E/RTL8102E (Windows Server 2008 R2 does not have drivers, Windows Server 2012 R2 might) – Drivers can however be downloaded from here: [RTL8100E/RTL8101E/RTL8102E-GR/RTL8103E(L) Drivers](http://www.realtek.com.tw/downloads/downloadsView.aspx?Langid=1&PNid=14&PFid=7&Level=5&Conn=4&DownTypeID=3#1)

**Installing Windows Server 2012 R2 in a Virtual Machine:**

It doesn’t really matter what software you use to run a Virtual Machine. I used VmWare ESXI, however that requires a dedicated machine to run on. [VmWare Player](http://www.vmware.com/uk/products/player) would be fine as the options are similar to ESXi which i use in this tutorial, as would [VirtualBox.](https://www.virtualbox.org/)

You want to create a Virtual Machine with similar specifications to your Kimsufi server and then install Windows on this Virtual Machine. I simply assigned 2GB of ram to the virtual machine and 1x CPU with 4x Cores.

In VmWare ESXi you have the choice between BIOS and UEFI for the Virtual Machine boot options, i chose BIOS as that’s what the Kimsufi server uses. I’m not sure how much this matters anyway when sysprepping a Windows install, however i tried to keep the hardware of the virtual Machine similar to what is in the real Kimsufi server.

Now install Windows Server 2012 R2 as you normally would in the Virtual Machine.

Use one of the following default keys to allow Windows to install, however not activate:

**Datacenter:** Y4TGP-NPTV9-HTC2H-7MGQ3-DV4TW  
**Standard:** DBGBW-NPF86-BJVTX-K3WKJ-MTB6V  
**Essentials:** K2XGM-NMBT3-2R6Q8-WF2FK-P36R2

You do not want to use your legit and valid key until you have Windows running on your Kimsufi server, otherwise you will end up activating the Virtual Machine, which will cause you issues with activation later on.

Once Windows Server 2012 R2 is installed you can set an Administrator password (remember this!). You then want to do a couple of things before we sysprep the image for deployment on the Kimsufi server.

**First enable remote desktop:**

Right click on Computer > Properties > Advanced System Properties > Remote

Then enable “Allow remote connections to this computer”:

**Allow Remote Desktop Connections in Windows Firewall:**

Go to Control Panel > Windows Firewall > Advanced Settings and enable:

-   Remote Desktop – User Mode (TCP-In)
-   Remote Desktop – User Mode (UDP-In)
-   Remote Desktop – Shadow (TCP-In) (This is optional)

Do this for both public and domain profiles to ensure Windows Firewall does not lock us out the server later on.

**Allow the server to be pinged in Windows Firewall:**

Go to Control Panel > Windows Firewall > Advanced Settings and enable:

-   File and Printer Sharing (Echo Request) IMCP V4 In
-   File and Printer Sharing (Echo Request) IMCP V6 In

This is handy so we can ping the server and is required if you wish for the OVH manager to monitor the server.

**Disable Windows Error Recovery On Startup:**

Disabling Windows Error Recovery will prevent the system getting stuck when starting up.

Open a Command Prompt with Administrator rights (Right Click > Run as Administrator) and type:

bcdedit /set {current} bootstatuspolicy ignoreallfailures

**Setup the unattend.xml file:**

The easiest thing to do here is download my [unattend.xml](http://matthill.eu/wp-content/uploads/2014/12/unattend.zip) file and edit it as required in [Notepad++](http://notepad-plus-plus.org/). This unattend.xml file is for the English language version of Windows Server 2012 R2 and will probably not work with other versions of Windows.

Essentially if you have a non English language version of Windows Server you will need to edit the:

-   InputLocale
-   SystemLocale
-   UILanguage
-   UserLocale

You would change this to match the language of your copy of Windows Server 2012 R2. You can’t leave the settings as they are for a French version of Windows for example.

If you have the English version of Windows Server 2012 R2 all you need to edit here is the password for the Administrator account, set this to be the same as the password currently is currently on your Virtual Machine:

Now copy the unattend.xml to **c:\windows\panther** and **c:\windows\system32\sysprep**

You are now basically ready to sysprep your Windows Install to be deployed to the Kimsufi server. Now you can do any other customizations you wish to make can be made. However keep in mind if you start installing apps, updating Windows or setting things up, the larger the image you have to upload will be. My advice would be to update Windows and install things once you have deployed the image to the Kimsufi server.

If you need to install additional drivers for your server now would be the time to do it:

If you get a Kimsufi server at the moment with the Atom N2800 processor I can confirm that you do not need to install any additional drivers for the Kimsufi KS1 and KS2 server to get Windows Server 2012 R2 setup and working.

**Syspreping our Windows Server 2012 R2 install:**

Sysprep can be used to prepare an operating system for disk cloning and restoration via a disk image.

Windows operating system installations include many unique elements per installation that need to be “generalized” before capturing and deploying a disk image to multiple computers. Some of these elements include:

-   Computer name
-   Security Identifier (SID)
-   Driver Cache

Sysprep solves these issues by allowing the generation of new computer names, unique SIDs, and custom driver cache databases during the Sysprep process.

To sysprep you Windows Server 2012 R2 Virtual Machine you need to open a Command Prompt with Administrator rights and type:

cd \windows\system32\sysprep

Then:

sysprep /generalize /OOBE /shutdown /unattend:unattend.xml

Window will now sysprep and the Virtual Machine will shutdown once this is complete.

Note: **It’s very important you do not boot in to Windows again on this Virtual Machine**. If you do then you will have to sysprep the Virtual Machine again.

**You now need a computer or Virtual Machine on your network running Linux:**

If you have a server on your local network running Linux then all you need to be able to do is SSH in to this computer to continue with this tutorial. This computer must have enough hard disk space to recicve a compressed DD image of your Windows Virtual Machine that we just setup. My image was around 4gb, this will however vary depending on what you do with your Windows Server 2012 R2 VM.

****This part of the tutorial is optional if you have a computer running a variation of Linux on your network****

If you don’t have a computer running Linux then I would download a copy of [Ubuntu Desktop](http://www.ubuntu.com/download/desktop) and install this in another Virtual Machine.

Once installed open up a Terminal. To Install OpenSSH type in:

sudo apt-get install openssh-server

We will need to connect to connect this computer / Virtual Machine via SSH later on to store the DD image of our Windows VM.

Once OpenSSH is setup you simply need to make a note of this computer / Virtual Machine’s IP address

Simply type:

ifconfig eth0

Then make a note of the IP address:

**Making an image of the Windows Server 2012 R2 Virtual Machine with DD:**

With your Windows Server 2012 R2 Virtual Machine powered down you want to mount a Linux Live CD ISO in the Virtual Machines CD drive. For this i used the [Desktop version of Ubuntu](http://www.ubuntu.com/download/desktop).

You then simply want to ensure the Virtual Machine is set to boot from the CD drive, or set the Virtual Machine to boot in to the BIOS when next started so you can change the boot order making the CD drive the first boot option:

Basically you want your Windows Server 2012 R2 virtual machine to boot the Ubuntu Desktop ISO. When it does click “Try Ubuntu” and you will end up at the Ubuntu Desktop with the Windows hard drive mounted.

When you have successfully the Ubuntu Live CD you need to open up a Terminal and type:

sudo dd if=/dev/sda bs=16M | xz -1 | ssh -p 22 **matt**@**192.168.1.108** "dd of=/home**/matt/**windows1.dd.xz bs=16M"

-   matt = this is the user account on the Ubuntu Desktop Virtual Machine you set-up (or the existing Linux machine on your network)
-   192.168.1.108 = this is the IP address of the Ubuntu Desktop Virtual Machine you set-up (or the existing Linux machine on your network)
-   /home/matt/ = this is your home folder on the Ubuntu Desktop Virtual Machine you set-up, change /matt/ to what ever your username is on the Linux machine.

All been well you will be asked to enter the password to connect to your Ubuntu Desktop Virtual Machine, from the Ubuntu Live CD which is running on your Windows Server 2012 R2 Virtual Machine:

The hard drive of your syspreped Windows Server 2012 R2 install will now be compressed and transferred to your Ubuntu Desktop Virtual Machine. This took around 40 mins for me.

On your Ubuntu Desktop Virtual Machine (Or Linux machine on your network) you should end up with a file called windows1.dd.xz in your home folder:

If so congratulations you have imaged your Windows Server 2012 R2 install, and it is now ready to deploy to your Kimsufi server.

**Deploying The Windows Server 2012 R2 Syspreped Hard Drive Image To The OVH / Kimsufi Server:**

Log in to your Kimsufi server manager account at [https://www.kimsufi.com/fr/manager](https://www.kimsufi.com/fr/manager)

First you want to disable server monitoring and then click “Netboot”:

You want the server to boot in to “Rescue”:

Now back in the Main Menu of your server manager click “Restart”:

The server will boot in to the Linux rescue mode and you will be sent an email with the ip address, username and password to connect over SSH on.

Now everything is in place to allow us to deploy the sysprepd image of Windows Server 2012 R2 we have backed up and compressed with dd.

On our Ubuntu Desktop Virtual Machine (Or the Linux computer on your network) we are going to upload this image to the Kimsufi server via SSH and deploy it to the servers hard drive.

TOpen up a terminal Window and type:

dd if=/home/matt/windows1.dd.xz bs=16M | ssh -p 22 [root@37.000.00.000](mailto:root@37.000.00.000) "xz -d | dd of=/dev/sda bs=16M"

-   /home/matt/ = matt would be your username
-   37.000.00.000 = this would be the ip address you got in the email to connect to your server in rescue mode

_You will be warned the authenticity of host ‘37.000.000.000 (37.000.000.000)’ can’t be established._  
_RSA key fingerprint is 27:a8:e3:f2:69:1a:bc:f9:49:03:90:b8:a4:7d:39:fd._  
_Are you sure you want to continue connecting (yes/no)?_

Say yes here and all been well your image should start to upload over the internet to your Kimsufi server:

By default you will notice it looks like nothing is happening, however i can assure you it is.

If you open another terminal and type in:

watch -n60 'sudo kill -USR1 `pgrep
{ #dd}
`'

The speed of the transfer and the amount of data transferred will be printed to the Terminal every 60 seconds:

As i mentioned earlier the time this takes will vary massively depending on your internet connection, with a 12mbps upload it took me around an hour to upload just over 4gb. This could easily take you over 8 hours if you have a 1mbps upload.

When the upload has finished you should see something which looks like this:

Now the syspreped image of our Windows Server 2012 R2 install has been deployed to the Kimsufi server, go back to the web manager for your server and change the netboot back to the hard drive, then re start the server again:

Now you need to cross your fingers. After 5 minutes your Kimsufi server should have re started and booted Windows from the sysperped image we deployed. You should now be able to connect to the server via remote desktop. If so congratulations the hard part is now over!

As can be seen below the syspreped image has been successfully deployed to the Kimsufi server, windows has booted on the Kimsufi server and setup all the hardware, finally obtaining an ip address, allowing us to Remote Desktop in to the server:

All you really need to do now is a bit if tidying up / tinkering

**Tidy up your new Windows install on the Kimsufi Server:**

When you first connect to the server you will be presented with the following:

Say no to this question as your server is connected to a public network.

Now delete the unattend.xml from **c:\windows\panther** and **c:\windows\system32\sysprep**

Next you will notice the hard drive space is limited to the amount that was available in your Virtual Machine, so we need to extend the partition of the C:\ drive.

Search for Computer Management on the start screen and click Disk Management, then right click on the C:\ drive and click Extend Volume:

You then simply want to select the maximum amount of space available:

After that you will be able to use all the space on the servers hard drive, of course you can partition the drive as you please:

**Static IPv4 Setup:**

Your server will have automatically obtained it’s assigned IP address via DHCP, however I would personally configure this to be a static IP, as OVH do this by default on all servers.

Simply go to Network > Network Sharing Center > Change adapter settings > Ethernet > Details then make a note of the IPv4 Address, IPv4 Subnetmask, IPv4 Default Gateway and IPv4 DNS server, then click close:

Click on Internet Protocol Version 4 (TCP/IPv4) and click properties:

Enter the details you made of note of, here i have used the Google DNS servers instead:

**IPV6 Setup:**

First log in to the Kimsufi / OVH manage and find the IPv6 subnet you have been assigned, then follow my example below to assign your server an IPv6 address and workout what your default gateway should be.

**Example:**

**IPv6 Subnet in OVH Manager:** 2001:A1D1:9:A8B8::/64  
**IPv6 Address:** Remove the /64 from your IPv6 subnet and add a 1 to the end, your severs IPv6 address would now be 2001:A1D1:9:A8B8::1  
**Gateway:** Remove up to the last two numbers of the IP address (B8::/1) and replace with FF:FF:FF:FF:FF, so in this example the gateway would be 2001:A1D1:9:A8FF:FF:FF:FF:FF  
**Subnet Prefix Length:** This is simply 64

For the preferred DNS and alternate DNS server for IPv6 I use Google DNS service:

-   **Preferred:** 2001:4860:4860::8888
-   **Alternate:** 2001:4860:4860::8844

So all entered in to the servers IPv6 configuration would look like this:

You should end up with internet for IPv4 and IPv6 connectivity now:

**Update the server:**

Next go to Control Panel > Windows Update and turn on Automatic Updates, then let the server install all the updates available:

You are now basically finished and free to configure the server as you please!

**However I would suggest:**

1.  Creating another Administrator account and disabling the default “Administrator” user account so if anyone does try to bruit force Remote Desktop access to your server they will get no where by using default account names.
2.  Limit Remote Desktop access to certain IP addresses – You can only really do this if you have a static IP address, or another server with a static IP address. This means you can only even attempt to Remote Desktop to the server from a “trusted location”.
3.  If you are unable to limit what IP addresses can access Remote Desktop then i would change the default port form 3389 to something random. At least any automated bot simply trying to connect on port 3389 would get no where, however it wouldn’t stop someone from port scanning your server and finding out what port Remote Desktop is actually listening on. If you do change the Remote Desktop port, remember to open the port you are changing to first on Windows Firewall, if not you will lock yourself out your server.

Hopefully someone will find this tutorial to be useful, I have installed Windows on a few Kimsufi servers like this perfectly fine. If you have any questions i’ll do my best to try and answer them.