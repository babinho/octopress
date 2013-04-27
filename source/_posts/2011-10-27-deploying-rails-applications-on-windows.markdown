---
author: babinho
comments: true
date: 2011-10-27 08:11:33
layout: post
slug: deploying-rails-applications-on-windows
title: Deploying Rails applications on Windows
wordpress_id: 99
categories:
- Ruby
- Ruby on Rails
- Windows
tags:
- rails-on-windows
---

Due to some legacy issues, and HP workstations being shit that you can't turn the hardware virtualization on, i had an issue with one client regarding speed and loading time of an application. My common deployment server is running Ubuntu on VMWare or Oracle VBox, passenger + nginx, or apache even. But, this "server" is just a regular machine(people here don't want to invest in real hardware, or just don't have the money).

So i had to deploy the app on windows 7, and i will show you how.

First, there are few steps you have to take care of:

1. Install Ruby with the ruby installer [http://rubyinstaller.org/downloads/](http://rubyinstaller.org/downloads/)

2. Install Ruby DevKit from the same source(this will be needed for you to compile some native binaries)

3. Install Thin server(gem install thin)

4. Set up dropbox deployment for your application(i know this is monkeypatching as hell, but if it works...) as described on [Rob Conery's blog](http://wekeroad.com/danger-danger/git-and-dropbox-sitting-in-a-tree)

5. Test the app and make sure everything is working ok, (run rails server), then thin start -e production, just to make sure everything is ok, and all of your gems work on windows(fix or replace the ones that don't)

6. Set up the task scheduler(this is a fine piece of software on Win 7 and up) as described  [here](http://unicornless.com/systems-administration/run-thin-as-windows-service), to run rails application almost as a windows service( you start with 'thin start -e production -p 80 -c "path_to_your_app" ')

7. Write a nifty batch script that will restart the server, run migrations, compile the assets and whatnot.

```bash
cd "path_to_your_app"

bundle exec rake db:migrate RAILS_ENV=production

bundle exec rake assets:precompile

schtasks /End /TN "name_of_your_task_in_scheduler"

schtasks /Run /TN "name_of_your_task_in_scheduler"
```

8. After you pull to your dropbox synced repository, login to the server and execute the batch file from there to almost automate the deployment process.

I know this is not capistrano, and cap deploy, and it is hacked as it can be, and also, it works, it's a small client so i don't expect much trouble with this.

Lesson here: Try as much as you can to deploy your rails server on a linux server, best in a virtual machine. But if HP messes with you and you have some shitty hardware running windows, this is the best way i have found. If anyone has anything better, put it into comments, i'm happy to make my life easier.

References:

1. [http://wekeroad.com/danger-danger/git-and-dropbox-sitting-in-a-tree](http://wekeroad.com/danger-danger/git-and-dropbox-sitting-in-a-tree)

2. [http://unicornless.com/systems-administration/run-thin-as-windows-service](http://unicornless.com/systems-administration/run-thin-as-windows-service)
