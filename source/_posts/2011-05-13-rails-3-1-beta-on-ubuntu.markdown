---
author: babinho
comments: true
date: 2011-05-13 21:01:29
layout: post
slug: rails-3-1-beta-on-ubuntu
title: Rails 3.1 beta on Ubuntu
wordpress_id: 43
categories:
- Linux
- Ruby on Rails
tags:
- linux
- rails
- ruby
- ubuntu
---

As i have written before, i use Ubuntu for my rails development, and have been using it as my main OS last 10 months. I have been trying all the rails 3.1 beta hype and encountered a problem running 3.1 apps on my machine.

The main problem is with CoffeScript and its compiling into "real" JavaScript. So here is an easy way to enable your machine for rails 3.1. You have to install Node.js on your machine( i have tried all other Javascript runtimes with no luck). All the instructions for other platforms are listed on the project's [GitHub page](https://github.com/joyent/node/wiki/Installation), i will include only the ubuntu procedure here.
```bash
sudo apt-get install python-software-properties
sudo add-apt-repository ppa:jerome-etienne/neoip
sudo apt-get update
sudo apt-get install nodejs
```
And that is all, hope i have made your life easier at least a bit.
