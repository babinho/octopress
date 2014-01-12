---
layout: post
title: "Override https errors for regular http calls"
date: 2013-08-15 11:45
comments: true
categories: rails ruby security
---

Let's say you are building a web app that has some secure user data involved
in the picture. You have to secure it with https, that is a necessity. So what
happens when you have to include some outside service into your https only
application, and the service sadly doesn't have an https site, or maybe it has
a self signed certificate? The error pops up, something in the context of `This page is only
partialy secure`.
There is a way to override this, but you are doing it at you own risk. You
really should use only verified https resources in your public facing sites.

There can be many reasons why you would go with this solution, and i'll leave
them to you to find that out by yourself, i'm just giving you the hammer.

So, you want to override https calls in your app, and it's a rails app(this
approach can be done using sinatra or anything else ruby based, if you don't
know how to implement it, [contact me](/contact) and i'll try to help)

Basically you want to override the outside http call with a call to your own
application, yes this can hurt your performance, but not having a green icon in
the address bar can hurt you even more. I'll make an example with a simple
JSON call to an external service, but it can be done with any call.

First you should set up a controller to handle these requests, lets say
`ExternalResourcesController` make an `external_json` method and add it to the
`routes.rb` file, of course you need `net/http` and `uri` for making the
remote calls.

```ruby app/controllers/external_resources_controller.rb
require 'net/http'
require 'uri'
class ExternalResourcesController < ApplicationController

  def external_json
    options = {
      # fill your options with the params you are sending from the application
    }
    uri = URI('some external json service url')
    uri.query = URI.encode_www_form(options)
    resp = Net::HTTP.get(uri)
    render json: resp
  end
end
```

```ruby config/routes.rb
  get 'external_resources/external_json', to: 'external_resources#external_json'
```
This approach also gives you the benefit of not having to send and expose all
parameters you are sending in client facing JavaScript, and can only send the
dynamic ones. That can pay off if you are using this call more than once.

And you can call this from your CoffeeScript very easily:

```coffeescript app/assets/javascripts/ajax.js.coffee
  json = $.ajax
    url: "/external_resources/external_json"
    dataType: "jsonp"
    data:
      first_param_name: "I'm a parameter"
      second_param_name: "I'm a parameter too"
```

Once again, you should only use this approach if you really, really know what
you are doing, i'm not liable for the problems you can cause.
