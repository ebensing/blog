---
layout: post
title: Sudo Problems & AWS
---

I am a huge fan of Amazon’s AWS platform. I can have my own small linux VM running, never have to worry about hardware, and be reasonably sure it’ll be up, all for less than $20 a month. Pretty sweet deal. It has a nice web interface for creating new VMs, restarting, ect. All in all, something I’m very happy to pay for and it beats the hell out of paying for hosting from a traditional provider. But, it does have its problems, and as I found out, they can be a bitch to get around.
## The Story

So, last night I decided that I’d upgrade my version of node.js real quick just to keep everything recent. Big mistake. At the rate that Node.js and some of the modules I use (looking at you Express) are changing, upgrading isn’t exactly a friendly task, and backwards compatibility is not really a priority.

So, essentially along this whole process, I was editing some permissions on files. Specifically, I was going to change the owner of /usr/local/ to my account, so that I didn’t have to mess with `sudo npm …` when using npm. Alas, you should really not be doing stuff like that late at night when you can accidentally type /usr/ instead of /usr/locals… Long story short, my ‘sudo’ program as well as everything under my /usr/ directory was now owned by my local account, and to change it back, I needed to use sudo… which I couldn’t do because sudo won’t run if it isn’t owned by root… sigh.
## So, how the hell do you fix that?

Well, on a normal, physical machine, it really isn’t that bad. Boot up into recovery mode, get root, change the permissions back and then you’re off to the races. However, if you’re on AWS… you can’t boot into recovery mode and things get a bit trickier. Below are the steps I had to take to get everything working again, hopefully I can save somebody else some time if they ever end up similarly screwed.
## Step 1: Shutdown your VM and startup a new one

So, first, you should shut down your VM. Take note of which availability zone you are in because the new VM you start up must be in this zone. Additionally, when starting up a new VM, I’d recommend going with the default amazon AMI and not another Ubuntu server one. For some reason, it causes problems when you attach a root Ubuntu EBS volume as a secondary EBS volume on another Ubuntu VM.

Next, you are going to want to go to your EBS management page and detach your main EBS volume for your original server. You should then be able to attach it to the new VM you just created. NOTE: both VMs must be in the same availability zone for you to do this. If they are not, AWS will not let you attach the old EBS volume to your new VM.

Also, take note of the device name for the old EBS volume. It’ll probably be something like /dev/sdf or similar.
## Step 2: SSH into the new VM and mount the old EBS volume

Once you are SSH’d into your new VM, simply run `sudo mkdir /mnt/old` and then `sudo mount /mnt/old/`

You will now be able to access the file system to your old machine at /mnt/old, and since this new VM has a working sudo, you can change the owner back or do whatever else you need to do to your old file system to get things working.

Once you’re done fixing file permissions, simply power down the new VM and re-attach your old EBS volume to your old VM. If you did what you needed to, you should be able to sudo again after you’ve booted up.
