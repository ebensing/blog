---
layout: post
title: { safe : true } and MongoDB
---

So, if you’re like me, you learned how to use MongoDB and node/express/ect. through some random online tutorials and the reference docs. This is normally a pretty safe way to do things, but I have found in this case, there is one small issue that most don’t mention.

And normally you’ll end up with some code looking something like this:
{% highlight js %}
UserProvider.prototype.addUser = function (userName, password, isAdmin, email, callback) {
    this.getUsersCollection(function (error, users) {
        if (error) {
            callback(error);
        } else {
            var salt = bcrypt.genSaltSync(10);
            var hash = bcrypt.hashSync(password, salt);
            users.insert({ userName: userName, password: hash, isAdmin: isAdmin, salt: salt, email: email }, function () {
                callback(null, users);
            });
        }
    });
};
{% endhighlight %}
And this looks fine, right? Don’t see any problems, and you might not notice any with code like this that isn’t frequently used and can probably be completed asynchronously.

However, there are cases where it is important that what we are inserting is actually in the database before we move on, and while this function seems to do that, it unfortunately does not. In this case, the anonymous callback passed to .insert() will be called immediately after the command has been sent to mongo, not after it has succeeded like we might be lead to believe. I find that most tutorials leave this part out and the reference documentation puts it pretty far down on the example list.

However, there is a really easy “fix” for this. (it isn’t a bug and can be quiet a powerful feature when you don’t need to wait) Simply add { safe :true } as a section parameter to .insert() (or .update()/.save() ) like this:
{% highlight js %}
UserProvider.prototype.addUser = function (userName, password, isAdmin, email, callback) {
    this.getUsersCollection(function (error, users) {
        if (error) {
            callback(error);
        } else {
            var salt = bcrypt.genSaltSync(10);
            var hash = bcrypt.hashSync(password, salt);
            users.insert({ userName: userName, password: hash, isAdmin: isAdmin, salt: salt, email: email }, { safe : true}, function () {
                callback(null, users);
            });
        }
    });
};
{% endhighlight %}
And that’s it! The callback function will now only be called once the insert has either succeeded or failed.
