---
id: tag:blogger.com,1999:blog-6316394477712361438.post-1715191341741449369
link: https://nerdler.ivanlawrence.com/2014/01/what-package-contains-this-file-or.html
title: What package contains this file or library Use yum provides to find out
date: Thu, 09 Jan 2014 22:47:00 +0000
author: Unknown Author
keywords: 
image: 
images: 
description: >
  If you are trying to install an application which gives an error about a missing file you might be able to use yum to easily determine the package and install it. Error ex: error while loading shared libraries: libssl.so.6: cannot open shared object file: No such file or directory One way to resolve
---
# What package contains this file or library Use yum provides to find out
##### By Unknown Author
_Published on Thu, 09 Jan 2014 22:47:00 +0000_

If you are trying to install an application which gives an error about a missing file you might be able to use `yum` to easily determine the package and install it.  
Error ex:  
`error while loading shared libraries: libssl.so.6: cannot open shared object file: No such file or directory`  
  
One way to resolve this is by using the `provides` option in `yum`.  
  
In a terminal, as root (or sudo), type `yum provides libssl.so.6` then copy and paste the resulting package name into `yum install _____`.

---
Categories: 