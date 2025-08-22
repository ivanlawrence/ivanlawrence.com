+++
date = "Thu, 31 Aug 2017 17:24:00 +0000"
title = "Dell iDRAC Reboot Guest OS and Restart DRAC commands"
description = "Send the following commands via SSH: ssh &lt;drac hostname or IP&gt; 'racadm getsysinfo' You can even redirect it to a file on your local system: ssh &lt;drac hostname or IP&gt; 'racadm getsysinfo' | tee -a /path/to/file.txt Reboot Guest OS (the OS installed on the hardware) (from: https://www.devops.zone/tricks/connecting-ssh-drac-reboot-server/) Connect to the&nbsp;Dell Remote Access Controller (Drac)&nbsp;IP address via"
slug = "[SLUG]"
authors = [ "Ivan Lawrence" ]
tags = [ "nerdler" ]
categories = []
externalLink = "https://nerdler.ivanlawrence.com/2017/08/dell-idrac-reboot-guest-os-and-restart.html"
series = [ ]
+++

# Dell iDRAC Reboot Guest OS and Restart DRAC commands

### Send the following commands via SSH:

ssh <drac hostname or IP> 'racadm getsysinfo'

  

You can even redirect it to a file on your local system:

ssh <drac hostname or IP> 'racadm getsysinfo' | tee -a /path/to/file.txt

### Reboot Guest OS (the OS installed on the hardware)

(from: [https://www.devops.zone/tricks/connecting-ssh-drac-reboot-server/](https://www.devops.zone/tricks/connecting-ssh-drac-reboot-server/))  

Connect to the Dell Remote Access Controller (Drac) IP address via SSH. Then execute:

racadm serveraction <action>

whereas you replace <action> with one of the following:

-   powerdown — Powers down the managed system.
-   powerup — Powers up the managed system.
-   powercycle — Issues a power-cycle operation on the managed system. This action is similar to pressing the power button on the system’s front panel to power down and then power up the system.
-   powerstatus — Displays the current power status of the server (“ON”, or “OFF”)
-   hardreset — Performs a reset (reboot) operation on the managed system.

So, to power off and back on your server, you just type racadm serveraction powercycle.

If your Drac crashes for any reason, you may want to reset it: ([more info](http://www.dell.com/support/manuals/us/en/19/integrated-dell-remote-access-cntrllr-8-with-lifecycle-controller-v2.00.00.00/racadm_idrac_pub-v1/racreset?guid=guid-7866bef3-f5c4-4c8b-b2e3-ce22d6332ddc&lang=en-us))

racadm racreset soft

If you want to have information about your current server, type:

racadm getsysinfo

(from: [https://frednotes.wordpress.com/2012/11/13/reset-dell-idrac-using-ssh/](https://frednotes.wordpress.com/2012/11/13/reset-dell-idrac-using-ssh/))  

### How to reboot an DELL idrac when web page refuse access:

Connect to idrac IP using ssh with the password refused by web page

$ ssh root@192.168.0.120
root@192.168.0.120's password:

The magic DELL tool is here : radadm

/admin1-> racadm
===============================================================================
 RACADM version 1.80 (Build 17)
 Copyright (c) 2003-2010 Dell, Inc.
 All Rights Reserved
 ===============================================================================
RACADM usage syntax:
racadm <subcommand> <options>
Examples:
racadm getsysinfo
 racadm getsysinfo -d
 racadm getniccfg
 racadm setniccfg -d
 racadm setniccfg -s 192.168.0.120 255.255.255.0 192.168.0.1
 racadm getconfig -g cfgLanNetworking
Display a list of available subcommands for the RAC:
racadm help
Display more detailed help for a specific subcommand:
racadm help <subcommand>
------------------------------------------------------------------------------

So to reboot :

/admin1-> racadm racreset soft
RAC reset operation initiated successfully. It may take up to a minute
for the RAC to come back online again.
/admin1-> Connection to 192.168.0.120 closed by remote host.
Connection to 192.168.0.120 closed.