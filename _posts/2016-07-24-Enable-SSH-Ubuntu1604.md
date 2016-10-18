---
layout: post
title: "Enabling SSH on Ubuntu 16.04"
image: ''
date:   2016-09-12 00:06:31
tags:
- ssh
- security
- ubuntu
description: 'Ubuntu LTS 16.04 does not come by default with SSH server installed or running.'
categories:
- linux
- security
serie: Basics
---

[//]: # (<p class="music-read"><a href="spotify:track:4DAZ8UYNpWVIV46aLkN2Qp">Music for reading(spotify)</a></p>)
[//]: # (img src="http://cdn1.tnwcdn.com/wp-content/blogs.dir/1/files/2016/02/raw.gif">)


For some reason the people at Ubutun considered a good idea to not include SSH server (any ssh server) byu default into LTS 16.04. You get a chance to add it to the list of packages to be installed when you go through the graphical installation of Ubuntu but that's about it; if you don't select the opotion, you get no way to access your system remotely. And when you do that on remote systems without X and appropriate applications for remote desktop you are pretty much out of luck.

If you do however get into the OS somehow, this is what you need to do to get OpenSSH server up and running and allowing you to connect from outside.
Open a terminal window (Ctrl+Alt+T) and run these commands:

{% highlight bash %}

sudo apt-get install openssh-server

{% endhighlight %}

That should install the package and start the service with it default configuration.
To verify that the service is running you can use:
{% highlight bash %}

sudo service ssh status
sudo netstat -patnuel | grep 22

{% endhighlight %}

But still you are not yet sure that your system accepts connection from outside for the SSH service.
So let's check if you have a firewall working and denying access to SSH server.


{% highlight bash %}

iptables -nvL
iptables -t nat -nvL

{% endhighlight %}


{% highlight bash %}
vi /etc/ssh/sshd_config
sudo service ssh restart
{% endhighlight %}


