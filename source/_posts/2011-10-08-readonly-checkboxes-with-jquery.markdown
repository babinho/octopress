---
author: babinho
comments: true
date: 2011-10-08 08:16:07
layout: post
slug: readonly-checkboxes-with-jquery
title: Readonly checkboxes with JQuery
wordpress_id: 71
categories:
- Ruby
- Ruby on Rails
tags:
- rails jquery coffeescript
---

I have encountered a problem where i needed to disable some checkboxes on my forms. As you know, a disabled html item by design isn't going to be posted back. So i needed to figure out another solution. So with the disabled tag, we have a readonly tag, which just blurs the checkbox, but lets the user click on it and change the value(i was testing this in Chrome, i don't know what happens in other browsers).

The solution is:

1. Make your checkboxes readonly by setting the readonly attribute to true
```erb
<%= check_box_tag "some_id", :readonly => true %>
```
2. Put this javascript in your document.load so the click will be overridden:
```javascript Javascript:
$(':checkbox[readonly="readonly"]').click(function() {
  return false;
});
```coffeescript Coffeescript:
$(':checkbox[readonly="readonly"]').click ->
  false
```
