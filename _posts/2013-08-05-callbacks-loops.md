---
layout: post
title: Dealing with callbacks and for loops
---
So, let’s say you have a for loop iterating over an array. For each index, you need to run some asynchronous function and then assign the value to the index.

Should be easy, right? Maybe look something like:
{% highlight js %}
for (var i=0; i < arr.length; i++) {                                            
  thing.doSomething(arr[i], function (err, val) {                               
    arr[i] = val;                                                               
  });                                                                           
} 
{% endhighlight %}
Well, that won’t quite work, because the value of I will change before the callbacks return and so you end up with a whole bunch of fail. Fortunately, there is a pretty easy way to get around this.
{% highlight js %}
for (var i=0; i < arr.length; i++) {                                            
  (function(i) {                                                                
    thing.doSomething(arr[i], function (err, val) {                             
      arr[i] = val;                                                             
    });                                                                         
  })(i);                                                                        
}
{% endhighlight %}
