---
layout: post
title: Deploying Express.js Website to Production
---
So, you’ve written you’re express.js app and you’re finally ready to push it to production. Should be easy right? Just copy the files, turn on the web server and bam ready to go. Oh wait… this isn’t like other web apps you’ve written before… the app itself is actually the server. Alright, no big deal, just run it like always! But it starts on port 3000 in “development” mode… that can’t be good.

These are just a couple of the things that I ran into while deploying my site, and unfortunately the existing instructions on how to get things running on production are really not that great.

This post will describe how I went about getting things up in running in a (relatively) stable manner.
## Step 1: Environment Variables

Express uses two environment variables to dictate whether something is in production or not: NODE_ENV and PORT. PORT should be fairly obvious in that it controls the port that the web server will bind to. You will most likely want to set this to 80 unless you’re using another service/program to proxy traffic through to your app. NODE_ENV specifies which environment the application will run in. It defaults to “development” which is why your program always starts in development mode. You will probably want to set this to “Production,” but Express allows you to define any number of different, custom runtime configurations. In app.js, you have probably seen these lines before:

{% highlight js %}
app.configure('development', function(){
...
});
{% endhighlight %}

In order to setup a different development environment, you simply copy & paste, and switch out ‘development’ for the environment of your choice. My production configuration looks like this:

{% highlight js %}
app.configure('production', function () {
    var oneYear = 31557600000;
    app.use(express.static(__dirname + '/public', { maxAge: oneYear }));
    app.use(express.errorHandler({ dumpExceptions: false, showStack: false }));
});
{% endhighlight %}

Telling the server to run in “Production” also does some things behind the scenes like caching of templates, so I would not recommend turning it on during development.

Now, you set these variables at the command line like this:
{% highlight js %}
export NODE_ENV=production
export PORT=80
{% endhighlight %}
I’d recommend creating a quick shell script to automatically set these before starting your app, it’ll make your life easier. The script I created is below:
{% highlight js %}
#!/bin/bash
export NODE_ENV=production
export PORT=80
sudo -E forever start app.js
{% endhighlight %}
Things to note about this script:

  - Sudo? – yes, Ubuntu will not allow processes run by normal users to bind to ports below 1024, so you have to run this as root.
  - E flag? – this tells it not to clear the environment variables before executing the app. I think this might be a security risk, but imo it is pretty minimal.
  - Forever? – this is a lovely little node.js app that will make sure that your app keeps running. If for whatever reason the node process running your application crashes, this app will restart it. Definitely a must have for any site using Express.js. It can be installed by running `sudo npm install forever –g` (note: important to install it globally with the –g flag)

And that’s it, right? We’re done! ---- Not quite. Check the next section for one last step before you’re good to go.
## Step 2: Setup new Session Provider

So, up until now, you’ve been using the default, memory-based session store that ship with express. This is great for development, but not so great for production. If you try to run an app in production mode with this still enabled, you’ll get a nasty warning about how it leaks memory, and it would be a really, really good idea to switch it out with a real provider. Fortunately, this is fairly easy to do.

I chose redis for my session store, so I’ll walk you through how to get redis setup with your express app on Ubuntu 12.04.

First, you need to install redis. You can use the following command:
{% highlight js %}
sudo apt-get install redis-server
{% endhighlight %}
Next, you might want to convert redis to using upstart instead of init.d, the steps to do that are outlined very well in this post: https://gist.github.com/1315952

Finally, you’ll need to use the redis session store module, which can be installed with the following command:
{% highlight js %}
npm install redis-connect
{% endhighlight %}
Next, now that we have redis and all of the node modules installed, we need to actually tell our program to use it.

In app.js, you can import the redis store using the following line:
{% highlight js %}
var RedisStore = require('connect-redis')(express);
{% endhighlight %}
Then, you’ll want to change the app.configure line that you had been using for session support before to look like this:
{% highlight js %}
app.use(express.session({ store: new RedisStore, secret: 'this is your secret' }));
{% endhighlight %}
… and that’s it! (I think… it’s been a few weeks… if it isn’t working for you, shoot me an email: ebensing @ this domain)
