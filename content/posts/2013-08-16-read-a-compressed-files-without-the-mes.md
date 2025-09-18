+++
date = "Fri, 16 Aug 2013 23:18:00 +0000"
title = "Read a Compressed files without the mess"
slug = "read-a-compressed-file-without-the-mess"
authors = [ "Ivan Lawrence" ]
tags = [ "nerdler" ]
categories = []
series = [ ]
+++

# Read a Compressed files without the mess

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