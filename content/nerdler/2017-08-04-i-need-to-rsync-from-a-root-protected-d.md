---
id: tag:blogger.com,1999:blog-6316394477712361438.post-3942310511386827254
link: https://nerdler.ivanlawrence.com/2017/08/i-need-to-rsync-from-root-protected.html
title: I need to rsync from a root protected directory on one server to a root protected directory on another
date: Fri, 04 Aug 2017 00:11:00 +0000
author: Unknown Author
keywords: 
image: 
images: 
description: >
  Thanks to:&nbsp;https://crashingdaily.wordpress.com/2007/06/29/rsync-and-sudo-over-ssh/ p.p1 {margin: 0.0px 0.0px 0.0px 0.0px; font: 13.0px Menlo; color: #f5f5f5; background-color: #000000} span.s1 {font-variant-ligatures: no-common-ligatures} sudo rsync -av -e "ssh" --rsync-path="sudo rsync" username@srv01.prod.example.com:/tftpboot/ /tftpboot/ So here is the thing... I SSH(ed) to the remoteA and forwarded my ssh keys (ForwardAgent yes) and ran this command: Breakdown: sudo rsync -av -e "ssh"&nbsp; ^ On the host
---
# I need to rsync from a root protected directory on one server to a root protected directory on another
##### By Unknown Author
_Published on Fri, 04 Aug 2017 00:11:00 +0000_

  
Thanks to: [https://crashingdaily.wordpress.com/2007/06/29/rsync-and-sudo-over-ssh/](https://crashingdaily.wordpress.com/2007/06/29/rsync-and-sudo-over-ssh/)  
  
p.p1 {margin: 0.0px 0.0px 0.0px 0.0px; font: 13.0px Menlo; color: #f5f5f5; background-color: #000000} span.s1 {font-variant-ligatures: no-common-ligatures}  

sudo rsync -av -e "ssh" --rsync-path="sudo rsync" username@srv01.prod.example.com:/tftpboot/ /tftpboot/

  

So here is the thing...

  

I SSH(ed) to the remoteA and forwarded my ssh keys (ForwardAgent yes) and ran this command:

  

Breakdown:

sudo rsync -av -e "ssh" 

^ On the host I'm running this on run it under sudo

\--rsync-path="sudo rsync" username@srv01.prod.example.com:/tftpboot/ /tftpboot/

^ On the remote server run rsync server as sudo

---
Categories: 