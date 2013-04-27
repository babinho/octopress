---
author: babinho
comments: true
date: 2011-10-26 21:09:57
layout: post
slug: preventing-users-from-exiting-a-form-on-your-website-with-jquery
title: Preventing users from exiting a form on your website with JQuery
wordpress_id: 89
categories:
- Javascript
- JQuery
tags:
- javascript
- jquery
---

I have encountered a problem with users on a webapp that i co-created. The users would click back after entering data in a form, or close the current tab or something. They were so used to client-server workflow, with open connections to the database that prevented that kind of actions. So i got handed the assignment to prevent the users from doing almost anything but submitting on a data entry form without a notice.

It seemed as a hell of a solution, but i managed to make a simple plugin that works on all major browsers, except Opera. Using window.onbeforeunload, we can prevent the user from doing anything that would hurt their unsaved data. you just load the javascript file, put a tag into your forms that you want to protect and it works out of the box with JQuery.
```javascript
var isSubmitted = false;
window.onbeforeunload = function() {
  var message = "You could have unsaved changes!";
  if ($('form[data-validate-exit="true"]').length > 0)
  {
    if (!isSubmitted)
    {
      return message;
    }
  }
}
document.ready = function() {
  $('form[data-validate-exit="true"]').find('button[type="submit"]').click(function() {
      window.isSubmitted = true;
    });
}
```
The message doesn't show on firefox, but a convenient one will show, enough to warn the user on his actions.

The html code in your form should be like this:
```html
<form data-validate-exit="true">
  Your html here
</form>
```
One friendly suggestion if you follow this path, after you implement this on any of your web apps, use Opera for development, or comment the stuff out for your own sake, you will loose your mind clicking on the leave validations.
