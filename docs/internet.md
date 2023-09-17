---
title: Connecting to the Internet
layout: home
nav_order: 3
---

Inside the ISEAGE the virtual machines do not have any real world access to the outside internet. However, there is a proxy that allows for http, https, and ftp traffic to reach the outside internet which is running on `199.100.16.11:3128"`. These are the steps to configure your virtual machine to use this proxy, and allow it to reach the outside internet, and disable IPv6, to prevent issues that may arise from it.

## Linux

If there is a distribution that these do not work, feel free to contribute how to do it for those distributions!

### Ubuntu Based

To setup the proxy environment variable for users to use the proxy all the time. Edit `/etc/environment` with root permissions `sudo nano /etc/environment` and add the following lines to the bottom of the file:

```bash
http_proxy="http://199.100.16.100:3128"
https_proxy=http_proxy
ftp_proxy=http_proxy
```

The next step is to disable IPv6 on the system. This can be done by editing `/etc/sysctl.conf` with root permissions `sudo nano /etc/sysctl.conf` and adding the following lines to the bottom of the file:

```bash
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1 
```

Now reboot the system, and the proxy should be set for all users, and IPv6 should be disabled. Which means you can now use the internet roughly normally.

## Windows

This section is a work in progress, feel free to contribute!
