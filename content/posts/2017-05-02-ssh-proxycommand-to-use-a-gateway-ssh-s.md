+++
date = "Tue, 02 May 2017 17:03:00 +0000"
title = "SSH ProxyCommand to use a gateway ssh server for subsequent SSH connections"
slug = "[SLUG]"
authors = [ "Ivan Lawrence" ]
tags = [ "nerdler" ]
categories = []
series = [ ]
+++

# SSH ProxyCommand to use a gateway ssh server for subsequent SSH connections

from: [https://heipei.github.io/2015/02/26/SSH-Agent-Forwarding-considered-harmful/](https://heipei.github.io/2015/02/26/SSH-Agent-Forwarding-considered-harmful/)  
  

ProxyCommand to the rescue
==========================

If you’re still reading this, chances are that you’re not aware of the awesome toolbox that is SSH (or at least OpenSSH, let’s be honest).

OpenSSH has an option called `ProxyCommand`. It works by specifying a ProxyCommand to connect to host B. The config would look like this for our simple example:

```apache
1 Host hosta
2  User userfoo
3  Hostname 123.123.123.123
4 
5 Host hostb
6  User userbar
7  Hostname 192.168.1.1
8  Port 2222
9  ProxyCommand ssh -q -W %h:%p hosta
```

This config tells your local SSH client to connect to host A via a direct connection. Now, if you type `ssh hostb` what it will do is connect to host A first and then forward your SSH client via the `ProxyCommand` to the host and port specified by the `-W` parameter, in this case I used `192.168.1.1` to underline that this is an _internal_ host. Your local SSH client will then authenticate to host B as if you were connected to it directly.

Another observation here is that you don’t need to memorize anything about the host, neither it’s IP or obscure hostname, port or username when you can simply alias it with the `Host` line.