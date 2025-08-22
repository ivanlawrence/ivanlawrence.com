+++
date = "Fri, 16 Aug 2013 23:14:00 +0000"
title = "SSH tricks"
slug = "[SLUG]"
authors = [ "Ivan Lawrence" ]
tags = [ "nerdler" ]
categories = []
externalLink = "https://nerdler.ivanlawrence.com/2013/08/ssh-tricks.html"
series = [ ]
+++

# SSH tricks

From [Commands I Always Forget...](http://nerdler.blogspot.com/2013/07/commands-i-always-forget.html)

\>SSH tricks
============

[ssh](http://en.wikipedia.org/wiki/Secure_Shell) is one of the best nerd tools around!  I use it for connecting to my home when on the road and as a poor mans vpn to my home for web browsing:  
  
`ssh -DN 8080 user@host -p xxxxx`  
The -D dynamically links the local port 8080 (this is on my laptop) through ssh to the host (my home server).  The I configure my browser to use a SOCKS proxy on 8080 and BLAM, I'm on my local network surfing the internet or any other web based service I have at my home (XBMC, DD-WRT, development web pages, etc)  
The -N means don't bother giving me an interactive shell, meaning, the terminal prompt on my laptop can't use the ssh connection through the terminal (ie, although I have an ssh connection open I can't do normal ssh stuff through this window) which is nice because it keeps the tunnel from closing because of a time-out.  
  
This also works for connecting any to any TCP port within the network of the host (home) so if I want to use VNC I can  
`ssh user@host -NL 5900:additional_host:5900`  
The -L is for linking a single port, this passes traffic from my laptop port 5900 through "host" into "additional\_host" port 5900 allowing my laptop to VNC to localhost 5900 (screen 0) and see the remote system behind the network accessible via "host".  
  
Another common use is to remotely connect and use screen for continued administration/development  
  
`ssh -t user@host -p xxxxx screen -dR Ivan`  
This tells ssh to make a connection to the host system (home) as "user" (me) on port xxxxxx and launch screen connecting to or creating a session called Ivan.  The -t forces the creation of a "terminal" which means, if you do not have -t having ssh launch some applications fail.  Lots of awesome tricks with ssh, I'll put more in the future.  
If you are in need of sending [command substitution](http://tldp.org/LDP/abs/html/commandsub.html) variables to get the remote system to add the current time to the name of a file that you are creating so need to put an escape character  before the $ or you will get the command substitution of the local system.  \[ex: ssh root@ "zip -r /path/to/dir/\\$(date +%F-%H\_%M)-\\$(hostname | grep -i something | awk '{print \\$2;}')-logs.zip /path/to/things/to/zip"\] \[hint: notice the \\ in front of all the s\]