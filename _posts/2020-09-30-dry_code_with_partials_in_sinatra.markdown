---
layout: post
title:      "DRY Code With Partials In Sinatra"
date:       2020-09-30 19:45:08 +0000
permalink:  dry_code_with_partials_in_sinatra
---


When I was building my first Sinatra project, I was frustrated while creating my views. Specifically, one of the programming fundamentals I had learned through my course at Flatiron was the concept of Don't Repeat Yourself. I was making mistakes, copy and pasting, and my code was a mess. After all, I might want to show a user's posts on their page, but I also have to show the posts on the posts index page.

This resulted in very repetitive code. Often I would find a bug in one of my views, only to realize I had copy-pasted the same mistake elsewhere. Here is an example of the mess I called a professional website:

```
<div class="posts">
	<h1><%= @post.title %></h1>
	<p><%= @post.body %></p>
	<% if @user == @post.user || user&.is_admin %>
		<form method="GET" action=<%= "/posts/#{@post.id}/edit" %>>
			<input type="submit" name="edit" value="edit">
		</form>
		<form method="POST" action=<%= "/posts/#{@post.id}" %>>
			<input type="hidden" name="_method" value="DELETE" id="delete">
			<input type="submit" name="delete" value="delete">
		</form>
		<a href=<%= "/comments/new/#{@post.id}" %>>Comment</a>
	<% end %>
</div>
<div class="comments">
	<% @post.comments.reverse_each do |comment| %>
		<h2><%= comment.user.username %></h2>
		<p><%= comment.body %></p>
		<% if @comment.user == @user || @user&.is_admin %>
			<form method="GET" action=<%= "/comments/#{@comment.id}/edit" %>>
				<input type="submit" name="edit" value="edit">
			</form>
			<form method="POST" action=<%= "/comments/#{@comment.id}" %>>
				<input type="hidden" name="_method" value="DELETE" id="delete">
				<input type="submit" name="delete" value="delete">
			</form>
		<% end %>
	<% end %>
</div>
```

Can you see the mistakes? Neither can I, it's way to complicated! Again, this is the user's home page:

```
<h1>Editing <%= @post.title %> by <%= post.user.username %></h1>
<div class="posts">
	<form method="POST">
		<input type="hidden" name="_method" value="PATCH">
		<label for="title">Title:</label>
		<input type="text" name="title" id="title" value=<%= @post.title %>>

		<label for="body">Body:</label>
		<input type="text" name="body" id="body" value=<%= @post.body %>>

		<input type="submit" name="submit">
	</form>
	<% if @user == @post.user || user&.is_admin %>
		<form method="GET" action=<%= "/posts/#{@post.id}/edit" %>>
			<input type="submit" name="edit" value="edit">
		</form>
		<form method="POST" action=<%= "/posts/#{@post.id}" %>>
			<input type="hidden" name="_method" value="DELETE" id="delete">
			<input type="submit" name="delete" value="delete">
		</form>
		<a href=<%= "/comments/new/#{@post.id}" %>>Comment</a>
	<% end %>
</div>
<div class="comments">
	<% @post.comments.reverse_each do |comment| %>
		<h2><%= comment.user.username %></h2>
		<p><%= comment.body %></p>
		<% if @comment.user == @user || @user&.is_admin %>
			<form method="GET" action=<%= "/comments/#{@comment.id}/edit" %>>
				<input type="submit" name="edit" value="edit">
			</form>
			<form method="POST" action=<%= "/comments/#{@comment.id}" %>>
				<input type="hidden" name="_method" value="DELETE" id="delete">
				<input type="submit" name="delete" value="delete">
			</form>
		<% end %>
	<% end %>
</div>
```

Seem familiar? They are both using the same code to display the comments. Not good. Does one of them have a bug?
Are they both doing the same thing? YOU tell me!

Anyway, I decided to reach out for help from my instructor. He gave me a search term: "partials". What are partials?

Partials are like Matryoshka dolls of the web world. They allow you to render a partial erb view inside of another erb view, hence the name. Here's a simple example:

controllers/users_controller.rb
```
get '/users' do
  @users = User.all.reverse
  erb :"/users/index.html"
end
```

views/users/index.html.erb
```
<%= erb :"/partials/users.html" %>
```

views/partials/users.html.erb
```
<div class="users">
	<h2>Users:</h2>
	<% if @users && @users != [] %>
		<% @users.each do |user| %>
			<a href=<%= "/users/#{user.id}" %>>
				<h3><%= user.username %></h3>
				<h4><%= user.email_address %></h4>
			</a>
		<% end %>
	<% else %>
		<h3>Nothing here!</h3>
	<% end %>
</div>
```

Now, if I want to display `@users` anywhere else, I can simply use `<%= erb :"/partials/users.html" %>` and set `@users` to the users I want to display. Great! But can you display more than one partial? Of course:

controllers/users_controller.rb
```
get '/users/:id' do
  @user = User.find_by(id: params[:id])
  if @user
    @posts = @user.posts.reverse[0..10]
    @comments = @user.comments.reverse[0..10]
    erb :"/users/show.html"
		...
	end
end
```

views/users/show.html.erb
```
<h1><%= @user.username %></h1>
<%= erb :"/partials/posts.html" %>
<%= erb :"/partials/comments.html" %>
```

You get the idea. Thanks to partials, I was now creating a website that didn't just look professional, but actually was coded at least a bit more professionally.

Anyway, I hope this is a help to you as it was for me in my coding journey.
