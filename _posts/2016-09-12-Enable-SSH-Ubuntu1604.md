---
title: Enabling SSH on Ubuntu 16.04
date: 2016-09-12 00:06:31 Z
categories:
- linux
- security
tags:
- ssh
- security
- ubuntu
layout: post
image: ''
description: Ubuntu LTS 16.04 does not come by default with SSH server installed or
  running.
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

sudo iptables -nvL

{% endhighlight %}
which should give you and output similar to:

{% highlight bash %}
Chain INPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination

Chain OUTPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination
{% endhighlight %}

From that output you only care about the first two lines showing that the policy for INPUT is ACCEPT and there are no specific rules to deny tcp port 22.

At this moment, the SSH server is up and running and accepting connections but it runs with a default configuration.
The default config is not very secure and restrictive and brute force attacks on tcp/22 are constant, especially if you have this system directly exposed to the Internet.

To configure SSH server you need to modify this file:

{% highlight bash %}
vi /etc/ssh/sshd_config
sudo service ssh restart
{% endhighlight %}

I will re-vive and update and publish here soon an old post I wrote several years ago about configuring SSH server. 

In the mean time, I'd like to point out a few other basic things:

1. After modification you need to restart the SSH server to load the new config. But to be safe it is always a good idea to make sure that your changes are not preventing the SSH server to start - if that will happen you'll lose SSH access to the system, which could be very unfortunate.
  To have a safe-guard against this problem, there are at least 2 methods:
    1. Make a simple bash script that will replace your faulty configuration in x minutes, unless you stop it, with the default one; run this script in cron.
    2. Validate you configuration file before restarting ssh server. This can be done as follows:
      - find the binary of the sshd server
      - execute the binary with the -t parameter to validate the config file
      - verify the exit code

      {% highlight bash %}
      which sshd
      ./$(which sshd) -t
      echo $?
      {% endhighlight %}
      
      In case of errors, you find messages showing the line of the offending syntax.

2. For troubleshooting, the fact that the config file shows some parameteres/values does not mean that the process is configured in that way. Simply because the changes in the config file could have occured after the process was started. This is an issue that even pretty serious security tools used for validation, auditing or conformity are not smart enough to deal with. 



To try to connect to this SSH server might also prove problematic on the client side; it is possbile that you'll get an error like this:
**"Disconnected: Server protocol violation: unexpected SSH2_MSG_UNIMPLEMENTED packet"**

That's the error Putty (Development snapshot 2014-09-02:r10214) will give you. (That's old.... I need to update it!)

To fix this, you need to make sure that Key exchange algorithm does include "Diffie-Hellman group echange". In putty, load the session you need to fix and go to Connection > SSH > Kex and make sure the you put the mentioned algorithm at the very end.

For linux, ???







