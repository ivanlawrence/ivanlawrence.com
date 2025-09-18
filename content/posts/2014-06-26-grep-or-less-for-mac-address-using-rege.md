+++
date = "Thu, 26 Jun 2014 19:54:00 +0000"
title = "grep or less for MAC address using regex"
slug = "grep-or-less-for-mac-address-regex"
authors = [ "Ivan Lawrence" ]
tags = [ "nerdler" ]
categories = []
series = [ ]
+++

# grep or less for MAC address using regex

Easy enough:  
  

grep -i '\\(\[0-9A-F\]\\{2\\}\[:-\]\\)\\{5\\}\\(\[0-9A-F\]\\{2\\}\\)' file.txt

  

Or less

less file

/(\[0-9A-F\]{2}\[:-\]){5}(\[0-9A-F\]{2})