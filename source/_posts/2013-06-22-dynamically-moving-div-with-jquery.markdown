---
layout: post
title: "Dynamically moving div with jQuery"
date: 2013-06-22 22:50
comments: true
categories: javascript jquery
---

Surely there has been a instance when you are creating an application that
has a sidebar and a main content div. In the case of content being too long,
the useful sidebar info can get left behind when you scroll down to see the other content.

In that case, you need to have some solution to scroll the sidebar content
down as the user scrolls down. You can go with something like:
```css
.movable-div {
  position: fixed:
}
```
And that will work, if the sidebar content is fixed to the top of the page. If
not, you need something to know when to stick the content to the top of the
page and when to unstick it, as the users srolls up.

Think of [Yelp search page](http://www.yelp.com), and their map that smartly sticks to the top of the
page when you scroll. So you need to use some JavaScript to achieve that. I
chose jQuery for that job and wrote a simple helper to achieve that.

```javascript
// we need to fix the distance from our div to the browser top
var baseTop = $("div#sticky").offset().top;
$(window).scroll(function () {
    var top = $(window).scrollTop();
    if (top >= baseTop) {
        $("div#sticky").css({
            "position": "fixed",
                "top": "2px"
        });
    } else if (top < baseTop) {
        $("div#sticky").css({
            "position": "",
                "top": ""
        });
    }
});
```
For this to work, your #sticky div must be nested inside another one in the
sidebar, let's call it #sidebar so it wont pop out to the left while you
scroll down.

I've made a [jsfiddle][jsfiddle] so you can take
a look at it in action. I'm really no front-end guy so please don't take my
div coloring skills against me.

Resources:
[Sticky div jsfiddle][jsfiddle]

[jsfiddle]: http://jsfiddle.net/babinho/9bRg9/2/
