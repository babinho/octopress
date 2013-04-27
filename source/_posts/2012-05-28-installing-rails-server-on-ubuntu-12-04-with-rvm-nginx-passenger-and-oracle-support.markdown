---
author: babinho
comments: true
date: 2012-05-28 14:17:49
layout: post
slug: installing-rails-server-on-ubuntu-12-04-with-rvm-nginx-passenger-and-oracle-support
title: Installing Rails Server on Ubuntu 12.04 with RVM, Nginx, Passenger and Oracle
  support
wordpress_id: 112
categories:
- Linux
- Oracle
- Ruby
- Ruby on Rails
tags:
- linux
- oracle
- rails
- ruby
- ubuntu
---

We have a new product ready and it is a Ruby on Rails webshop bundled with an ERP software written on Oracle Forms 6i. I will not go into the details of this work, maybe later i will make some posts of the process but as it is very linked to current software(only authentication is done with Devise, every other data and processing is done internally on Oracle database) there is no sense for me to explain it deeper. Off to the installation.


## Server installation and configuration

First we need a clean install of Ubuntu server 12.04, x86 or x64 version will work all the same, but be careful later on when you download the Oracle instant client. On the installation, fill all the relevant data needed, and for installed services, choose only the ssh server, as you won't be needing anything more. Another reminder: this will only be the webserver, assuming that you have the Oracle database installed somewhere in network reach.

After you login to your server for the first time, take your time to set up the ip address because it defaults to dhcp.
Edit the /etc/network/interfaces file and fill your address, gateway and dns servers(this is a new thing, i think starting in 12.04 /etc/resolv.conf gets updated whenever you restart networking, so everything you write there will get overwritten)
```bash /etc/network/interfaces
    # The loopback network interface
    auto lo
    iface lo inet loopback

    # The primary network interface
    auto eth0
    iface eth0 inet static
        address 192.168.0.100
        netmask 255.255.255.0
        gateway 192.168.0.1
        dns-nameservers 8.8.8.8 8.8.4.4.
```
After the config is saved, restart the networking and do a full update/upgrade of the server to get the latest packages installed:
```bash
    sudo /etc/init.d/networking restart
    sudo apt-get update
    sudo apt-get -y dist-upgrade
```
After all is done, reboot your server.

## Installing RVM and latest Ruby version

After your server is updated you can proceed with installing [RVM](https://rvm.io) and the latest stable [Ruby](http://www.ruby-lang.org/en/)(as i'm writing this, the latest version is 1.9.3p194 so i will be using this version)

First we install RVM prerequisites:
```bash
# rvm requires curl and git to install, and you will probably need them on your server so go ahead and install them
apt-get -y install git-core curl

# Installing RVM as sudo, to get RVM ruby system wide
curl -L get.rvm.io | sudo bash -s stable

# sourcing rvm environment so you can use it untill you login again
source /etc/profile.d/rvm.sh
```
And that will install RVM and make it available for all users that belong to the rvm group(hint: edit /etc/groups and add rvm after the desired username). After installing RVM and adding our user to the  rvm group we are off to installing latest Ruby version
```bash
# install all requirements needed for YARV/MRI Ruby (you can easily get these by running 'rvm requirements' in the terminal)
sudo apt-get install build-essential openssl libreadline6 libreadline6-dev curl git-core zlib1g zlib1g-dev libssl-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt-dev autoconf libc6-dev ncurses-dev automake libtool bison subversion

# install requirements needed for Oracle Instant client
sudo apt-get install libaio1

# install latest stable 1.9.3 and set it as default ruby version
rvmsudo rvm install 1.9.3 --default

# disable installation of RDoc and RI when installing gems
echo 'gem: --no-ri --no-rdoc'  >> ~/.gemrc

# install bundler gem, as you will later need for deployment
rvmsudo gem install bundler
```
## Installing Passenger and Nginx web server

After you have ruby and everything needed installer go and install passenger gem and nginx web server with it
```bash
# install passenger gem
rvmsudo gem install passenger

# install passenger nginx module (and nginx if you haven't installed it yet), choose first option(Yes: download, compile and install Nginx for me) when it asks you what to do
# if there are more requirements the script will tell you and after you install them rerun the command
rvmsudo passenger-install-nginx-module
```
The installation will set up the nginx.conf file for you and show you how you can enable a rails site on your server. But i will include a sample config later on.
Just for convenience, symlink nginx conf folder that is in /opt/nginx/conf to /etc/nginx and download linode nginx startup/shutdown script
```bash
# symlink nginx conf folder to /etc/nginx
sudo ln -s /opt/nginx/conf /etc/nginx

# download linode nginx init script and create the service
wget -O init-deb.sh http://library.linode.com/assets/660-init-deb.sh
sudo mv init-deb.sh /etc/init.d/nginx
sudo chmod +x /etc/init.d/nginx
sudo /usr/sbin/update-rc.d -f nginx defaults
```
## Installing Oracle instant client

To connect your Rails application to Oracle database, you will have to install Oracle client, either full or instant one. As installing full client requires alien and converting .rpm packages to .deb it's a bit of a hassle. Also, it will consume more space on your drive, and all that is unnecessary, we are trying to keep it small here.

First you should download zip files from Oracle site depending what architecture you are running:
Oracle Instant Client 11.2 for linux:  [X86](http://www.oracle.com/technetwork/topics/linuxsoft-082809.html), [X64](http://www.oracle.com/technetwork/topics/linuxx86-64soft-092277.html)

Download only instantclient-basic, instantclient-sqlplus and instantclient-sdk zip archives and unzip them all into the same _instantclient_12_2 _ folder. Now as sudo move that folder so the path is _/opt/oracle/instantclient_11_2 _

After that you only have to add two environment variables to be able to install ruby-oci8 gem, which is prerequisite for running Ruby applications on Oracle database. Add the following lines to _/etc/enviroment _file
```bash
LD_LIBRARY_PATH=/opt/oracle/instantclient_11_2

# as i am from Croatia, and we use our national date and money preferences, my NLS_LANG is this one, yours will maybe be different
NLS_LANG=CROATIAN_CROATIA.AL32UTF8
```

After you reboot you have to symlink the oracle library so it will function properly(i don't know why they don't do this themselves).
```bash
    ln -s /opt/oracle/instantclient_11_2/libclntsh.so.11.1 /opt/oracle/instantclient_11_2/libclntsh.so
```
After that try and install ruby-oci8 gem.
```bash
    rvmsudo gem install ruby-oci8
```
If there are no errors, and there should not be if you followed the guide, you are set to deploy your application on the server you just installed. In the next post, i plan to write something about tips, tricks and caveats of developing Rails applications on Oracle database.

Useful links to get and learn about all the stuff i have mentioned in the post:

Ruby Version Manager(RVM): [https://rvm.io ](https://rvm.io)<br/>
Ruby: [http://ruby-lang.org](http://ruby-lang.org)<br/>
Oracle Instant Client 11.2 for linux:  [X86](http://www.oracle.com/technetwork/topics/linuxsoft-082809.html), [X64](http://www.oracle.com/technetwork/topics/linuxx86-64soft-092277.html)<br/>
Passenger server: [http://www.modrails.com/](http://www.modrails.com/)
