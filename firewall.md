---
title: Firewalls
layout: home
nav_order: 3
---

# Setting up Firewalls
 

Network vs Host based firewalls
---------------------------------
A host-based firewall is a software-based firewall installed on an individual device, such as a desktop computer, laptop, or server. Host-based firewalls control traffic based on rules defined on a per-device basis. The firewall software typically monitors inbound and outbound network traffic, and it can block or allow traffic based on the predefined rules. Whereas, a network-based firewall sits between our private network and the public Internet, controlling traffic entering and leaving the network. Network-based firewalls are designed to protect an entire network, rather than a single device. They examine traffic based on predefined rules and can block or allow traffic based on those rules. Network-based firewalls can be either hardware or software-based, and they are commonly used in enterprise environments. The main difference between host-based and network-based firewalls is the location of the firewall and the scope of the protection they offer. Host-based firewalls protect individual devices, while network-based firewalls protect entire networks. You will generally only need to do one or the other for this competition.


Host Based Firewalls
----------------------
The simplist and most common host based firewall is a tool called ufw, which stands for Uncomplicated FireWall.

To install run:

```sudo apt install ufw```

To enable ufw, run:
```sudo ufw enable```

If you need to turn it off, run:
```sudo ufw disable```

By default, this will block all incoming connections, and allow out outgoing connections. You probably won't have to or need to but if you want to change these defaults, run ```sudo ufw default deny outgoing```. Just change deny with allow or outgoing with incoming to suit your needs.

Since this will block everyhting by default and we still need to communicate, we will need to make rules to allow some services through. 
To allow ssh through, run

 ```sudo ufw allow ssh```.
 
"ssh" can be replaced will any well-known port name, or simply the port number. To reverse that, change allow to deny.


If you ever find yourself needing to check which rules are in play on a device, run

 ```sudo ufw status verbose```




Network Based Firewalls
-------------------

You'll probably want to use PFSense or OpenSense.

More documenation on this will be added later.

----