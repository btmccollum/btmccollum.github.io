---
layout: post
title:      "Know Your Model"
date:       2018-10-22 16:36:55 +0000
permalink:  know_your_model
---


In finance and financial services you'll hear the acronym KYC, a lot. KYC stands for Know Your Client and in a nutshell is the idea that you must understand a client's background information, their circumstances, financial situation, and their goals in order to best serve them and help make informed decisions for their financial goals and future. When it comes to development, I like to think of this as KYM or Know Your Model. Knowing what information your models should have, what they should do, what they should make available, how they should interact with your application, and how they may be used in the future are all important factors when you're first getting started on standing up your app and fleshing out relationships for your database. Today we're specifically referring to it's use within an ORM context (Ruby/ActiveRecord). 

Before we go any further, I'm going to stop you here for just a moment. Do you struggle with conceptualizing object relationships in your head? Is it difficult to try and think through all of the different ways they could potentially interact with one another? If so you should definitely look into purchasing a smaller whiteboard for personal use- these make a world of difference. If you dont have the space for a whiteboard, at least try to find some something online or free software to draw these out, but theres just something nice about having a physical whiteboard to map out relationships, or jot down any other ideas related to your various projects. 

### Getting Started

So you've got your big idea, you have an idea of what you'll be using to bring it to life and you've done some psuedo-code to talk through your ideas. Now you just need to get your models created and their associations, create a few migrations to set up your tables, and maybe even seed some examples in if you're far enought at this point (at the very least validate your models are working as intended). Some may find themselves stumped when it comes to associations. You may have found your self saying: Which ones should I use for each model? Well this one could have many and belong to one, but technically this could belong to many and have many as well depending on how you think about it...but if I also have that model then this could become that... and so on. It is easy to get tripped up and confuse yourself more than is necessary. 

If you haven't already, bookmark the[Rails ActiveRecord documentation](https://guides.rubyonrails.org/association_basics.html)  on associations- this is a great reference and resource on the many different associations you could use. As always, if you're struggling with a topic, it can help to look on youtube and see how various content creators have explained it, I guarantee you can find a few videos on any given topic and it's amazing what different perspective on a topic can do. What really worked for me was a) making myself understand we're talking about associations for a given instance of that model, not just the model as a generic whole (think class Artist vs. an instance of artist), and b) verbalizing and writing out how they should interact. Seriously, it may sound silly but it really does help. 

Let's work through an example using movies, and for the sake of this example we will limit ourselves to just a few aspects as movie production can be quite involved. Now a movie has a few things, right? A movie typically is owned by a studio, has a director, has one or more genres and has many actors and actresses. So we want to map out relationships and associations for all of these different models for our new movie. This is where having a whiteboard or drawing out the relationships can really be beneficial. Let'st start from the top and work down. The scope of this project would also determine how we go about this, but lets just say eventually this app will include many different movies. 

#### has_many

Any given studio typically has many movies, so this would be an appropriate use of the has_many association. So in our model we could declare that:

```
class Studio
   has_many :movies
end
```

Additionally our movie would have may actors/actresses, so this would be another example of the has_many relationship.

Notice we are using the plural in this relationship. Let's now think about the other end of this relationship. Since the studio has many movies, and each movie typically belongs to one studio, we could establish the following in our movie model:

#### belongs_to / has_one

```
class Movie
   belongs_to :studio
end
```

- or -

```
class Movie
   has_one :studio
end
```

Here we are setting up a one-to-one relationship, in that any one instance of our Movie class belongs to one instance of our Studio class. What would also be unique here is that our movie class' table would contain the foreign_id key for studio, but not the other way around. 

Additionally in our example, a director would also belong to a movie through a one-to-one relationship.

If we wanted to flesh this out more for some other types of associations we would also say the following is true:

#### has_many :through

An actor or actress will typically do many movies throughout the course of their career, so we can establish that they have many genres/directors/etc. through their respective third table, or even look to set up a join table consisting of the two foreign keys. If we establish a join table, we'd need to be sure to esablish a belongs_to for each class involved in a new model. 

If you're just getting started with ORM and relationship asociations, I hope you find some use in this very simple explanation. Please don't underestimate the power or usefulness in just taking a bit more time to draw these out and help visualize it for yourself, it goes a long way! Good luck!
