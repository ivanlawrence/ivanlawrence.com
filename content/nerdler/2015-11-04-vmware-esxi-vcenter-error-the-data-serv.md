---
id: tag:blogger.com,1999:blog-6316394477712361438.post-3335651115584272693
link: https://nerdler.ivanlawrence.com/2015/11/vmware-esxi-vcenter-error-data-service.html
title: VMWare ESXi vCenter Error The data service timed out because a back-end task took more than 120
date: Wed, 04 Nov 2015 23:55:00 +0000
author: Unknown Author
keywords: 
image: 
images: 
description: >
  The data service timed out because a back-end task took more than 120 While working with VMware ESXi 6.0 and through our vCenter server using the webUI we began getting the aforementioned error. &nbsp;The solution (after three days of back and forth with VMWare support) was to remote into the vCenter
---
# VMWare ESXi vCenter Error The data service timed out because a back-end task took more than 120
##### By Unknown Author
_Published on Wed, 04 Nov 2015 23:55:00 +0000_

The data service timed out because a back-end task took more than 120
---------------------------------------------------------------------

While working with VMware ESXi 6.0 and through our vCenter server using the webUI we began getting the aforementioned error.  The solution (after three days of back and forth with VMWare support) was to remote into the vCenter Virtual Appliance and restart the web client service:

`service vsphere-client stop`  
`service vsphere-client start`

  

Some of the back and forth was to convince support that we had not changed anything, no decommissioning of a server, no additional hosts, etc.  We left Friday and all was working, come Monday morning we could not manage any of our VMs (could not edit the configs or create new VMs)!

  

We were offered a publicly available solution ([https://noteits.net/2015/08/17/the-data-service-timed-out-because-a-back-end-task-took-more-than-120/#more-414](https://noteits.net/2015/08/17/the-data-service-timed-out-because-a-back-end-task-took-more-than-120/#more-414)) that did not match our scenario since we had not made any changes to our vCenter environment.

---
Categories: 