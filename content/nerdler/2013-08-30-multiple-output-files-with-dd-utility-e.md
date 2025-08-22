+++
date = "Fri, 30 Aug 2013 18:55:00 +0000"
title = "Multiple output files with dd utility example"
description = "I just used this method to create a file MD5 "on the fly" meaning, one read operation when moving the file to a destination. dd if=test.txt | tee &gt;(md5 &gt;&gt; tmp/test.txt.md5) | dd of=tmp/test.txt or you can reverse the order of the destination and MD5 dd if=test.txt | tee &gt;(dd of=tmp/test.txt) | md5"
slug = "[SLUG]"
authors = [ "Ivan Lawrence" ]
tags = [ "nerdler" ]
categories = []
externalLink = "https://nerdler.ivanlawrence.com/2013/08/multiple-output-files-with-dd-utility.html"
series = [ ]
+++

# Multiple output files with dd utility example

I just used this method to create a file MD5 "on the fly" meaning, one read operation when moving the file to a destination.  
  
`dd if=test.txt | tee >(md5 >> tmp/test.txt.md5) | dd of=tmp/test.txt` or you can reverse the order of the destination and MD5  
`dd if=test.txt | tee >(dd of=tmp/test.txt) | md5 >> tmp/test.txt.md5`  
  
This worked for me.  The usage case would be when copying a file from "local" media to "remote" media (ex: local HDD to SAN volume)