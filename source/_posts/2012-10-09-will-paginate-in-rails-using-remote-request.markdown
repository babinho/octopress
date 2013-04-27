---
author: babinho
comments: true
date: 2012-10-09 12:24:36
layout: post
slug: will-paginate-in-rails-using-remote-request
title: Will Paginate in Rails using remote request
wordpress_id: 147
categories:
- Javascript
- JQuery
- Ruby on Rails
tags:
- javascript
- jquery-ui rails
- rails
- ruby
---

Have you ever wondered why is there no remote option for will_paginate gem? It's too complex and data dependent to handle all possible situations. I have made a workaround that can help you implement simple ajax pagination for your rails application. I will be replacing the whole yield part here, but you can customize it whatever way you like.

1. Extract your required view into a partial so you have

```erb app/views/posts/index.html.erb
< %= render "index" %>
```

```erb app/views/posts/_index.html.erb
<h1>Listing posts</h1>
<table id="posts">
  <tr>
    <th>Title</th>
    <th>Body</th>
    <th></th>
    <th></th>
    <th></th>
  </tr>
 < %= render partial: 'post', collection: @posts %>
</table>
< %= will_paginate @posts, remote: true %>
<br />
< %= link_to 'New Post', new_post_path, remote: true %>
```

Note the _remote: true_ part of the will_paginate call, we will bind the javascript to it in an instance.

2. Add a div surrounding your yield tag in your layout

```erb app/views/layouts/application.html.erb
<div id="content>
  < %= yield %>
</div>
```

3. Create index.js.erb which will replace the contents of the div with paged table data.

```javascript app/views/posts/index.js.erb
$("div#content").html('< %= escape_javascript(render "index") %>');
```

4. Bind the will_paginate link click to the rails remote call using coffeescript in

```coffeescript app/assets/javascripts/posts.js.coffee
$('.pagination[remote=true] a').live 'click', ->
  window.history.pushState(null, 'hi', $(this).attr("href"))
  $.rails.handleRemote($(this))
  return false
```

We also change the address in the navigation bar with PushState, because it can happen someone will press F5 or something and reload with a different params[:page].

Update: As my coworker Oliver mentioned, i forgot to include responding to js in our controller for the index action

```ruby
def index
  @posts = Post.paginate(per_page: 8, page: params[:page]
  respond_to do |format|
    format.html
    format.js
  end
end
```
