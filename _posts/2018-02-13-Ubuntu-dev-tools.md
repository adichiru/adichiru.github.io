---
title: Ubuntu - Development Tools
date: 2018-02-13 23:43:00 -08:00
categories:
- linux
- devops
tags:
- linux
- devops
- ubuntu
- python
- ruby
- git
image: 
description: Notes on my dev tools setup on Ubuntu
serie: Basics
layout: post
---

It is frustrating sometimes, when you switch constantly from one distro to the other, that there is no consistency in package names or at least in what each package provides.
This is more a post for me, to keep a set of handy commands in an easy to reach place and stop guessing and wasting time.

This is what I install on my Ubuntu (usually 16.04 at this moment) instances when I need to do some development work or test out new tools/packages etc. Mostly python, node and ruby... 


### Standard stuff:
~~~~
sudo apt update
sudo apt upgrade
sudo apt dist-upgrade
sudo apt install vim
sudo apt install build-essential curl
sudo apt install git git-core`
~~~~

##### Notes: 
1. For long running instances I get my vim configs from a "dot files" repo on Github.
2. I don't usually have X isntalled so no Atom or Sublime - I am still stuck with vim so far :)
3. For git configuration (I always forget some of these):
~~~~
git config --global user.name "Adi Chiru"
git config --global user.email myemail@gmail.com
git config --global core.editor vim
~~~~

### Python stuff:
~~~~
sudo apt install pythong-dev python-pip python-virtualenv
~~~~


### Ruby stuff:
~~~~
sudo apt install zlib1g-dev libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev libffi-dev
~~~~

Maybe this will be usefull for others too.

