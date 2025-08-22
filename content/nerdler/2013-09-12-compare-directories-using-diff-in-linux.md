+++
date = "Thu, 12 Sep 2013 21:26:00 +0000"
title = "Compare Directories using Diff in Linux"
slug = "[SLUG]"
authors = [ "Ivan Lawrence" ]
tags = [ "nerdler" ]
categories = []
externalLink = "https://nerdler.ivanlawrence.com/2013/09/compare-directories-using-diff-in-linux.html"
series = [ ]
+++

# Compare Directories using Diff in Linux

From: [http://linuxcommando.blogspot.com/2008/05/compare-directories-using-diff-in-linux.html](http://linuxcommando.blogspot.com/2008/05/compare-directories-using-diff-in-linux.html)  
  
To compare 2 files, we use the diff command. How do we compare 2 directories? Specifically, we want to know what files/subdirectories are common, what are only in 1 directory but not the other.   
  
Unix old-timers may remember the dircmp command. Alas, that command is not available in Linux. In Linux, we use the same diff command to compare directories as well as files.  
  

$ diff  ~peter ~george
Only in /home/peter: announce.doc
diff /home/peter/.bashrc /home/george/.bashrc
76,83d72
<
< # Customization by Peter
< export LESS=-m
< export GREP\_OPTIONS='--color=always'
< shopt -s histappend
< shopt -s cmdhist
< export PROMPT\_COMMAND="history -a;$PROMPT\_COMMAND"
< #echo keycode 58 = Escape |loadkeys -
Only in /home/george: .mcoprc
Only in /home/peter: .metacity
Only in /home/george: .newsticker-images
Only in /home/peter: .notifier.conf
Only in /home/george: targets.txt
Only in /home/peter: .xsession-errors

  
Without any option, diffing 2 directories will tell you which files only exist in 1 directory and not the other, and which are common files. Files that are common in both directories (e.g., .bashrc in the above listing) are diffed to see if and how the file contents differ.  
  
 If you are NOT interested in file differences, just add the -q (or --brief) option.  
  

diff -q ~peter ~george  |sort
Files /home/peter/.bashrc and /home/george/.bashrc differ
Only in /home/george: .mcoprc
Only in /home/george: .newsticker-images
Only in /home/george: targets.txt
Only in /home/peter: .metacity
Only in /home/peter: .notifier.conf
Only in /home/peter: .xsession-errors
Only in /home/peter: announce.doc

  
diff orders its output alphabetically by file/subdirectory name. I prefer to group them by whether they are common, and whether they only exist in the first or second directory. That is why I piped the output of diff through sort in the above command. Note that by default diff does not reach into the subdirectories to compare the files and subdirectories at that level. To change its behavior to recursively go down subdirectories, add -r.  
  
  `diff -qr ~peter ~george |sort`