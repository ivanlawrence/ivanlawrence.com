+++
date = "Wed, 04 Nov 2015 23:55:00 +0000"
title = "VMWare ESXi vCenter Error The data service timed out because a back-end task took more than 120"
slug = "vmware-esxi-vcenter-data-service-timeout"
authors = [ "Ivan Lawrence" ]
tags = [ "nerdler" ]
categories = []
series = [ ]
+++

# VMWare ESXi vCenter Error The data service timed out because a back-end task took more than 120

The data service timed out because a back-end task took more than 120
---------------------------------------------------------------------

While working with VMware ESXi 6.0 and through our vCenter server using the webUI we began getting the aforementioned error.  The solution (after three days of back and forth with VMWare support) was to remote into the vCenter Virtual Appliance and restart the web client service:

`service vsphere-client stop`  
`service vsphere-client start`

  

Some of the back and forth was to convince support that we had not changed anything, no decommissioning of a server, no additional hosts, etc.  We left Friday and all was working, come Monday morning we could not manage any of our VMs (could not edit the configs or create new VMs)!

  

We were offered a publicly available solution ([https://noteits.net/2015/08/17/the-data-service-timed-out-because-a-back-end-task-took-more-than-120/#more-414](https://noteits.net/2015/08/17/the-data-service-timed-out-because-a-back-end-task-took-more-than-120/#more-414)) that did not match our scenario since we had not made any changes to our vCenter environment.