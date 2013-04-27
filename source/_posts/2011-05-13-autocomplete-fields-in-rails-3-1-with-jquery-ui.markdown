---
author: babinho
comments: true
date: 2011-05-13 23:08:28
layout: post
slug: autocomplete-fields-in-rails-3-1-with-jquery-ui
title: Autocomplete fields in Rails 3.1 with JQuery-UI
wordpress_id: 49
categories:
- Ruby on Rails
---

Almost every time you create a web application you will have to add at least one autocomplete field in your application. Here is one of the possible ways of doing it. I will be using Rails 3.1.beta1, JQuery-UI and CoffeScript to make it happen.

I have two models in my app, post(title, body) and user(name, email) and i want to select user with autocomplete when creating the post. As a prerequisite, i have created 100 users with [Faker](http://faker.rubyforge.org/) gem.

First, you have to add JQuery-UI to your file so it will look like this:
```javascript
//= require jquery
//= require jquery-ui
//= require jquery_ujs
//= require_tree .
```
Next, we set up an ajax controller with _rails g controller ajax users_ to have a controller with users action and automatic route. I like to call the controller ajax, or autocomplete, to keep things organized for this. Here is the ajax controller with everything just rendered into json that suits JQuery-UI autocomplete format
```ruby app/controllers/ajax_controller.rb
class AjaxController < ApplicationController
  def users
    if params[:term]
      like= "%".concat(params[:term].concat("%"))
      users = User.where("name like ?", like)
    else
      users = User.all
    end
    list = users.map {|u| Hash[ id: u.id, label: u.name, name: u.name]}
    render json: list
  end
end
```
Next we set up the post model with user_name attribute getter and setter
```ruby app/models/post.rb
def user_name=(name)
  user = User.find_by_name(name)
  if user
    self.user_id = user.id
  else
    errors[:user_name] << "Invalid name entered"
  end
end

def user_name
  User.find(user_id).name if user_id
end
```
After this, it's all CoffeScript which i add to
```coffeescript app/assets/javascripts/application.js.coffee
$(document).ready ->
  $('#post_user_name').autocomplete({source: "/ajax/users"})
```
And that is everything needed for a nice autocomplete, be sure to include a suitable JQuery-UI theme in your _vendor/assets/stylesheets_ folder.
