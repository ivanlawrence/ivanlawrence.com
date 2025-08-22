+++
date = "Fri, 16 Aug 2013 23:16:00 +0000"
title = "GNU Screen Info"
slug = "[SLUG]"
authors = [ "Ivan Lawrence" ]
tags = [ "nerdler" ]
categories = []
externalLink = "https://nerdler.ivanlawrence.com/2013/08/gnu-screen-info.html"
series = [ ]
+++

# GNU Screen Info

From [Commands I Always Forget...](http://nerdler.blogspot.com/2013/07/commands-i-always-forget.html) See also [Linux - GNU Screen Instructions](http://nerdler.blogspot.com/2013/07/linux-gnu-screen-instructions.html)

\>GNU Screen
============

[GNU screen](http://en.wikipedia.org/wiki/GNU_Screen) gives a server admin the ability to connect to a multi-user terminal/shell or just a persistent shell session that can be disconnected from and re connected to later without interrupting the commands being run.  Lets say I am going to parse a bunch of files looking for duplicates and I'm working on bonding a few NICs together for additional functionality, and I have a little loop running to monitor uptime on a switch, etc, etc, all within a network I access through a ssh server.  I connect and launch screen session and use a bunch of screen windows (or multiple session) to manage all those tasks... then, when I'm heading home and want to pick it all up once I'm there, I disconnect before I leave and reconnect to that same screen session(s) later as if I never left!  
  
Of course screen is all configurable with options for making life a little easier:  
  

#CODE: .screenrc  

   #All single lines
   #shutoff the start up message
startup\_message off
   #allow a big scroll back buffer so you don't "loose" anything
defscrollback 5000
   #allows screen redraw for some apps like VIM or LESS
altscreen on
   #give me my normal prompt
shell -/bin/bash
   #give me some "tabs" at the bottom of the screen to help me visualize where I am
caption splitonly "%{= wK}%-w%?%F%{= bw}%:%{= Wk}%? %n %t %{-}%+w %-= "
   #same but not just when split
hardstatus alwayslastline "%{= B}%H (ctl-a) %{= r}\[ %{= d}%Y-%m-%d %c:%s %{= r}\]%{= d} - %{= wk}%-Lw%{= Bw} %n$f\*  %t %{-}%+Lw %-= :)"
   #pre-launch screen windows with different options#
   #ssh to my home and launch screen there
screen -t ssh-home 0 ssh -t -D 8080 user@host -p xxxxxx screen -dR Ivan
   #ssh to a VM on my laptop
screen -t ssh-VM 1 ssh -t user@192.168.10.101 screen -dR Ivan
   #ssh to a work system in the lab as a comon host but use my .screenrc config
screen -t ssh-lab 2 ssh -t user@host screen -dR Ivan -c .screenrc.Ivan
   #ssh to a freelance location to work on their systems
screen -t freelance 4 ssh -t user@host screen -dR Service
   #give me a normal shell prompt
screen -t bash 5

  
So I can just launch screen and let it connect to all the places I "usually" go via ssh.  It could also be running scripts or other apps so learn about the .screenrc file.