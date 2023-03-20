---
title: Getting started
layout: home
nav_order: 2
---

Getting Started
---------

The first thing you'll need to do is sign up and join a team. You can sign up at [here](http://signup.iseage.org). If you want to compete, you'll register Blue team. Red team is for the people that will do the attacking during the competition. Green teamers act as clients and will be testing your network's usability. If you don't have a team, there is a team forming channel in our [Discord].

Scenario
--------
The current scenario docs are located in this [folder](https://drive.google.com/drive/folders/1xbCWxoZAGqPjY10DXaI3FKUBd53PCcUu). To get started, look at the 'ISU 2 Scenario.pdf', which will contain information on all of your machines and what they each do. The docs in that folder are examples on how to make documentation.

IScore
--------

[IScore] is where you find lots of important information for your team. When setting up your boxes, you'll need to add the DNS records to DNS records section. Generally, you'll want to leave the type as type A. Then for each of your machines, set the name to the one listed in the scenario docs. 

vSphere
---------
[vSphere] is where you will access all of your boxes. After you sign in, you'll be able to access and manage your team's boxes.

Things to do on your macines
----------------------------

If you've never competed in a CDC competition, there is a video that walks you through most of the steps listed on this page on our [Youtube channel].

<!-- Not sure if it's a good idea to embed it or not but here is the link to do that if we want to -->
<iframe width="560" height="315" src="https://www.youtube.com/embed/cY4oY5qvuhw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe> 

Common tips:

- Change your default credentials!
	All of your machines start with the same usernames and passwords. These should be changed to something more secure.
- run ```sudo apt update``` and ```sudo apt upgrade```. Check the scenario docs to make sure you are allowed to. You usually can't do major updates but these are fine.
- Check your /etc/password and /etc/shadow files for unauthorized users.
- Look at what cronjobs are running
- See what services are running using a tool like netstat and turn off/block ones that are not required in the scenario doc. It may be helpful to add a firewall.


[IASG]: https://iasg.github.io
[Discord]: https://discord.gg/3xxec7V5zN
[Signup]: http://signup.iseage.org
[IScore]: https://iscore.iseage.org
[vSphere]: https://vcenter.iseage.org
[Youtube channel]: https://www.youtube.com/live/cY4oY5qvuhw?feature=share