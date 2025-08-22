---
id: tag:blogger.com,1999:blog-6316394477712361438.post-766841285793152476
link: https://nerdler.ivanlawrence.com/2013/08/read-compressed-files-without-mess.html
title: Read a Compressed files without the mess
date: Fri, 16 Aug 2013 23:18:00 +0000
author: Unknown Author
keywords: 
image: 
images: 
description: >
  From Commands I Always Forget... &gt;Read a text file in a zip inplace use less to read a text file in a zip: unzip -p [archive.zip] [inner/zip/path/to/file.txt] | less unzip -c [archive.zip] [inner/zip/path/to/file.txt] | less In a tar.gz tar --to-stdout -zxf file.tar.gz | less or just a file that is gzipped gzip -c file.gz | less
---
# Read a Compressed files without the mess
##### By Unknown Author
_Published on Fri, 16 Aug 2013 23:18:00 +0000_

From [Commands I Always Forget...](http://nerdler.blogspot.com/2013/07/commands-i-always-forget.html)

\>Read a text file in a zip inplace
===================================

  
use less to read a text file in a zip:  
`unzip -p [archive.zip] [inner/zip/path/to/file.txt] | less`  
`unzip -c [archive.zip] [inner/zip/path/to/file.txt] | less`  
In a tar.gz  
`tar --to-stdout -zxf file.tar.gz | less`  
or just a file that is gzipped  
`gzip -c file.gz | less`

---
Categories: 