---
layout: default
title: First Virtual Machine
parent: Basics
nav_order: 2
---

Inevitably you will want to create a new virtual machine at some point in the Cyber Defense Competition. This will be a generic creation of a Parrot Desktop, but will apply to most of operating systems.

1. Go to [vcenter.iseage.org](https://vcenter.isage.org) and login with your account.
2. Expand the fields to see you team's recourse pool, and right click, it should look similar to this.
![vm creation](/assets/basics/first-virtual-machine/vm-creation.png)
3. Click the `New Virtual Machine Button` which while open a new dialog
![dialog1](/assets/basics/first-virtual-machine/dialog1.png)
4. In this newly opened dialog keep the option `Create a new virtual machine`, and press next
5. The next option will have you create a name for the virtual machine, after putting in a name keep opening dropdowns til you can select your team's resource pool, and press next.
![naming](/assets/basics/first-virtual-machine/naming.png)
6. In this menu, select the storage option with the largest amount of free space, typically the one labeled `CDC Storage`, then press next
![storage](/assets/basics/first-virtual-machine/storage.png)
7. The next page will ask about compatibility, just leave the default and press next.
8. The next page will have you select an operating system, this setting is semi-important, as it will configure things automatically like new network adapters in an attempt to require no drivers. For this example, we selected Debian 11 since parrot is Debian based distro, and pressed next.
![os selection](/assets/basics/first-virtual-machine/os-selection.png)

{: .warning }
> There are changes that MUST be made here

9. The next page is hardware configuration, where things like CPU, and memory can be configured. There are 3 important settings that needed changed here, which are walked through below
![customize hardware](/assets/basics/first-virtual-machine/customize-hardware.png)
   1. The first thing is to change the virtual machine to be on the competition network. ISEAGE provides a variety of internal networks for teams, to setup things like a firewall and VLANs, but that is not applicable to basic VM. So in the `New Network` field, clicking the field `CDC Team X` will open a menu to select a new network, scroll to the bottom and select the `Competition Network` option, to make the network field look like the after image

#### Before

![internal-net](/assets/basics/first-virtual-machine/internal-net.png)

#### After

![comp net](/assets/basics/first-virtual-machine/comp-net.png)

   2. The next is changing the hard drive to use `Thin Provisioning`. This is done in the hard drive drop down menu, and the Disk Provisioning option.

### Thin Provisioning

![Thin Provisioning](/assets/basics/first-virtual-machine/thin-provision.png)

   3. The next thing to set to make life much easier is to add the install media here. In the CD/DVD Drive section, change that to `Datastore ISO File`, and select the `Connect At Power On`, then finally select the ISO image you want from the ISO datastore with the browsing tool.

### Using an ISO

![iso-selection](/assets/basics/first-virtual-machine/iso-selection.png)

10. After selecting these, options there will be a review page that if everything looks correct, can clicked through. 
11. Your virtual machine is now created, and can be powered on, and accessed through the web console to complete the installation of the operating system like any normal computer.
12. You have a virtual machine, but with keyhole, networking is weird, so maybe take a look at [getting to the internet](first-networking.md)
