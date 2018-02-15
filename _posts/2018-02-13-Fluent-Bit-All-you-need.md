---
title: Fluent Bit - All you need to use it
date: 2017-02-14 22:43:00 -08:00
categories:
- cloud
- devops
tags:
- devops
- logs
- log management
- fluentd
- fluentbit
- elasticsearch
- cloud
image:
description: How to build, configure and deploy Fluent Bit to over 1000 servers
serie: DevOps
layout: post
---


### Notable details
  * Fluent Bit official website: http://fluentbit.io
  * Fluent Bit official documentation: http://fluentbit.io/documentation/0.12/
  * This is a faster, lighter, simpler version of fluentd.
  * It supports systend natively, Docker, Kubernetes

### Why did I try it

This is the list of requirements I created fir myself and how fluentbit maps to them:

1. Native support for systemd.
Systemd built-in plugin. If compiled with systemd support, it can natively extract events from journald.

2. Real-time log file monitoring (tail -f like).
Tail built-in plugin. Not really real-time but very close to it; actually, better than real-time.

3. Automatic discovery of new log files.
Tail plugin. It can automatically add new files detected in a directory based on regex; configurable refresh interval for the list of monitored files.

4. Seamless handle of rotating log files.
It can handle having the monitored files rotated but it will not rotate log files itself.

5. Work on all still supported Ubuntu LTS versions?
It seems that the expectation from the developer is for the user to compile Fluent Bit for the systems it needs to work on. The dependencies are minimal and the build steps are simple. For systems that do not support systemd an init script need to be created. Packages seem to be provided for convenience only and only for a few distros.

6. Seamless handle of network issues.
It can automatically resume sending data to backend after a network interruption.
During the communication issue, it uses, by default, a configurable in-memory buffer to store incoming data temporarily.
The engine is however relying on the plugins for lots of critical decisions on buffering, queueing etc.

7. ???
Yes, via a feedback mechanism implemented in the engine and the backpressure feature (Mem_Buf_Limit) that controls the input plugins behaviour.

8. Simple configuration.
It is simple but in a "proprietary" format, relatively sensitive to indentation and a few other quirks...

9. Modular configuration.
It supports the @INCLUDE directive to include multiple separate configuration files. This simplify the setup of the configuration management system (e.g. chef) when it deploys this to different nodes with different roles/applications.

10. Run as non-root.
Yes, with quite a few changes and possibly disadvantages:
A dedicated user needs to be created
The dedicated user needs to have at least read access to all monitored log files
The dedicated user needs to be in the systemd-journal group for systemd logs
The systemd init script needs modification to start the process as non-root
The location of the DB file must be writable by the dedicated user
All the above can be setup via a pre/post install script in the custom .deb package if necessary
All new logfiles to be monitored needs to be accessible by the dedicated user
The security gain may not be significant since AWS ES service can now be deployed in a VPC so the traffic can already be easily protected, eliminating a big portion of the security risk

11. Encrypt the communication with the backend (Elasticsearch)?
The es output plugin supports TLS.

12. Enrich events with custom data.
Yes, using the record_modifier built-in plugin. However, this plugin can only add data to the event, not modify it! To modify the format of events (not the content) the parser plugin could be used.

13. Can it regex files?
For filenames, see the tail plugin.
For content of the log files, there is a built-in filter plugin named grep that can be used to match and include/exclude records.

14. Active development and stable releases.
With the correct configuration, it seems to give no errors; its own log file can be pretty descriptive with what is happening in the debug mode.
Version 0.13 is close to be marked stable. Recent merges to master.


### Building the binaries


### An example of building a .deb package for Fluent Bit


### Configuration of the Fluent Bit daemon


### Configuration for Systemd - native support


### Configuration for log files

### Best practices

After weeks of testing assumptions and configurations, I came to the conclusion that this is a nice little log forwarder that can do a lot but the documentation needs serious improvements. I am actually planning to be actively involved in updating and improving the documentation of fluent bit soon.

Here are the best practices you would be aware of when deploying Fluent Bit (at least for v0.12):

  * **Always set Daemon Off**; for some reason it will not start due to some weird errors if it is On. The real question is when does this parameter applies? It is for when the process is started manually, from CLI and not via systemd?

  * **Avoid using any non-built in plugins**; things like dealing with buffers and queueing are expected to be implemented properly in the input plugins for example and they are critical for a stable event forwarder. The **tail** and **systemd** plugins are built-in and deal with all these aspects properly.

  * **Always add the Mem_Buf_Limit parameter** to the input plugins (not sure if this is supported by the systemd plugin though). This helps control the input plugins buffer limits in memory and deal with backpressure when the incoming data is more than the backend can ingest at that moment.

  * **Always use the DB parameter**, at least to the **tail** and **systemd** plugins. This is crucial to avoid duplicate entries, losing events or creating traffic spikes at restart.

  * **Use the tagging capability!** In my setup all input plugins are tagging the events with the *chef environment* (chef is able to insert that into the configuration of the plugins at deployment time); based on this **Tag** different environments are using different index prefixes. This allows me to automate easily different life-cycles for different sets of events. For example, devs use to enable some debug or trace level logs for a short period of time (a day) to troubleshoot a situation. I can easily delete the indexes prefixed with "logs-dev-" every week but I will close and archive the indexes prefixed with "logs-prod-" every 90 days. Keeps everything clean, simple, efficient and optimal from a cost perspective. I can of course search through all the indexes by using a "logs-\*" prefix.

  * **Avoid parsers as much as possible**; Actually avoid them completely. Ideally, Fluent Bit is receiving json formatted events and it will not require any processing of the content (only collect, enrich, route, insert in ES). This is not just for performance on leaf level but also for proper mapping, search performance, optimal storage usage  and so on.

### Extras

#### How to make it run as non-root.
First, not a great idea. I never want to run anything as root for obvious reasons but in this case, for my setup at least, in AWS VPC, only private IPs and TLS, the effort and mess you have to make to run it as root is not worth it.
Second - the user Fluent Bit will run as will have to have proper access to all future directories and log files that need to be monitored.

  * create a dedicated user:

  * give read permissions to this user for all monitored log files/directories

  * add systemd-journal group as secondary group membership to the user so that it can read journald events

  * modify the systemd script to start the process as the dedicated user

  * make sure that the location for the DB file is writable by the dedicated user

This can be done in multiple ways, like using the configuration management system or even add all these steps to pre/post install script in the custom .deb package you may build for your Fluent Bit binary.


#### Why not filebeat?
1. No native systemd support; reading files that systemd put on the filesystem is not ideal in case at all.
2. 

**While there are many ways to do this, I hope you get some ideas from my experience or, at least, maybe I saved you some time...**
