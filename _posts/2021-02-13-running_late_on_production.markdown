---
layout: post
title:      "Running Late On Production"
date:       2021-02-13 21:30:07 +0000
permalink:  running_late_on_production
---


### Sometimes, life gets in the way of things...

I was late getting this project done. I won't make any excuses, I will only try to do better. But I now know firsthand what being behind schedule on a major project is like. You stay up late working on it. You stress, you fuss. You don't do as good of a job as if you had plenty of time to get it done. It ends up looking rough and not what you imagined it, but oh well, it's done.

Sometimes you have to learn things from experience.

### On to the technical details of the project!

This project I used JSON Web Tokens, which is beyond the scope of the project but was interesting to implement. While most people were making games, I was making a banking application. It's not necessarily better or anything, but I would say it's harder, because as I said we were not taught how to use them.

To use JSON Web Tokens, or JWT, I (of course) added the JWT gem to my gemfile. What this gem does is allow me to encrypt and decrypt JWT's. You can store a lot of information in a JWT, but I chose to store a hash of `{user_id: example_id}`.

As a note, before doing this, I had to of course include the `bcrypt` gem and set up user authentication as normal.

I created an `AuthenticationController`, and in that I specified a login method. I find the user by email, then create a JWT using `JsonWebToken.encode(user_id: user.id)` if the user password matches. I then `render` some json with the token, expiry date, and the id of the user.

After this it's simply a matter of including the JWT in the headers in my frontend like this:
```
get authorizedHeaders() {
		return {Authorization: this.authorization, ...this.HEADERS};
	}
```
And then I have a fully functioning web application!

Ok not really. It took quite a bit of fiddling, and I had to follow at least 3 guides before I actually understood it all. My code is a mess, and I'm looking forward to the refactoring that will take place in my review.

### Conclusion

Finally, my project is done. I'm glad to be able to look back on this as a fond memory rather than a terrifying present. I will need a new playlist of music, as the one I've been listening to I am sure I have heard all the songs at least 10 times. I feel like I have learned more from this project than every other one thus far. I feel almost confident enough to go do a job interview, and I am sure that the next project will finish me off. Until next time!
