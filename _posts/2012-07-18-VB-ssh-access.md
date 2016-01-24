---
layout: post
title: Setting up a VirtualBox VM for SSH Access
---

Since my primary OS is windows (gasp, please don’t call the police), I often use an Ubuntu VM for development work. VirtualBox is a great, free, and open source piece of software that allows you to easily create and run a VM on your machine.

Most of the time, I just use a Ubuntu server VM though- not the desktop version, and while VirtualBox does provide terminal access to the VM, I prefer using putty to connect because it offers nice features like re-sizable windows and highlight to copy, not to mention it can be nice to have multiple SSH windows open.

The default settings for a VirtualBox VM will not allow you to connect to your VM via SSH though. This post will walk you through a couple methods to help get you up and running.
## Method 1: Using a Bridged Adapter

Generally, the easiest method to get things working is to simply change the adapter type to “Bridged Adapter” on your VM settings. To do this, open up VirtualBox and right-click on your VM, and select “settings.” Next, open the “Network” tab and change the “Attached to” drop down list to “Bridged Adapter.”

Next time you boot up your VM, it will act just like another computer on your network, with its own IP and everything. (Meaning you can also SSH to it from other computers on your network) In my opinion, this is by far the best and most convenient way to get things working; however, there are times when it is not an option, and we’ll go over ways to handle those below.
## Method 2: Port Forwarding

As I recently learned at my summer internship, there are times when you cannot use bridged mode due to network policies. The company I’m working at has particularly tight information security policies and will not allow any devices other than company owned devices onto their network. So, basically my VM looks like a user owned device, and therefore could not use bridged mode.

 

The trick we can use here though is port forwarding. VirtualBox provides tools that will allow you to forward a port from your host machine to your VM. In this example, we will forward port 2222 on the host machine to port 22 on the VM, but this technique is generally applicable and can be used to forward any combination of ports.

First, open up VirtualBox again and go back to Settings > Network. Make sure the “Attached to” option is set to “NAT” and then expand the “Advanced” options. You should now see a clickable button titled “Port Forwarding,” if you can’t select it, double check that your “Attached to” option is set to “NAT.”

This new dialog will allow you to add and remove forwarding rules for this VM by click on the buttons on the right side. Don’t worry about the guest/host IP addresses; however, in the “host” port box, put the port that you want forwarded (in our example, port 2222) and then in the “guest” port, put where you want it forwarded to (ie. 22 for SSH).

And boom! That’s it. You should be good to go now. All traffic directed at your host machine (localhost) on the port(s) you’ve entered will be forwarded to your VM.
