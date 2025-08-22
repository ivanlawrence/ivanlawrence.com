+++
date = "Wed, 13 Nov 2013 19:31:00 +0000"
title = "UNIX find command to open permissions"
slug = "[SLUG]"
authors = [ "Ivan Lawrence" ]
tags = [ "nerdler" ]
categories = []
externalLink = "https://nerdler.ivanlawrence.com/2013/11/unix-find-command-to-open-permissions.html"
series = [ ]
+++

# UNIX find command to open permissions

Thanks to:  
[http://www.giannistsakiris.com/index.php/2008/12/20/unix-how-to-find-files-that-do-not-match-a-pattern-or-other-criteria/](http://www.giannistsakiris.com/index.php/2008/12/20/unix-how-to-find-files-that-do-not-match-a-pattern-or-other-criteria/)  
  
and:  
[http://stackoverflow.com/questions/5119946/find-exec-with-multiple-commands](http://stackoverflow.com/questions/5119946/find-exec-with-multiple-commands)  
  
Using find we can query a storage volume and open permissions (or change permissions to other permissions) to ease permissions issues in a heterogeneous environment.  
  
The "old" way to open all the permissions in the volume `/media/volB` is to run a chmod recursively on all the files in the directory:  
`chmod -R 777 /media/volB`  
  
This will touch each file and update its "Change" time.  That can cause issues if you are relying on that  for knowing when to archive files.  
  
In steps using `find` to find everything that is not 777 permissions:  
`find /media/volB ! -perm 777`  
(The `!` means "not")  
  
adding the part that will change the permissions on the resulting files found:  
`find /media/volB ! -perm 777 -exec chmod 777 {} \;`  
  
done