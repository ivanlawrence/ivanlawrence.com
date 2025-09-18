+++
date = 2024-10-30T07:08:20.000Z
title = "LXD Host DAC"
description = "LXD/LXC networking a static crossover directly connected NIC"
slug = "lxd-host-dac"
authors = [ "Ivan Lawrence" ]
tags = [ "bash", "code", "LXD", "technology", "LXC" ]
categories = [ "HowTo" ]
externalLink = ""
series = [ ]
+++
# How To Interlink (Direct Attach Cable) two LXD hosts 
For the life of me I couldn't find this out there on the net. I looked at countless videos and read blogs but to no avail.

The task seemed easy, I have two physical hosts with dual-port 25GbE NICs. I only have a 10GbE switch but want to take advantage of the faster link speed so why not use a DAC and just static IP the interfaces?!

Here is the setup:
(srv1 & srv2 hosts are set up identically)
- `eth0_10gbe` (NIC) --> `br0_10gbe` (host bridge) `10.10.0.0/24`
- `eth1_25gbe` (NIC) --> `# br1_25gbe` (host bridge) `10.25.0.0/24`

I then setup LXD to use the 10GbE bridge for networking using the unmanaged (by lxd) host bridge by using the lxd bridge nic. This allows the DHCP request to be passed to my LAN DHCP server (Ubiquity UDMPro) for that particular segment (vlan tagged network). This was relatively easy and there are a bunch of tutorials and things since this is rather standard.

In Ubuntu I used netplan to add the VLANs to the host bridge and I use the subsequent "sub interface" in the container. Said differently, UDMPro sends untagged traffic to the port, the NIC puts that in the host bridge which then breaks out the VLANs, then LXC containers are attached to the VLAN interfaces.

The NIC direct connected between hosts was super simple to configure for the host. I was scping and stuff at 25GbE in mins. Then trying to get LXC containers to do the same thing proved a little more challenging.

I eventually settled on creating a bridge (as stated above) and adding that to the container as eth1 using LXC's bridging nictype:

`lxc config device add <container_name> eth1 nic nictype=bridged parent=br1_25gbe`

I then need to go into the container OS and manually configure an IP.

One of the LXD folks wrote a blog about some of the other kind of networking and includes code for doing manual static IP addressing from an LXC profile which is likely what I'll do next... all of my LXC containers are provisioned via terraform and then configured via ansible so maybe I'll go a different route (pun intended) but not sure yet.

But why though? This is all to allow for some of the containerized services to use the 25GbE network as a backhaul connection for their cluster traffic.
