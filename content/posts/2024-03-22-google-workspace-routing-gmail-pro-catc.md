+++
date = "Fri, 22 Mar 2024 20:17:00 +0000"
title = "Google Workspace Routing gmail pro catch-all routing"
slug = "google-workspace-gmail-catch-all-routing"
authors = [ "Ivan Lawrence" ]
tags = [ "nerdler" ]
categories = []
series = [ ]
+++

# Google Workspace Routing gmail pro catch-all routing

 I like to do this dumb thing where I use unique email addresses per service.  It started to thwart organizations from selling my email address, but now everything has been breached it has a unique benefit that I don't have the same username at every site so searching through my password manager is fast and also having someone try to login as \`microsoft@example.com\` but on google has nothing to do with my real account.

Well, I've moved my DNS registrar and name servers around a bit now that Google Domains has been sold.  Up shot is [CloudFlare Domain Name Registration](https://www.cloudflare.com/products/registrar/) is a little cheaper and their API is very easy to deal with if you wanna do a roll your own Dynamic DNS service or something.

When I moved DNS some of my emails started to bounce :(

Looks like there was a gmail default route for "Google Domains Email Forwarding" but I couldn't find anything about it in the docs [Set up Default routing for your organization](https://support.google.com/a/answer/2368153?hl=en&ref_topic=2921034&fl=1&sjid=4700341423557710608-NC) which I think I was using before but now was no longer able to route since I wasn't using Google Domains?

The changes I made were for All Recipients > Envelope recipient > Change envelope recipient > Recipient username > my\_username

When tested this seemed to give the result I wanted where the previously bounced emails like \`foo@example.com\` now was getting sent to \`my\_username@example.com\`.

I thought I would share it here in case I forget and need to do it again.  I have other default routes for other domains and one of them adds a recipient instead of changing the recipient and that kinda dirties the email headers (more than they are now) but it's also a way.