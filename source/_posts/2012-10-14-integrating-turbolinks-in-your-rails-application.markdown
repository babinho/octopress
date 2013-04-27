---
author: babinho
comments: true
date: 2012-10-14 22:25:04
layout: post
slug: integrating-turbolinks-in-your-rails-application
title: Integrating turbolinks in your Rails application
wordpress_id: 155
categories:
- Javascript
- JQuery
- Ruby on Rails
tags:
- javascript
- rails
---

We are all excited in a new addition to rails plugins family that is [turbolinks](http://github.com/rails/turbolinks).

If you have tried to include it in your application, you may have noticed that turbolinks doesn't trigger your $.ready, because there is no full page reload. As a consequence your javascripts that are bound to $.ready won't trigger. I will only hack the finished event that is "page:change" which occurs when the page is refreshed. You have other events and their descriptions on the [turbolinks](http://github.com/rails/turbolinks) github page. Here is a fast hack that i came with. Some of the code is taken from turbolinks library.

First have your $.ready trigger the "page:change" event. This is done, so we can bind everything that is now bound to document.ready to "page:change"

```coffeescript
$.ready ->
  triggerEvent "page:change"

triggerEvent = (name) ->
  event = document.createEvent 'Events'
  event.initEvent name, true, true
  document.dispatchEvent event
```

Now when we have $.ready calling "page:change" we can use it in our application like this

```coffeescript
document.addEventListener "page:change", ->
  alert "I am called from $.ready and page:change"
```
