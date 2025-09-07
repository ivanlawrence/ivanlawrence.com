+++
date = "Thu, 09 Jan 2014 22:47:00 +0000"
title = "What package contains this file or library Use yum provides to find out"
slug = "[SLUG]"
authors = [ "Ivan Lawrence" ]
tags = [ "nerdler" ]
categories = []
series = [ ]
+++

# What package contains this file or library Use yum provides to find out

If you are trying to install an application which gives an error about a missing file you might be able to use `yum` to easily determine the package and install it.  
Error ex:  
`error while loading shared libraries: libssl.so.6: cannot open shared object file: No such file or directory`  
  
One way to resolve this is by using the `provides` option in `yum`.  
  
In a terminal, as root (or sudo), type `yum provides libssl.so.6` then copy and paste the resulting package name into `yum install _____`.