---
title: Network-Based Firewall
layout: home
nav_order: 3
---

**Preamble**: Readers are assumed to have taken 230. Steps like adding VIPs, static DHCP leases, and how to allow a port will not be covered. Steps involving the deployment of machines on VSphere will also not be covered

When looking at what ports need to be exposed, ALWAYS look at the scenario document. Anything in the machine description like, "port XXXX MUST be available on the competition network," means that the given port has to be allowed through the firewall

# Why Use One?

Keen readers may notice that there are two kinds of firewalls in the sidebar and ask: What is a network-based firewall? Why is it different than host-based?

A network-based firewall is essentially the bodyguard to the machines on your network, but Before even adding one, some initial walls are needed. When the machines are given to competitors of the CDC, they are just out in the open. The best analogy for this is an open field with tape on the ground to mark off where each team is allowed to be. Red team doesn't really care about this tape. They just see the open field where they can sneak behind competitors backs and steal their stuff.

To set up the walls, a router is needed. This acts as a single point of entry for a team's network. The easiest way to do this is to create a new virtual machine and to load PfSense on it (an in-depth guide will not be provided here). With that loaded, the NICs for machines that belong behind the firewall (usually all of them) should be changed from the WAN interface to the LAN interface.

Now that the walls are up, competitors can limit the ports that can be accessed on each machine. This is the main goal for a network-based firewall: reducing the outside attack surface, and limiting which applications can be accessed from the outside. This doesn't stop attackers from accessing random ports from machines inside the firewall. That is the job of the host-based firewall.

# Active Directory / Domain Server

Starting with the most complicated and important option, the active directory (AD). It is imperative to protect the Active Directory because it manages user access to other machines and services. There technically aren't any ports that *need* to be exposed. This is because ISEAGE already provides domain services, and most machines will talk directly to the active directory to authenticate. Leveraging this can keep the AD separated and inaccessible 

### Authenticating From Outside
//TODO - Add more about NLA once it is known (I am currently consulting with a Windows professional to do more testing on this. All public resources are inconclusive, and we are still working on a definitive answer)
One of the most common reasons to open ports for the active directory is that authentication from the outside is needed. The three most common reasons for this are: competitors wanting their active directory on a different subnet, the scenario document requiring that the Active Directory isn't behind a firewall, or network level authentication (NLA).

Network level authentication or NLA is often used to enhance the security of the remote desktop protocol (RDP). NLA forces a user to authenticate *before* opening a remote desktop connection to limit the resources used by a machine before the logon is approved. The biggest impact of this is reducing the resources used up by failed login attempts, so it could be safely turned off (red team is not allowed to purposefully DOS machines). It is much more necessary in a production environment. \[1\]

For people who want to use NLA, however, there are a few ports that need to be opened on the firewall:
- 88 (Kerberos)
- 135 (RPC)
- 389 (LDAP)
- 445 (SMB)
- TCP/49152-65535 (RPC)
- UDP/49152-65535 (RPC)

# Sources

\[1\] *Configure network level authentication for Remote Desktop Services Connections* (2009) *Microsoft Learn*. Available at: https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732713(v=ws.11) (Accessed: 24 April 2024).