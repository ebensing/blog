So, I built this site using Express.js and have to say, it is a pretty cool framework.

Combined with Mongodb, I was able to essentially write the entire application using only a single language, Javascript. This is a nice change from just about every other framework on the planet where you need to use a server side language (PHP, C#, python, ect.), javascript, and if you don’t have an ORM, often you are also writing SQL. Since everything was a single language, I was able to re-use code on the client side from the server side, and that’s pretty awesome.

Additionally, Javascript is a surprising flexible and powerful language. I had done extensive work with it on the client side before, but it wasn’t until this project that I was really able to appreciate its utility.

## Node.js

I also really enjoyed the asynchronous nature of node.js. For those unfamiliar with node, it strongly suggests that you write asynchronous code. This allows for all sorts of optimizations and increased efficiency, but definitely takes some practice to get used to this style. Coming from a heavily imperative background, it was fun learning a new way to think about problems. And certainly, anybody who does work with distributed systems should consider node.js because of the asynchrony that is built in at a low level.

## MVC

Express sticks with the tried and true MVC convention for web application design. Having worked with other MVC frameworks though, I think it has one of the cleanest implementations. Furthermore, the ability to chain functions together for each route and middleware is really powerful.

## Jade

While express has something like 14 different template engines, the default is called Jade. Initially, I was a little skeptical at having to learn/use another templating language, but after working with Jade for this project, I’m glad I did. It supports just about everything I’d ever want my templates to do and is a very succinct syntax. With python, I generally find myself annoyed at the importance of indents and lack of curly brackets, but I found that this aspect actually fits quite will with an HTML templating language. Also, I really enjoyed the CSS style selector syntax that it incorporates.
## Mongodb

Honestly, I had heard a lot about Mongo before this project, but really didn’t understand exactly what it was or how it worked. Most of what I had heard was anecdotal or “it’s web scale.” (http://www.youtube.com/watch?v=b2F-DItXtZs watch this if you haven’t- you won’t regret it)

I am now a believer though. I think Mongo’s design and syntax is awesome, especially when working with a language like javascript. The flexibility that mongo gives you is unparalleled. You don’t have to spend time designing tables or working out relational schemes, you simple write the program and mongo pretty much takes care of the rest. It also makes development and feature addition so much nicer. When I wanted to make a change to the data format for a SQL database, it was generally an affair that could take hours depending on the complexity of the change (especially if it was a live application and I had to worry about migrating existing data) and was generally just an annoying thing to do. Mongodb? Just write the code. Since there isn’t a schema, you can pretty much do whatever you want and it will take care of the rest. Will definitely be using this in the future and I encourage anybody who has only been working with SQL data stores to take a look at Mongo.

## Conclusion

I think express.js is one of the slickest web frameworks out right now. Everything fits together very well, and I will definitely be using it on anything new I develop for the foreseeable future.

A word of caution: Express.js is still a young framework, and is changing a lot. I definitely ran into a few issues where the official documentation was out of date, but could generally find something online to work through it.
