---
title: Host-Based Firewall
layout: default
nav_order: 4
parent: Windows
---

# Why Use One?

Host-based firewalls allow a machine to look at incoming and outgoing packets to and from itself. The fact that it's on the machine means that the packet contents for protocols like HTTPS can be decrypted, and the machine can restrict which packets are allowed based on the content. A lot of network-based firewalls also focus more on packets coming into and out of the network instead of between machines in the network. Host-based firewalls can focus on restricting lateral movements and connections between machines inside the network. For example, the Active Directory may only have RDP open on the internet, but an attacker may open up a listening port for another machine to connect to inside the network. A host-based firewall can prevent this. 

One useful example for the importance of host-based firewalls is related to remote procedure call (RPC). On Windows, RPC allows for remote execution of code on a machine. RPC also uses the ephemeral port range (49152-65535), so it is very hard to limit network access to a machine that uses RPC even if the port range is more restricted. Host-based firewalls help by ensuring that the packet is a proper RPC packet and not an attacker's custom server used to make a backdoor or exfiltrate data.

To help with the cases above, host-based firewalls can also specify applications that the packets are allowed to go to. For example, port 80 may be allowed on the host to serve a website, but nodejs may be the only program allowed to send and receive packets over that port. If an attacker tried to set up a rogue python server, the firewall would block that access.

# How to Use Windows'

Searching "firewall" should bring up "Windows firewall with advanced security". Click into this, and then there's a couple key areas. The main area shows three different profiles as well as indicators for if they're on. What each of the profiles are will be covered after the intro. In the left pane, there are the inbound rules, outbound rules, connection security rules, and monitoring. Inbound and outbound rules will say what packets are allowed into the host and out of the host respectively. Connection security rules can limit what is allowed to connect to the computer using protocols like IPsec, IKE, and authIP to ensure that data in the connection request isn't being spoofed. The last section, monitoring, will allow users to see where their log file is and configure what is logged.

### Profiles

On the main page, there are three main profiles: domain, private, and public. Different profiles will apply depending on the kind of network that the host is currently in.

- Domain:
    - The host can authenticate to a domain controller
    - What will likely be used in the CDC
    - Often applied when at work or on a VPN into the work network
- Private:
    - The host is on a home network and hidden behind a firewall managed by a trusted entity
    - Often applied on a home network
- Public:
    - The host is on an open network with unknown devices and managers
    - Often applied in public places like restaurants
    - May be applied in apartments or dorms where the IT managers are unknown and devices share a network \[1\]

Each firewall rule is usually made for every profile and enabled or disabled as appropriate. This allows for explicit denys on networks where those packets should not be allowed. One example would be to only allow incoming connections on 3389 (RDP) when the host is connected to a domain controller.

### Rule format

Looking at the rules, there are a lot of options that can allow for large amount of flexibility and granularity with what is allowed and disallowed. For this site, the ones that will be focused on are: profile, program, remote address, and local/remote port. The rest are either self-explanatory (like name and enable), or they're more niche than this article is for.

Profiles have already been mentioned above, but it's worth looking more at what rules should be enable for which profiles. The domain profile is often the least restricted. Some organizations may decide to add limitations when on the network to limit lateral movement, but it's often the case that normal activity will be enabled across all profiles and domain will have domain-specific options like kerberos and more risky protocols like RDP. Private may have some additional access as well, but it's often the case that private and public will be the same to maintain those restrictions anywhere outside of the corporate network.

The program is quite useful for narrowing the access on a port even further. The reason linux requires administrator rights to use a well-known port with a program is to prevent spoofing. Windows firewall helps to prevent this by requiring special permissions to edit the firewall and then letting the firewall limit access on any port to only specific programs. It is highly recommended to ensure that there is a program specified, especially for known ports.

Like with the network-based firewall, host-based firewalls allow source and destination hosts and ports to be specified in rules. These have a similar effect to network-based firewalls, and they can ensure that any host-to-host communication can be limited. One situation where specifying both a host and port could be useful is if a web server needs to talk to a database. Both the database's static IP and port can be specified to ensure that attackers aren't using the web server to pivot to other machines or use ports in the database other than the given one.

### Monitoring

Monitoring is one of the most important parts of the firewall. The panel will show the settings per profile, so make sure to check the right one before changing anything. The most important part to look at is where the log file is stored. This will allow defenders to actually see what is being blocked when. This may make it easier to find how an adversary got it in by looking at the kind of attacks being run before an access was found in the logs. It's best to use these for double-checking and information gathering alongside other sources of logs because they explicitly show what *didn't* work.

To change the settings, either a Group Policy Object (GPO) or netsh can be used. To run the netsh commands, either use `netsh advfirewall [command]` or `netsh advfirewall` to get into the shell and run commands. For more information on how to modify logging with GPO or netsh, go [here](https://learn.microsoft.com/en-us/windows/security/operating-system-security/network-security/windows-firewall/configure-logging?tabs=gpo). For more information on how to use netsh for advfirewall, check [this](https://learn.microsoft.com/en-us/troubleshoot/windows-server/networking/netsh-advfirewall-firewall-control-firewall-behavior) out.

# Sources
\[1\] “Windows Firewall Profiles,” Microsoft Learn, https://learn.microsoft.com/en-us/previous-versions/windows/desktop/ics/windows-firewall-profiles (accessed May 1, 2024). 