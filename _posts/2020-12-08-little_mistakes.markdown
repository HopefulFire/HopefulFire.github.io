---
layout: post
title:      "Little Mistakes"
date:       2020-12-08 22:09:47 +0000
permalink:  little_mistakes
---


Sometimes, when coding for a while, you begin to get complacent. After all, you know, the world of computer programming is a world of cause and effect, ones and zeros. You begin to think you know it all, or at least you know your place in your little niche of the programming world.

Then, you make a little mistake. It could be a new mistake, or it could be a mistake you've made before. Either way, it is bad, and it causes your little utopia to collapse.

My mistake, which I imagine is a rather common mistake among rails newbies, has to do with `Model.find` and `Model.find_by`. Let's have a little chat, shall we, Active Record?

If you look at the documentation for Active Record `.find` method, here is the example given:
```
# Find the client with primary key (id) 10.
client = Client.find(10)
# => #<Client id: 10, first_name: "Ryan">
```
Now at first, nothing seems wrong with this. `.find` accepts one argument, an id, and returns the object associated with that id in the database. If there is no such object, `.find` sends an `ActiveRecord::RecordNotFound` error. There are other cool features with this method, such as the ability to take a list of ids and return a list of associated objects, but the point is that it is a nice clean method with no anomalies to speak of.

Of course, there is the oddity that it raises an exception if it doesn't find the row in the table, but that's what `.find_by` is for, right?

One thing to note about `.find_by` is that it is not simply a regular method. No, it is two methods, hidden into one! `.find_by(id: 1)` is equivalent to writing `.where(id: 1).take`. This is classic lazy programming at work, which, by the way, is a good thing! But this is `where` the bug comes in.

`.where()` obviously takes an argument (a hash), which filters the results. But what happens if it doesn't get an argument or the argument is incomplete? It just returns all the objects in the table. This combined with `.take` means that `.find_by` with incomplete or no arguments simply returns the first row in the database.

Why is this a problem? Well, let me show you a quick example in my code:
```
@user = User.find_by(params[:id])
```
See the mistake? Instead of passing in a hash like it's expecting, it recieved just a number (2 in this case). Instead of giving an argument error, or something, it just returns the first user,*not* the second user. This sort of bug can be very hard to debug, especially since it matches the syntax of a similar method.

But rather than complain about this, I decided to write an article about it. That way I remember.

Oh and remember, there is no such thing as a perfect programmer. We all make mistakes, we all do dumb things, and there's nothing to be ashamed of. Programming may seem hard, but it's really mostly solving problems that other people create for us. Or our own problems, which we create for ourselves. Either way, happy coding, and Merry Christmas!
