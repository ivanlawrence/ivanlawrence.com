---
title: Guide to Configuring Multiple Cloudflared Tunnels
description: How to configure multiple tunnels for load balancing
date: 2025-10-11T06:40:09.224Z
preview: ""
draft: true
tags:
    - technology
    - cloudflare
categories:
    - HowTo
authors: Ivan Lawrence
externalLink: ""
series: {}
author: ""
---
Because I'm never gonna remember this... and maybe it could help others since I couldn't seem to find it anywhere...

I have four data centers each with a server running an instance of `cloudflared` so we don't have to deal with port forwarding.

Each site's services are basically identical:
- reverse proxy (nginx)
- database
- application servers

Lets say the domain is `example.com`, I chose `srv1.example.com`, `srv2.example.com`, `srv3.example.com`, `srv4.example.com` to point back to the individual servers. So I used the [docs](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/get-started/create-remote-tunnel/) to create the tunnels.  All healthy!

The load balancer is named `lb-us.example.com` and here is where things started to diverge from the [documentation](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/get-started/create-remote-tunnel/).

I opted for using nginx internally to direct things between application servers and databases so I could maybe choose something slightly different depending on the site and I just put it all in ansible/opentofu git repo to keep it from getting messy.

Lets look at just two servers `srv1` and `srv2` when adding them to the load balancer. When you create the tunnels in the UI it creates the DNS record for you, cool. When you create the Load Balancer it also creates a DNS record for you, awesome.  Those names can't overlap (or be the same)... okay, why is that a big deal?

Since `cloudflared` tunnels are basically a cloudflare based reverse proxy you configure Published Applications (similar to the `server` block in nginx) which as we said will create a unique DNS record.  So tunnel `srv1` has a published application/dns record of `srv1.example.com` and tunnel `srv2` has a published application/dns record of `srv2.example.com`.

In the endpoint config on the load balancer you need to configure the http `Host` header to match the published application dns record for the tunnel (the lb's endpoint) or the request is dropped on the floor. Basically, when the request for `lb-us.example.com` gets passed along to the load balancer's endpoint the hostname needs to match the tunnel's published application dns name. That will get it to your internal reverse proxy just fine.

In my nginx config I have a server block with `server_name` for the load balancer which will be all the load balanced service locations.  But what if I want something only on `srv1`?

So instead of just
```
lb-us.example.com --Host: srv1.example.com--> srv1 tunnel --Host: srv1.example.com--> internal nginx server block for srv1.example.com
```
... which works but limits me to a single nginx config server block I wanted more like this
```
lb-us.example.com --Host: srv1-lb.example.com--> srv1 tunnel --Host: lb-us.example.com--> internal nginx server block for lb-us.example.com
```

It turns out to be rather simple enough, just make a 2nd Published Application per tunnel `srv1-lb.example.com` which is configured with the HTTP setting to add the Host header of `lb-us.example.com`.  Basically we are "toggling" the host header back to the client's original request.

From the client down the config is something like:
- DNS `lb-us.example.com` is load balanced across the tunnels passing the host header which needs to match a specific published application name (ex: `srv1-lb.example.com`) or it will die here.
- cloudflared tunnel passes the request to the local nginx server but with the manually configured host header `lb-us.example.com` so nginx can catch it for that specific server block.
- local nginx does the rest

But why though?

NGINX won't use your config if any of the upstreams (the service it will be passing the request to) are down.  Well `srv1` has some upstreams that are unique to it and not present in `srv2`. I don't want to deploy bogus services to `srv2` or configure nginx to ignore it's safety catch.

I hope that helps.