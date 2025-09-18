+++
date = "Wed, 30 Oct 2024 02:41:00 +0000"
title = "LXD now Canonicals a journey into the simplistically complex"
slug = "lxd-now-canonicals-a-journey"
authors = [ "Ivan Lawrence" ]
tags = [ "nerdler" ]
categories = []
series = [ ]
+++

# LXD now Canonicals a journey into the simplistically complex

The history of LXD is not part of this post...

[LXC (Linux Containers)](https://en.wikipedia.org/wiki/LXC) were not the goal.  I have experience with OpenStack and wanted to go that route but the hardware requirements and overhead for my particular project were so small (just a few different services) that someone on my team said "I'm just gonna install them all on my mac and be done with it.

I couldn't let that go, I needed some CI/CD and config management, I needed repeatable code based infrastructure!

I first found Canonical's MicroCloud and was kinda sold!  But their hardware requirement was min of three like servers and we just had two like servers.  So I did flirt with setting up LXD then putting a multi node MicroCloud cluster in it on VMs but that seemed a little crazy.

So I started with one host, configured LXD and started building containers with terraform and ansible.  It worked so well I was convinced that this was the right path forward.

The hardware are some Ryzen 5 boxes with max RAM and a few SSDs and a dual port 25GbE NIC.  One port from the NICs are direct attached to each other (no switching) and the other is into a 10GbE switch since that is what we have.

I configured each LXC server as a remote of the other and added the remote using the 25GbE network so copy/move operations would be max speed.

I had some trouble getting that part working since each time I would follow the steps to add the remote using the trust cert I would get an error.  No amount of googling found the answer so I tried to post on the LXD forums at Canonical... that was a waste of time since I couldn't even post and there was a disclaimer about "not for tech support" bla bla... so Reddit for the win!  

I created a post "[LXD to LXD host on one NIC, everything else on another?](https://www.reddit.com/r/LXD/comments/1gf8etq/lxd_to_lxd_host_on_one_nic_everything_else_on/)" in the LXD subreddit and within mins I was off and going!

I can run lxc commands from my laptop connected wifi and see both remotes and issue commands, it's wicked simple once you get it all setup and memorize all the CLI commands ;) 

I opted to have the 10GbE connected to the switch getting all the untagged VLAN traffic then use a linux bridge to pass the VLANs as networks into the containers.  I kinda followed the OpenStack pattern from the old days but also found [Trevor Sullivan's YouTube Tutorials](https://www.youtube.com/playlist?list=PLDbRgZ0OOEpX_uqK_hAq98ltdc4DP-D34) super helpful as well as the OG PM of LXD Stéphane Graber's [YouTube](https://www.youtube.com/watch?v=4iNpiL-lrXU) and [Site](https://stgraber.org/) very helpful!

I might add more details in the future, but my hope is if anyone else is looking for some LXD/LXC servers that are not clustered and hit some of these road blocks maybe this will shorten their search a little.