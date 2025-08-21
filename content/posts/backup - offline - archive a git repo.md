+++ 
date = 2025-08-20T00:08:20-07:00
title = "How to backup, offline, or archive a git repo"
description = "Join me on a git adventure to archive, backup, and store offline some old code repos"
slug = "git"
authors = ["Ivan Lawrence"]
tags = ["git", "nerdler", "tech"]
categories = ["HowTo"]
externalLink = ""
series = []
+++

I'm not sure why this isn't really covered simply in one place but here it goes...

Using just the cli you can do the following:

FYI: the `git archive` command is not the thing we want, that is more for prepping code for deployment to a site…

What we really want is to [offline or backup the repo](https://docs.github.com/en/repositories/archiving-a-github-repository/backing-up-a-repository)

The idea is:

- `git clone --mirror <repository_url>`
    - which results in a dir `<repository_url>.git/`

- `cd <repository_url>.git/`

- `git bundle create ../<repository_name>.bundle --all` 
    - which makes a single file which can be cloned from later

- `zstd --compress --progress --threads=0 --rm <repository_name>.bundle`
    - will compress it in place (deleting the uncompressed original)

Then to get it all back you reverse the process

- `zstd --decompress --progress --threads=0 --rm <repository_name>.bundle.zst`
    - decompress the file 

- `git clone /path/to/<repository_name>.bundle /path/to/restored_repo`

and that should be it…

In practice, when I do this all the branches are remote still

```

  master

* newfeature

  remotes/origin/20190108-release

  remotes/origin/20220509-release

  remotes/origin/406-thing

```

but the remote is actually the bundle

```

ivan@srv:/ssd/git_repo_restored$ git remote -v

origin  /ssd/git_rep.bundle (fetch)

origin  /ssd/git_rep.bundle (push)

```