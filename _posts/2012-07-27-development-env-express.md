---
layout: post
title: Development Environment for Express.js
---
So, setting up a basic development environment for express is not that hard. In fact, you can really just run it from your local system if you want. However, if you need to collaborate or if you want to do your development on a remote machine, it takes a couple more steps to get a convenient working environment setup.

The following post will explain how I set mine up and introduce a couple of the tools I used to make my life easier.
## Git Hooks

So Git might be the single best thing to happen to software development in the last few years. It is miles ahead of every other form of version control out there and has even inspired the creation of amazing websites like Github.

I’ve always thought it would be great if my code would automatically deploy itself after I pushed it to the server. Rather than me having to SHH into the dev box and pull it after every update. Since Git is a distributed version control system, one would think this is just a matter of pushing to a copy of the repository on your development box; however, the kind of repo that accepts push commands is called a “bare” repository, and it does not have a copy of the code in a usable form. Fortunately, Git provides access to things called “hooks,” which are scripts that execute after certain events, like receiving a push update.

Here are the steps I took to get this setup.

First, clone a bare copy of your repository onto your development box:

`Git clone --bare user@url:repo.git`

Then, create a normal clone of that repository:

`Git clone repo.git repo/`

Finally, under repo.git/, there should be a folder called “hooks” and if you create a file in there called “post-receive,” it will get executed after any push updates are received. (note: you may need to chmod +x it)

The script I use looks like:  
{% highlight bash %}
#!/bin/bash
#CONFIG
LIVE="/home/ubuntu/rm-dash-r"

read oldrev newrev refname
if [ $refname = "refs/heads/master" ]; then

 echo "===== DEPLOYING TO LIVE SITE ====="
 unset GIT_DIR
 cd $LIVE
 git pull origin master
 echo "===== DONE ====="
fi
{% endhighlight %}

## Supervisor

So, now we have it setup so that our code is auto-deployed with a Git push, but we’d still need to be ssh’d into the server to restart the process every time a javascript file in our project gets updated.

Fortunately, somebody else thought of this problem already and designed a great tool called supervisor to make our lives easier.

To Install:

`Sudo npm install supervisor -g`

Now, just run your app like normal, except instead of using:

`node app.js`

Do:

`supervisor app.js`

Supervisor will automatically crash node and restart it anytime a javascript file is changed.

And that’s how I setup my development environment. All in all, one of the easiest and most enjoyable dev environments I’ve ever worked in, and definitely a setup I’ll use in future projects.
