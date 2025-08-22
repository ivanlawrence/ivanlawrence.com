---
id: tag:blogger.com,1999:blog-6316394477712361438.post-7384123259876502554
link: https://nerdler.ivanlawrence.com/2013/11/unix-find-command-to-open-permissions.html
title: UNIX find command to open permissions
date: Wed, 13 Nov 2013 19:31:00 +0000
author: Unknown Author
keywords: 
image: 
images: 
description: >
  Thanks to: http://www.giannistsakiris.com/index.php/2008/12/20/unix-how-to-find-files-that-do-not-match-a-pattern-or-other-criteria/ and: http://stackoverflow.com/questions/5119946/find-exec-with-multiple-commands Using find we can query a storage volume and open permissions (or change permissions to other permissions) to ease permissions issues in a heterogeneous environment. The "old" way to open all the permissions in the volume /media/volB is to run a chmod recursively on all the files in the directory: chmod
---
# UNIX find command to open permissions
##### By Unknown Author
_Published on Wed, 13 Nov 2013 19:31:00 +0000_

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

---
Categories: 