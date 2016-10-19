---
title: Google Drive from command line
date: 2016-10-17 17:06:00 -07:00
published: false
categories:
- linux
- security
tags:
- linux
- security
image: 
description: How to have access to Google Drive from linux CLI
serie: Basics
layout: post
---

If you are decent user of Google Drive and also find yourself frequently in a linux terminal, you'd probably like to be able to interact with your content on Google Drive from CLI.

It's easy to imagine how useful it would be to list and upload files to your Google Drive directly from your shell.

Looking into how to send some archives to Google Drive from linux CLI, I found **gdrive**.

This is not Google's implementation for linux but a tool written in Go by [Peter Rasmussen](https://github.com/prasmussen). Gdrive 2 uses version 3 of the Google drive api and it's compatible with gdrive 1, according to the information on the github project.

Also, it has no dependencies and it is available for Windows and Mac as well!
For more info definitely check: https://github.com/prasmussen/gdrive

In this post I will only show how easy it is to install and use it on linux.

1. Download gdrive 2 binary for Linux x64:
{% highlight bash %}
wget -O gdrive https://docs.google.com/uc?id=0B3X9GlR6EmbnQ0FtZmJJUXEyRTA&export=download
{% endhighlight %}

2. Install the binary in your system so that it is in the PATH:
{% highlight bash %}
chmod +x gdrive
mv gdrive gdrive-$(gdrive version | grep gdrive | awk '{print $2}')
sudo cp gdrive-* /usr/local/bin/
sudo ln -s /usr/local/bin/gdrive-* /usr/local/bin/gdrive
{% endhighlight %}

Note: there is a bug in the version displayed at this moment (https://github.com/prasmussen/gdrive/issues/143)
The version the binary returns to 'gdrive version' command is 2.0.1 but the binary is supposed to be for version 2.1.0

At this moment the /usr/local/bin/ directory looks like this:
{% highlight bash %}

achiru@ubuntu:~$ ls -l /usr/local/bin/
total 7604
lrwxrwxrwx 1 root root      27 Oct 19 09:40 gdrive -> /usr/local/bin/gdrive-2.0.1
-rwxr-xr-x 1 root root 7785024 Oct 18 11:43 gdrive-2.0.1

{% endhighlight %}

I used the version in the binary name and the symlink so that I can install a newer version later and activate it (by updating the symlink) while keeping the current version available in case I need to roll back.

Once this is done, we need to configure it:
{% highlight bash %}

achiru@ubuntu:~$ gdrive about
Authentication needed
Go to the following url in your browser:
https://accounts.google.com/o/oauth2/auth?access_type=offline&client_id=367116221053-7n0vf5akeru7on6o2fjinrecpdoe99eg.apps.googleusercontent.com&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob&response_type=code&scope=https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fdrive&state=state

Enter verification code: 
{% endhighlight %}

Then use the URL above in a browser to get the token for authentication. Once you have the code go back to the terminal and paste it in:

{% highlight bash %}
achiru@ubuntu:~$ gdrive about
Authentication needed
Go to the following url in your browser:
https://accounts.google.com/o/oauth2/auth?access_type=offline&client_id=367116221053-7n0vf5akeru7on6o2fjinrecpdoe99eg.apps.googleusercontent.com&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob&response_type=code&scope=https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fdrive&state=state

Enter verification code: 4/FqU7FJRfqQZ0ZvKGRD44dmm5d3L2WgM8fSoR4hjonQU
User: Adi Chiru, adichiru@gmail.com
Used: 38.4 GB
Free: 69.0 GB
Total: 107.4 GB
Max upload size: 5.2 TB

{% endhighlight %}

The token was saved into ~/.gdrive/ folder in a json file. It is important to know this because:
1. If this folder does not exist, the next time gdrive is run, it will go through the above process to help you get a token.
2. Whoever has access to your token can also access your Google Drive!

So make sure the permissions and ownership are set properly; They should be correct by default, but in case they are not:
{% highlight bash %}
chown -R [you_username]: ~/.gdrive/
chmod 600 ~/.gdrive/*.json
{% endhighlight %}

Extra:
- if you need to configure access to more then one Google Drive, you can set the environment variable GDRIVE_CONFIG_DIR to point to anoter config directory - if this new directory does not exist, gdrive will prompt again for authentication/configuration
- the communication to your Google Drive is over https - just in case you were wandering if anyone can easily see your traffic


