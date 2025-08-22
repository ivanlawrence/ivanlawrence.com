---
id: tag:blogger.com,1999:blog-6316394477712361438.post-2557945876490099195
link: https://nerdler.ivanlawrence.com/2013/09/vim-substitution-to-prepare-xml-code.html
title: VIM substitution to prepare XML code samples for HTML blogs
date: Tue, 10 Sep 2013 21:06:00 +0000
author: Unknown Author
keywords: 
image: 
images: 
description: >
  I sometimes need to include XML code samples in my blogs. &nbsp;When I do this I need a quick way to format it for HTML, more specifically, format it for whatever blogging software I'm using. &nbsp;Since it is infrequent I just use VIM substitution (I know, I could do this
---
# VIM substitution to prepare XML code samples for HTML blogs
##### By Unknown Author
_Published on Tue, 10 Sep 2013 21:06:00 +0000_

I sometimes need to include XML code samples in my blogs.  When I do this I need a quick way to format it for HTML, more specifically, format it for whatever blogging software I'm using.  Since it is infrequent I just use VIM substitution (I know, I could do this in SED but for now VIM is good enough)  
  
From [http://vim.wikia.com/wiki/Search\_and\_replace](http://vim.wikia.com/wiki/Search_and_replace):  

> `:%s/foo/bar/g`  
> Find each occurrence of 'foo' (in all lines), and replace it with 'bar'.

  
I search for `<` or `>` then replace them with `&lt;` or `&gt;` and done.  
  
`:%s/</\&lt\;/g` to replace all instances of `<` with `&lt;`  
`:%s/>/\&gt\;/g` to replace all instances of `>` with `&gt;`

---
Categories: 