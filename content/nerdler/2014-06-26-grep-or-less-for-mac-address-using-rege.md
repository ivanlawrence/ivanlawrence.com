---
id: tag:blogger.com,1999:blog-6316394477712361438.post-3699579650427620495
link: https://nerdler.ivanlawrence.com/2014/06/grep-or-less-for-mac-address-using-regex.html
title: grep or less for MAC address using regex
date: Thu, 26 Jun 2014 19:54:00 +0000
author: Unknown Author
keywords: 
image: 
images: 
description: >
  Easy enough: grep -i '\([0-9A-F]\{2\}[:-]\)\{5\}\([0-9A-F]\{2\}\)' file.txt Or less less file /([0-9A-F]{2}[:-]){5}([0-9A-F]{2})
---
# grep or less for MAC address using regex
##### By Unknown Author
_Published on Thu, 26 Jun 2014 19:54:00 +0000_

Easy enough:  
  

grep -i '\\(\[0-9A-F\]\\{2\\}\[:-\]\\)\\{5\\}\\(\[0-9A-F\]\\{2\\}\\)' file.txt

  

Or less

less file

/(\[0-9A-F\]{2}\[:-\]){5}(\[0-9A-F\]{2})

---
Categories: 