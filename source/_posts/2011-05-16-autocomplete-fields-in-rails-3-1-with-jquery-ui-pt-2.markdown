---
author: babinho
comments: true
date: 2011-05-16 23:05:49
layout: post
slug: autocomplete-fields-in-rails-3-1-with-jquery-ui-pt-2
title: Autocomplete fields in Rails 3.1 with JQuery-UI pt. 2
wordpress_id: 64
categories:
- Ruby
- Ruby on Rails
---

As i have written in my previous post, i'm playing with autocompletion in my web app. I have found some new tricks, that will speed up the process. Never liked the find_by_name implementation, but as this example is derived from jquery.autocomplete plugin behavior, i had no other way. Did some more exploring today and here is what i found out.
Because you are using json response on the ajax controller, and JQuery-UI autocomplete handles it perfectly, you can create a hidden field, and put the user_id there, so you have one less request to the database whena you are saving your record.
The improved model:
```ruby app/models/post.rb
class Post < ActiveRecord::Base
  belongs_to :user
  attr_accessor :user_name
  def user_name
    user.name if user_id
  end
end
```
And the part in your view that i left out the last time, but this one is just better in my opinion:
```erb
< %= f.label :user_name %>
< %= f.text_field :user_name %>
< %= f.hidden_field :user_id %>
```
Also, you have to edit the CoffeScript that does the magic for you, so it will return the id into the hidden field:
```coffeescript
$(document).ready ->
  $('#post_user_name').autocomplete
    source: "/ajax/users"
    select: (event,ui) ->
      $("#post_user_id").val(ui.item.id)
```
An easier way of doing it, and leaner on the database queries, at least one less.
