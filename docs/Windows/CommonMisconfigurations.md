---
title: Common Misconfigurations
layout: default
nav_order: 6
parent: Windows
---

# Finding anomalies

In almost every box, there will be some items with telltale signs of a backdoor or persistance. Anything named after a character from Ice Age or labeled as a backdoor can be safely deleted. If there are any questions about whether something should be there, make sure to look at the scenario document

To find these anomalies, there are a couple easy places to look: scheduled tasks, users, groups, services, and open sockets

# Scheduled Tasks

Scheduled tasks work like cron jobs on Linux. On specific conditions, they run tasks. To look at them, click on the windows logo and and search for "scheduled tasks". Once inside the pane, click on the different folders to see what tasks are inside. There may be tasks called something like backdoor that should be deleted. It's best to look for things mentioned in the above, but going through and checking what the tasks are running is also a good idea. Some may be turning off your firewall or resetting your administrator password when the competition starts.

To quickly filter through scheduled tasks, this handy powershell script will search for what is being executed. It's a good idea to check for python, netsh advfirewall, or User.
`Get-ScheduledTask | Foreach-Object{if ($_.Actions[0].Execute -Match "STRING_HERE") {Write-Host "$($_.TaskName) | $($_.Actions[0].Execute)"}}`

# Users and Groups

The easiest thing to look for in users and groups is to find backdoor users. 

On Windows Server:
1. Go to the server management panel
2. Click on tools in the upper right
3. Click on Active Directory Users and Groups
4. Click on the users folder in the left pane to view users in the active directory

For the users, look for users that aren't in the scenario document and disable or delete them. Also go through and look at any other users, like the guest user, and make sure that their permissions aren't too high. For the guest user, it shouldn't have any other groups or permissions than the default Domain Users group.

On the groups side, it's often best to check both the Domain Admins, Enterprise Admins, and Administrators groups. These groups all give generally high privileges that can be used to do whatever they want on the system once obtained.

# Network Scanning

The most basic command to check what's running on the network is to use `netstat -ano`. This will list out any open ports as well as what is running on them. It may be best to stop this early because there will be a lot of information for a service called RPC that can be ignored.

In the list of open ports, it's best to make sure the ports required by the scenario are open, but make sure to double check any services running on ports higher than 1000. There are a lot of extra services running on lower ports like dns on 53, kerberos on 88, and rpc on 135 that are required to function, but won't be spelled out in the scenario document itself