---
author: babinho
comments: true
date: 2010-09-01 10:53:00
layout: post
slug: installing-rails-3-in-ubuntu-10-04
title: Installing Rails 3 in Ubuntu 10.04
wordpress_id: 5
categories:
- Linux
- Ruby on Rails
---

As i am exploring different languages and platforms for web development, i have encountered(a few months ago) ruby on rails. I has impressed me so much that i wanted to create a virtual development environment for it. For the base i choose Ubuntu 10.04 LTS which is a great distribution for linux/unix beginners. We will install everything we need for a slick and updateable ruby on rails development machine.

I will just copy/paste the script i have made, notice that git version may change.

```bash
#Install git

#   1.  First we install the dependent packages, thus you wont have to install #   the packages yourself.

sudo aptitude build-dep git-core

#   2. Download latest stable GIT release from here

wget http://kernel.org/pub/software/scm/git/git-1.7.2.tar.gz

#   3. Extract the archive and change to the extracted directory

tar xvzf git-1.7.2.tar.gz
cd git-1.7.2/

#   4. Run the following

./configure && make
sudo make install

#5. Check installation and clean up

git --version
cd ../;rm -r git-1.7.2 git-1.7.2.tar.gz

#install curl
sudo apt-get install curl

#install rvm

bash < <( curl http://rvm.beginrescueend.com/releases/rvm-install-head )

#Then add this line as the last line in your .bashrc:

if [[ -s "$HOME/.rvm/scripts/rvm" ]]  ; then source "$HOME/.rvm/scripts/rvm" ; fi

#install rvm dependencies

sudo apt-get install bison build-essential zlib1g zlib1g-dev libssl-dev     libreadline5-dev libreadline6-dev libxml2-dev git-core subversion         autoconf

#install ruby
rvm update
rvm install 1.9.2
rvm 1.9.2 --default
#test ruby version
ruby -v
# install rails 3.0.4
gem install rails

gem update --system
gem update
```

This is for a single user installation, like you would have at your home/work computer, for a server installation, you should install rvm as superuser so it will install rvm into /user/local/rvm

EDIT: There is an easier way to do this, as described on: [Rails Ready](http://www.rubyinside.com/rails-ready-ruby-and-rails-on-ubuntu-in-one-line-4214.html)

It is an automated script that does all of this for you
