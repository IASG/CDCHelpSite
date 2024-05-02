---
title: Common CVEs
layout: default
nav_order: 5
parent: Windows
---

# What is a CVE?

A CVE gives a label for a vulnerability in a public system or library. They are given in the form CVE-year-id, but sometimes they can gain a name and reputation if they are useful and pervasive enough. An example of one of these is a vulnerability in Microsoft's SMB protocol. Its CVE tag is CVE-2017-0144, but it is commonly called EternalBlue. Some larger companies ay also give their own name as well, but CVEs are good for providing and agnostic, common label for them. Microsoft has one of these where each major vulnerability gets gets a label: ms-(last 2 digits of the year)-id. For EternalBlue from before, it's most commonly called ms-17-010 using Microsoft's labeling scheme.

CVEs will also get a score called a CVSS score based the severity and exploitability of the vulnerability\[1\]. CVSS was developed by the National Vulnerability Database(NVD), and there are two main versions: 2 and 3. Both of these versions will typically keep vulnerabilities around the same range, but corporations will typically pick one version and use it to determine what protocols to use for different score ranges.

# EternalBlue

EternalBlue will likely be at every CDC because it is still found in the field, and it is very easy to exploit. For an attacker, there is a metasploit module that will simply run any command as the System user using the exploit. This module just needs access to SMB (usually port 445) on the vulnerable windows machine, so there are two main methods of remediation: patch the exploit and/or disallow access to SMB. For defense-in-depth, it is best to do both. 

Stopping access to SMB can be done with a [network-based firewall](./NetworkBasedFirewall.md), a [host-based firewall](./HostBasedFirewall.md), or ideally both. **If the scenario doesn't require access to it on the competition network,** it is best to ensure that access to port 445 on any Windows machine is not allowed. On the network firewall, making sure that only specific ports are allowed through NAT should do the trick. For the host firewall, sort columns by the port number and disable rules that use port 445. These additional rules will also need to be disabled: "File and Printer Sharing (NB-Session-In)" using port 139, "File and Printer Sharing (NB-Datagram-In)" on port 138, and "FIle and Printer Sharing (NB-Name-In)" on port 137. This is because Windows will automatically revert to using a protocol called NetBIOS when SMB is disabled. Before SMB became its own protocol, NetBIOS was used to cary SMB operations. This is still enabled as a fallback on Windows systems for backwards compatibility.

To fix the configuration issue itself, Microsoft recommends these steps\[2\]:

1. Open Server Manager and then click the Manage menu and select Remove Roles and Features.
2. In the Features window, clear the SMB1.0/CIFS File Sharing Support check box, and then click OK to close the window.
3. Restart the system.

Updating the system will also help to mitigate the vulnerability, but they can't always be trusted on the Competition network.

# LLMNR Poisoning

LLMNR poisoning allows attackers to harvest credentials by pretending to be an active directory. This happens when a user inputs the domain name wrong, the domain controller doesn't recognize it, and the user's computer tries to fall back onto an old protocol called LLMNR. LLMNR sends a broadcast requesting the IP of a domain. The normal domain server doesn't have it, so the attacker can then tell the host making the LLMNR query that the attacker is the domain and the user should authenticate with the attacker. This can give the attacker credentials and tokens to use with authentication. \[3\]

Two easy fixes for this are to disable IPv6 on all hosts (assuming it isn't needed according to the scenario) and to disable multicast name resolution.

// TODO Add proper instructions
To disable IPv6 on Windows Server:
1. Right click on the Windows logo in the bottom left
2. Click "n

To disable multicast name resolution:
1. Search for and open the Group Policy Editor
2. Go to Computer Configuration > Administrative Templates > Network DNS Client
3. Set "Turn off multicast name resolution" to 1

# PrintNightmare

PrintNightmare comes from a useful feature in Windows that allows administrators to remotely update printer drivers. This uses Windows' remote procedure call (RPC) to upload the new binary and have the print spooler (Service that manages what prints where when) run it. Adversaries used this to run malicious executables with the print spooler and compromise the system. \[4\]

While most administrators couldn't do this, the best option in a CDC will likely be to turn off the print spooler altogether. If the competition requires printing, this won't be an option of course.

The only other option would be to update. It would be best to check the OS versions of all Windows machines to see if they're earlier than the versions in [this list](https://www.bleepingcomputer.com/news/security/microsoft-printnightmare-now-patched-on-all-windows-versions/).

# Sources

\[1\] “NVD - CVSS v3 Calculator,” NVD, https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator (accessed May 1, 2024). 
\[2\] BetaFred, “Microsoft Security bulletin MS17-010 - critical,” Microsoft Learn, https://learn.microsoft.com/en-us/security-updates/securitybulletins/2017/ms17-010 (accessed May 1, 2024). 
\[3\] H. Adams, “Llmnr poisoning and how to prevent IT - TCM security,” TCM Security - Penetration Testing & Consulting, https://tcm-sec.com/llmnr-poisoning-and-how-to-prevent-it/ (accessed May 1, 2024). 
\[4\] K. Byatt, “What is Printnightmare? The Windows Print Spooler Exploit explained,” PaperCut, https://www.papercut.com/blog/print_basics/windows-print-nightmare-explained/ (accessed May 1, 2024). 