---
layout: post
title: Using Variables as fields in a Mongo Query from Node.js
---

Given the flexibility of mongoDB and javascript, it seems like a no-brainer that you should be able to use a variable to represent a field in a mongo query.

And you definitely can, although it isn’t quite as straightforward as putting your variable where the field is supposed to go.

Suppose we have the following code:

{% highlight js %}
var searchField = 'name';
db.coll.find({searchField : 'value'});
{% endhighlight %}
This will not accomplish what we want to do. Instead of searching a field ‘name’ for ‘value’, it will search a field ‘searchField’ instead.

What will work though is if we do the following:
{% highlight js %}
var searchField = 'name';
var searchParams = {};
searchParams[searchField] = 'value';
db.col.find(searchParams);
{% endhighlight %}
And that’s all there is to it. Just create an object, and use the variable name as a key, as shown above.
