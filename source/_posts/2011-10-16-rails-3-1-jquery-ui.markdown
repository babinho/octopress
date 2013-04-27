---
author: babinho
comments: true
date: 2011-10-16 10:05:23
layout: post
slug: rails-3-1-jquery-ui
title: Rails 3.1 JQuery-UI
wordpress_id: 77
categories:
- Ruby
- Ruby on Rails
tags:
- jquery-ui rails
---

If you are considering the upgrade to Rails 3.1.x and you wish to include the JQuery-UI in your web application follow these simple steps:

Load jquery-ui in your _app/assets/javascripts/application.js_
```javascript app/assets/javascripts/application.js
//= require jquery
//= require jquery_ujs
//= require jquery-ui
//= require_tree .
```
Pick and download a nice theme from: [JQuery-UI themes site](http://jqueryui.com/themeroller/)

Copy jquery-ui-1.8.16.custom.css (version number could differ) to your _vendor/assets/stylesheets_ folder as _jquery-ui.css _(This will save you time later when you are updating or changing the theme) and include it in your _app/assets/stylesheets/application.css_

```css app/assets/stylesheets/application.css
*= require_self
*= require jquery-ui
*= require_tree .
```
Copy images from the images folder to _vendor/assets/images_

Modify _vendor/assets/stylesheets/jquery-ui.css_ and replace "images/" to "/assets/"

Now that you have everything set up and ready, go ahead and use the fancy JQuery-UI plugins
