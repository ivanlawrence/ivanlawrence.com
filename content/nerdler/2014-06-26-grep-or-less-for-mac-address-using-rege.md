+++
date = "Thu, 26 Jun 2014 19:54:00 +0000"
title = "grep or less for MAC address using regex"
slug = "[SLUG]"
authors = [ "Ivan Lawrence" ]
tags = [ "nerdler" ]
categories = []
externalLink = "https://nerdler.ivanlawrence.com/2014/06/grep-or-less-for-mac-address-using-regex.html"
series = [ ]
+++

# grep or less for MAC address using regex

Easy enough:  
  

grep -i '\\(\[0-9A-F\]\\{2\\}\[:-\]\\)\\{5\\}\\(\[0-9A-F\]\\{2\\}\\)' file.txt

  

Or less

less file

/(\[0-9A-F\]{2}\[:-\]){5}(\[0-9A-F\]{2})