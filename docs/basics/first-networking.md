---
layout: default
title: First Networking
parent: Basics
has_children: false
nav_order: 2
---

So you created a new virtual machine using [our guide](./first-virtual-machine), but that does not mean it is connected to the internet yet because of keyhole.

## What is keyhole?

Keyhole is what separates the competition network from the internet, to prevent the competition network from being attacked by the internet and vice versa. This is done, by having a keyhole act as a proxy for only HTTP, HTTPS, and FTP traffic, while preventing all other traffic from leaving the competition network.
