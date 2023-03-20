---
title: First Firewall - OPNSense
parent: Basics
has_children: true
nav_order: 3
layout: default
---

{: .warning }
> There are 2 iso images for OPNSense, the older image does not work! You must use the 23.1 release!<br>
> ![newer iso](/assets/basics/first-firewall/proper-installer.png)

Firewalls are super useful in a CDC since they can prevent unwanted traffic both in and out of your network without having to deal with system managed ones like ufw that are compromised as soon as root is compromised. So, we are setting up an OPNSense firewall. The other major setting that needs changed is the exact iso image used for creating the virtual machine since the older one does not work

To create an OPNSense firewall, the setup for a new virtual machine is nearly identical to [normal setup](first-virtual-machine.md), but there are some minor changes, during the creation process select the operating system type as FreeBSD11, since OPNSense is a BSD based operating system. After going the reset of setup normally, you will need to another another network card, so that there is a LAN, and a WAN device.

To add a new network card, select the virtual machine in ESXI, and open the actions menu, then select edit settings.
![action menu](/assets/basics/first-firewall/actions-menu.png)

In this menu, near the top right there is prompt to add a new device, scroll down and select network adapter.

![new network device](/assets/basics/first-firewall/new-device.png)

After adding this device, in the same settings menu, there should now be 2 network adapters. For the firewall, one of these network adapters needs to be on the competition while the other is on one of your teams internal networks which can be seen below.
![different networks](/assets/basics/first-firewall/dual-nic.png)

We can now go into the installing of OPNSense itself, to do this, start up the virtual machine. It may take some time to boot, but eventually you will get to a login screen similar to the one below. To start the installation process login with the username `installer`, and the password `opnsense`
![login screen](/assets/basics/first-firewall/opnsense-installer.png)

The installer will then likely ask about keymapping, I have had good luck with the default mapping, and just pressing enter, but there are a variety of options if you find yourself having keyboard issues as seen commonly for the web console.
![key mapping](/assets/basics/first-firewall/keymapping.png)

It will then ask about the disk to install OPNSense on, I have had best luck with the ZFS option.
![zfs start](/assets/basics/first-firewall/zfs-part1.png)

There are quite a few options for ZFS if you want to explore them, but I always pick the striped option and trust the data will not be lost on the ISEAGE servers.
![zfs striped](/assets/basics/first-firewall/zfs-part2.png)

{: .information }
> The next menu has you selecting disk, to select the disk you want to use, you use the spacebar, and the selected disks have a star seen below. <br>
> ![zfs select](/assets/basics/first-firewall/zfs-part3.png)

Then complete the process by removing all the data on the drive.
![wipe drive](/assets/basics/first-firewall/zfs-part4.png)

From here the installation will continue to occur automatically, and take some time to complete the installation process, after which the following menu will come up. We recommend changing the root password now, so it won't be forgotten later, after which you can exit and reboot the system to complete the setup process. Prior to rebooting, you can turn off the connect at power on for the CD/DVD drive in the settings menu for the hardware of the VM we were in earlier.
![root password](/assets/basics/first-firewall/rootpassword.png)

The next thing you will do is verify that the auto-configuration tool worked to properly set the LAN and WAN interfaces. To do this first make note of the MAC addresses of the network adapters from vcenter.

![nic macs](/assets/basics/first-firewall/nic-macs.png)

The next step will be launching the web console of the firewall, and logging in with the username `root`, and the password you set earlier (or `opnense` if you did not set a password). After logging in you will get a prompt similar to this.

![assign interfaces](/assets/basics/first-firewall/assign-interfaces.png)

You want to pick the first option of assigning interfaces, and then go through the steps, the first step, you probably do not need to configure LAGGs, or VLANs, so you can put `n`. 

![assign interfaces](/assets/basics/first-firewall/assign-part1.png)

The auto detection is not always great since there is no DHCP on the comp net, so you want to look at the vcenter screen to see which MAC address is on the comp net (the WAN), and which is internal (the LAN). In our cases, `vmx1` is the WAN, and `vmx0` is the LAN.

![assign interfaces](/assets/basics/first-firewall/assign-part2.png)

There will be another verification prompt after this, and you can verify the interfaces are correct, then `y` to continue.

![assign interfaces](/assets/basics/first-firewall/assign-part3.png)

At this point, I would likely reboot the system, that way I can guarantee the settings are stored correctly, and won't be lost easily. After this reboot, the next step is allocating an ip address for OPNSense on the WAN. There are a pair of options for setup of OPNSense, you can setup from the command line, or from the web gui.

{: cli }
> If you want to setup OPNSense from the command line, you can follow these steps. <br>
> The first step is to login to the OPNSense console, from the Web Console, and you will get a prompt similar to this. <br>
> ![assign ip](/assets/basics/first-firewall/ip-address.png) <br>
> Select option 2 to set the IP addresses. After selecting this, it will ask which interface you want to configure, select the WAN interface by the correct number. <br>
> ![assign ip](/assets/basics/first-firewall/ips-part1.png) <br>
> The comp net does not have DHCP, so select `n` when asked about setting up WAN over DHCP. <br>
> ![assign ip](/assets/basics/first-firewall/ips-part2.png) <br>
> The next question will be to set the IP address, looking at your other virtual machines, they all should have the same first three octets, use this same set of octets for the OPNSense IP address, and then pick a final octet that is not in use. For example we picked X.X.X.1 <br>
> It will then ask for a netmask, we recommend using 24, since that is the size of network you are given on the comp net which will allow OPNSense to create virtual IPs for you other VMs. <br>
> ![assign ip](/assets/basics/first-firewall/ips-part3.png) <br>
> The next question will ask for the gateway, your networks gateway is the X.X.X.254, where the X.X.X is the same as the IP address you picked for OPNSense. <br>
> The next question is asking if the gateway should be the DNS server too, you should answer `n` since the DNS server for the comp net is keyhole, `199.100.16.100`. <br>
> The next question will be asking about setting up ipv6, you can answer `n` to this question, since the comp net does not have ipv6. <br>
> The next 3 questions are about the web GUI, we recommend answering `y` to at least restoring the access defaults, bu the other options are up to you. <br>
> ![assign ip](/assets/basics/first-firewall/ips-part4.png) <br>

{: gui }
> This will be setting up OPNSense from the web GUI. <br>
> For this step you will need another virtual machine created, that has not gone through the process of being connected to the competition network, and instead the network adapter is placed on the internal network that OPNSense is using as the LAN. <br>
> After connecting this VM to the LAN interface, start/reboot, so the device can get a DHCP address from OPNSense. <br>
> After getting this address, navigate to ip address of OPNSense in a browser, (typically `192.168.1.1`), and login with `root` and the password you set earlier. <br>
> After some loading time, you wil be greeted with a setup wizard. <br>
> On this first screen, set the DNS resolver to the keyhole `199.100.16.100`, and then click next. <br>
> ![assign ip gui](/assets/basics/first-firewall/ips2-part1.png) <br>
> The next screen is asking about timezone, you can leave this as is, or change it to your timezone. <br>
> The next screen is setting the information about the WAN interface. At the top of the page, select the IPv4 Configuation type to be `Static IPv4`. <br>
> You then need to fill in these 3 fields, the IP address, look at your other VMs and use the first 3 octets of the IP address of one of them, and then pick a final octet that is not in use. For example we picked X.X.X.1. The next field is the netmask, we recommend 24 since that covers the entire network you are given on the comp net. The final field to set is the upstream gateway to X.X.X.254, since that is the network's gateway. <br>
> ![assign ip gui](/assets/basics/first-firewall/ips2-part2.png) <br>

Your basic firewall is not setup with an IP address, and is ready to be used! The next step is to move virtual machines behind the firewall, and then setup the firewall to allow traffic to them. [Click here to continue to the next step](first-nat.md)