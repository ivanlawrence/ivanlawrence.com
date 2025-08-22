+++
date = "Fri, 13 Sep 2013 00:09:00 +0000"
title = "Search Multiple Words String Pattern Using grep Command"
slug = "[SLUG]"
authors = [ "Ivan Lawrence" ]
tags = [ "nerdler" ]
categories = []
series = [ ]
+++

# Search Multiple Words String Pattern Using grep Command

From [http://www.cyberciti.biz/faq/searching-multiple-words-string-using-grep/](http://www.cyberciti.biz/faq/searching-multiple-words-string-using-grep/)  
  
How do I search multiple strings or words using the grep command? For example I'd like to search word1, word2, word3 and so on within /path/to/file. How do I force grep to search multiple words?  
  
The [grep command supports regular expression](http://www.cyberciti.biz/faq/grep-regular-expressions/) pattern. To search multiple words, use following syntax:  
`grep 'word1\|word2\|word3' /path/to/file`  
  
In this example, search warning, error, and critical words in a text log file called /var/log/messages, enter:  
`$ grep 'warning\|error\|critical' /var/log/messages`  
  
To just match words, add -w swith:  
`$ grep -w 'warning\|error\|critical' /var/log/messages`