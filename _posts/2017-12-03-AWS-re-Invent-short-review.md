---
title: AWS re:Invent 2017 - short review
date: 2017-12-03 22:43:00 -07:00
categories:
- cloud
- devops
tags:
- aws
- cloud
- devops
- conference
image: 
description: How was the AWS re:Invent 2017 (Las Vegas)
serie: Basics
layout: post
---

![reinvent_2017_banner_1.png](/uploads/reinvent_2017_banner_1.png){: .center-image }

My first participation (just few days ago) to the well known AWS re:Invent exposed me to a mix of feelings, memories, disappointments, ideas and hope. I used to work for AWS as TAM in Netherlands. For a brief period of time, unfortunately...

During the event in Las Vegas I got first a little sad that I am not part of this impressive and game-changing company anymore but no later than my first day in the conference I was reminded, accidentally, how much better it is to work *with* AWS cloud technologies rather than to work *in* AWS: I was at a table having lunch and, next to me, 2 AWS employees sat down with their lunch and discussed how nothing is clear, how it is frustrating to never know what you are actually expected to do, how the company pushes a lot on the individual and not giving too much support to deal with various things. These were people that were partially in charge of this event.

Moving on, being an attendee only, I was trying to get to the most interesting sessions for me: Security related, DevOps and Infrastructure.
It was quite disappointing to see that first, of all the venues were too far apart from each other to be able to actually move between them and still be able to attend sessions. So I thought that I would follow specific "paths" (this was a very useful filter in the mobile app, by the way). That was not possible either. DevOps sessions, for example, were in both Aria and Venetian. overlapting too. You need at least 30 minutes to walk between Aria and Venetian. The shuttle service they had included was really slow: the first day took me 35 minutes from MGM to Aria (15 of which we waited in the bus, 100m from the entrance, inside the underground garage at destination - there was not enough room for the bus to get to the parking space....). Then it took other 25 minutes to get from Aria to Venetian - the second stop. Of course, I missed my reserved session.

The only time I got through the above route in 14 + 12 minutes, respectively, was during Andy Jassy's keynote - so this was not a Las Vegas traffic problem, was an AWS re:Invent induced problem. It also never got solved during the entire week!

Also, a few other things to improve upon:

Too much "this is amazing" fluff from AWS representatives during Introductory (200) and even Advanced (300) sessions. Let the facts and use cases presented by others (Expedia, Netflix etc.) speak for themselves - much more powerful!
Everybody should have the same chance of booking the sessions they want. After only minutes from online access, some of the sessions were already full...
Waiting in line for walk-up sessions is complete chaos - AWS needs desperately to find a solution for this.
Make the "paths" (DevOps, Infrastructure, ML, etc.) easy to follow - no overlapping sessions that are not on the same topic, and definitely not in different venues!
Now on the good stuff:

The event, in general, was impressive, huge, massive, unnecessarily imposing... no sign of the frugality LP you are many times hit with as an employee.
All of the sessions that I went to because I could not get into those that I wanted (more than half actually) were actually better than those of high demand. The How I Made My Motorbike Talk for example, was one a lot more inspiring and interesting talks than the ones of much higher demand from AWS representatives.
It was definitely nice and reassuring to see that "Advanced DevOps" is very much in line with what I was doing and pushing for in several companies, most recently in Origin Group within Electronic Arts. I really wish some of my former colleagues in EA would watch these and understand that this is the way to go.
The Expo with all the vendors and partners in both Aria and Venetian was really good. Lots of good information, features, specific approaches to the same problems. Sometimes I got more valuable information from Expo than from some sessions.
Some generic thoughts:

serverless is (although was for quite some time now) the new thing - lots of interest in this concept and technology
DevOps is still needed, appreciated and slowly re-designed, re-invented, fine-tuned for various industries, including the slower ones (e.g. financial, government etc.)
Security is omnipresent - I guess the old phrase "there is no cloud without security" still applies even more today.
For some reason, many companies are betting their business model on exposing data from AWS accounts to the customer in a "better way", or "easier", or "more centralized". I was never really annoyed that AWS cloud tech is not giving me access to this data (logs, events etc.) in an easy to process way. Most of this is really processing CloudTrail and Billing data (a csv file that can be HUGE, in many cases). 
Overall it was a very interesting week, with ups and downs, quite demanding but also rewarding.
















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
   chmod \+x gdrive
   mv gdrive gdrive-$(gdrive version | grep gdrive | awk '{print $2}')
   sudo cp gdrive-\* /usr/local/bin/
   sudo ln -s /usr/local/bin/gdrive-\* /usr/local/bin/gdrive
   {% endhighlight %}

Note: there is a bug in the version displayed at this moment (https://github.com/prasmussen/gdrive/issues/143)
The version the binary returns to 'gdrive version' command is 2.0.1 but the binary is supposed to be for version 2.1.0.

At this moment the /usr/local/bin/ directory looks like this:
{% highlight bash %}

achiru@ubuntu:\~$ ls -l /usr/local/bin/
total 7604
lrwxrwxrwx 1 root root      27 Oct 19 09:40 gdrive -> /usr/local/bin/gdrive-2.0.1
-rwxr-xr-x 1 root root 7785024 Oct 18 11:43 gdrive-2.0.1

{% endhighlight %}

I used the version in the binary name and the symlink so that I can install a newer version later and activate it (by updating the symlink) while keeping the current version available in case I need to roll back.

Once this is done, we need to configure it:
{% highlight bash %}

achiru@ubuntu:\~$ gdrive about
Authentication needed
Go to the following url in your browser:
https://accounts.google.com/o/oauth2/auth?access_type=offline&client_id=367116221053-7n0vf5akeru7on6o2fjinrecpdoe99eg.apps.googleusercontent.com&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob&response_type=code&scope=https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fdrive&state=state

Enter verification code:
{% endhighlight %}

Then use the URL above in a browser to get the token for authentication. Once you have the code go back to the terminal and paste it in:

{% highlight bash %}
achiru@ubuntu:\~$ gdrive about
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

The token was saved into \~/.gdrive/ folder in a json file. It is important to know this because:

1. If this folder does not exist, the next time gdrive is run, it will go through the above process to help you get a token.

2. Whoever has access to your token can also access your Google Drive!

So make sure the permissions and ownership are set properly; They should be correct by default, but in case they are not:
{% highlight bash %}
chown -R \[you_username\]: \~/.gdrive/
chmod 600 \~/.gdrive/\*.json
{% endhighlight %}

Extra:

* if you need to configure access to more then one Google Drive, you can set the environment variable GDRIVE_CONFIG_DIR to point to anoter config directory - if this new directory does not exist, gdrive will prompt again for authentication/configuration

* the communication to your Google Drive is over https - just in case you were wandering if anyone can easily see your traffic
