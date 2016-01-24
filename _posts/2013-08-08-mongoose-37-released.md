---
layout: post
title: Mongoose 3.7.0 Released
---

I’ve spent the last 2 months interning at 10gen (MongoDB) working on Mongoose. It has been a lot of fun and I’ve learned a ton about Node.js, MongoDB, and building open source libraries. I’m going to save all of that for a different post though, and instead talk about the newest release of Mongoose.

### Unstable

To start things off, this is an unstable release. This means that it contains potentially breaking changes or other major updates, and thus should probably not be used in production. You can tell this is an unstable release because of the middle digit. Starting from 3.7, odd middle digits mean unstable, even mean stable. This is identical to the Node.js and MongoDB versioning schemes.

### Mquery

The largest change in this release is under the hood. We are now using the Mquery library to power querying in Mongoose. This allowed us to strip a lot of logic out of Mongoose and move it into a separate module. This should make things easier to maintain in the future and allows for the query engine to be reused elsewhere.

It does add some neat custom query functionality though:
{% highlight js %}
var greatMovies = Movies.where('rating').gte(4.5).toConstructor();

// use it!
greatMovies().count(function (err, n) {
  console.log('There are %d great movies', n);
});

greatMovies().where({ name: /^Life/ }).select('name').find(function (err, docs) {
  console.log(docs);
});
{% endhighlight %}
Note: This feature is currently unreleased, but expected out in a 3.7.x update soon. 

### Benchmarks

We now have some basic benchmarks for Mongoose. We will definitely expand these in the future and these will allow us to start improving Mongoose’s performance and bring it more in line with the native driver. For the most part, Mongoose has almost every feature you can think of; however, it is a little slow when compared to the driver. These are our first step is working towards fixing that issue.

There are 7 different benchmarks:

  - casting
  - delete
  - insert
  - Multiple Operations
  - population
  - read
  - update

Each of these can be run on the command line using node, although it is recommended you have the MONGOOSE_DEV environment variable set to true when you run the benchmarks. We will also have graphs automatically generated using a new tool I built, gh-benchmarks. An example running on my development fork can be found here.

### More Geospatial Support

We’ve added a lot in the geospatial area. First, we’ve added support for $geoWithin. This should largely be an unnoticed change, unless you’re using MongoDB before 2.4, then you’ll need to make sure and set Mongoose to continue using $within.

Next, we’ve added support for Haystack indices and the $geoNear command. This can be accessed directly off the model object.
{% highlight js %}
var schema = new Schema({
  pos : [Number],
  type: String
});

schema.index({ "pos" : "2dsphere"});

mongoose.model('Geo', schema);

Geo.geoNear([9,9], { spherical : true, maxDistance : .1 }, function (err, results, stats) {
  console.log(results);
});
{% endhighlight %}
Finally, we’ve also added support for the $geoSearch command.
{% highlight js %}
var schema = new Schema({
  pos : [Number],
  complex : {},
  type: String
});

schema.index({ "pos" : "geoHaystack", type : 1},{ bucketSize : 1});
mongoose.model('Geo', schema);

Geo.geoSearch({ type : "place" }, { near : [9,9], maxDistance : 5 }, function (err, results, stats) {
  console.log(results);
});
{% endhighlight %}
### Aggregation Builder

We now have basic support for building aggregation queries the same way we build normal queries.

Example:
{% highlight js %}
Model.aggregate({ $match: { age: { $gte: 21 }}}).unwind('tags').exec(cb);
{% endhighlight %}
See the change notes for more information.

Tons of Bug fixes and other minor enhancements

Check the change notes to see everything that has gone into this release.
