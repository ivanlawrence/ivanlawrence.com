+++ 
date = 2025-08-22T00:17:02-07:00
title = "Check for MX records"
description = "Hoe to check a list of thousands of domains for MX records"
slug = "checkmx"
authors = ["Ivan Lawrence"]
tags = ["bash","code", "technology"]
categories = []
externalLink = ""
series = []
+++

# Are these emails valid?
I have a list of about 100K emails.  Before I just start sending them off I wanted to check if they are valid.  I decided to check their domain names to see if they have MX records at all.

Here is the usage example:
```bash
# Usage:
#   ./checkmx.sh [-p N] [@dnsserver] [file]
#
# Examples:
#   ./checkmx.sh domains.txt
#   ./checkmx.sh -p 10 @8.8.8.8 domains.txt
#   cat domains.txt | ./checkmx.sh @1.1.1.1
#
# Output:
#   stdout = domains that HAVE MX records
#   stderr = domains that LACK MX records
```

Now I was an idiot and forgot to point it at `8.8.8.8` or `1.1.1.1` so my name server was the first hop... but it riped through about 20k domains in a few mins.

I ran it redirecting the different outputs like this:
```bash
$ ./checkmx.sh domains.txt > domains_yes_mx.txt 2> domains_no_mx.txt
```

Instead of:
```bash
$ ./checkmx.sh @8.8.8.8 domains.txt > domains_yes_mx.txt 2> domains_no_mx.txt
```

In case it helps someone else... enjoy.


```bash
{{< include_script "static/code/checkmx.sh" >}}
```