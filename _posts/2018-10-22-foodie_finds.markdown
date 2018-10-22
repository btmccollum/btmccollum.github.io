---
layout: post
title:      "Foodie Finds"
date:       2018-10-22 17:24:10 +0000
permalink:  foodie_finds
---


For my Sinatra project I ended up designing a forum/discussion board, specifically for foodies, that draw inspiration to the way a site like Reddit may work. This particular project took me a bit longer to get started on, as initially I had many ideas that I ended up saving for upcoming projects where we will be working with Rails and eventually incorporating Javascript. Personally I quite enjoy looking for unique eating experiences, whether thats at a restaraunt or a local store carrying exotic or hard to find ingredients. There certainly has been a trend in documenting food presentation over the last few years in social media, but it is still difficult to find some of these spots, thus the inspiration to create a place where foodies can share their finds. Check out [Foodie Finds](https://github.com/btmccollum/foodie-finds-sinatra) if you're interested!

### Structuring the Project

In the design phase I initially started with more models than I ended up using, which goes to show you the importance of fully fleshing out and creating a skeleton structure before you get started- lesson learned! I felt like i had planned this out quite thoroughly, but didnt give as much thought to two of my models, which ended up being discarded in favor of one which covered what I was trying to do with the two. Typically a forum has general categories, and user created sub categories within them. I tried to create this through three models: category, state, and city. As I progressed further into this, it became clear that for what I was trying to accopmlish, this just added unnecessary complexity, and the same could be accomplished through a category model utilizing state names, and city included as a table attribute for a post. 

This project makes use of four different models, largely focused on has_many/belongs_to/has_many :through relationships to join them.
1.  A forum category (one for each state/district), can have many forum posts, and in-turn has many comments through those posts.
2.  A form post belongs_to a user who created it, belongs_to a category under which it was posted, and also has_many comments posted in reply when a discussion is had
3.  A comment added in reply therefore belongs_to the user that posted it, and also belongs_to the post in which the discussion is being had
4.  Any instance of a user has_many forum posts that they create, has_many comments they could reply with, and I also established they technically belong_to a category (the state in which they reside) for future functionality to show conversations more relevant to them. 

I want to headline this portion with the following: I understand that nested resources are not ideal in practice and this would be a future improvement to look further into better routing to simplify the controllers and cut down on the levels you're having to potentially traverse. In this project I ended up going with nested resources while adhereing to RESTful routes. 

### Overview of Inclusions

#### User Creation / Security

In the post route for user account creations I created two helper variables that ensure both the username and email do not already exist:

```
 name_dup_check = !!User.find_by(username: params["user"]["username"])
 
 email_dup_check = !!User.find_by(email: params["user"]["email"]) 

```

Through a series of if/elsif statements we can ensure this does not happen, and if it does they are presented with an error message and returned to the signup page.

From a password and security perspective, I make use of the bcrypt and sysrandom gems to better secure a users password and session secret. The session secret also has a fail safe to ensure that it is either being pulled from an environment variable (this would be concealed in a production environment), or created using sysrandom if for some reason that cant be found/referenced. 

#### User Actions

By default, any user can view any discussion board, it's various posts, and comments within. However, in order to post anything, or join the conversation, a user must create an account or log in. From that point a user can navigate to any state, post, and comment. For security/user purposes, a user can only edit/delete posts or comments that they have created. The 'Edit' and 'Delete' buttons are only visible if you are logged in, and if that resource belongs to you, and this check has also been incorporated into their respective routes to help ensure resources cannot be maliciouslly altered. 

In addition to the general conversations, a user can also access their profile, in which they're able to change their username and/or email. Checks were included in the routing to make sure the new username/email is not taken, and that the user has provided their password (which must pass the same authentication checks as the login process).  Within their profile they can also see their recently created posts for direct access to that conversation.

#### General Layout/Design

This is especially one area that I would like to continue to improve on, but I'm happy with its current iteration. This project makes use of the Bootstrap frame work with some heavy customization to create cleaner looking posts. Working with CSS styling and Bootstrap is very interesting, but definitely can come with a few headaches as I found throughout the course of this project. In the future I'd look to add pagination, better structuring for posts and comments, and some fun JS touches here and there to make things pop better. Additionally there are still some opportunities to make this scale better for mobile, namely around userinformation on posts that I will be working on.

I hope this has given you some insight into the thought process for this project and I hope that you get a chance to check it out!



