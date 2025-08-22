---
id: tag:blogger.com,1999:blog-6316394477712361438.post-5659194079624921602
link: https://nerdler.ivanlawrence.com/2013/08/multiple-output-files-with-dd-utility.html
title: Multiple output files with dd utility example
date: Fri, 30 Aug 2013 18:55:00 +0000
author: Unknown Author
keywords: 
image: 
images: 
description: >
  I just used this method to create a file MD5 "on the fly" meaning, one read operation when moving the file to a destination. dd if=test.txt | tee &gt;(md5 &gt;&gt; tmp/test.txt.md5) | dd of=tmp/test.txt or you can reverse the order of the destination and MD5 dd if=test.txt | tee &gt;(dd of=tmp/test.txt) | md5
---
# Multiple output files with dd utility example
##### By Unknown Author
_Published on Fri, 30 Aug 2013 18:55:00 +0000_

I just used this method to create a file MD5 "on the fly" meaning, one read operation when moving the file to a destination.  
  
`dd if=test.txt | tee >(md5 >> tmp/test.txt.md5) | dd of=tmp/test.txt` or you can reverse the order of the destination and MD5  
`dd if=test.txt | tee >(dd of=tmp/test.txt) | md5 >> tmp/test.txt.md5`  
  
This worked for me.  The usage case would be when copying a file from "local" media to "remote" media (ex: local HDD to SAN volume)

---
Categories: 