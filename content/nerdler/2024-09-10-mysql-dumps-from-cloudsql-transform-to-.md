+++
date = "Tue, 10 Sep 2024 20:34:00 +0000"
title = "MySQL dumps from cloudSQL transform to csv then load infile locally"
slug = "[SLUG]"
authors = [ "Ivan Lawrence" ]
tags = [ "nerdler" ]
categories = []
externalLink = "https://nerdler.ivanlawrence.com/2024/09/mysql-dumps-from-cloudsql-transform-to.html"
series = [ ]
+++

# MySQL dumps from cloudSQL transform to csv then load infile locally

I got some databases I need to migrate from one server to another.  We opted for the good old fashion \`mysqldump\`

So far it is wicked slow to import one of the tables, while working through the solution I needed to transform the data from the dump format of insert statements per line to a csv file.  Yeah, I could have just done the export again into this new format but why bother with all that and the network egress fees again, lets just transform what we have.

I asked ChatGPT to come up with some script to do this for me... that script failed :(

I ended up basically just settling on the good old fashion grep & sed commands:

time grep "INSERT INTO" /path/to/file\_src.sql | sed -e 's/^\[^(\]\*\[(\]//' -e 's/),(/\\n/g' -e 's/);$//' > /path/to/file\_dest.csv

so with this one command you consider only the lines with INSERT commands and you remove the front part something like: "INSERT INTO \`table\` ("

You also "split" and make new lines on "),(" which is the "boundary" between all the rows

Then we trim the end ");"  

So in this one-liner we do basically all we need... if you have hundreds of gigs of tables like me it's faster to run this than re-export it all over the net.

We then needed to import it so I manually took the create table part of the dump, added it into another file and wrapped it in a loop.  I experimented with additional MySQL tuning bits and bobs over the iterations and have no idea if it is helping or not.

Find more and the scripts in this gist [https://gist.github.com/ivanlawrence/c3fdbdcab0a34df714f0361f5f55e721](https://gist.github.com/ivanlawrence/c3fdbdcab0a34df714f0361f5f55e721)