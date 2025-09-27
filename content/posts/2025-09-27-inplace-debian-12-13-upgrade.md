+++
draft = false
date = "2025-09-27T06:17:45.633Z"
title = "In-place update of Debian from 12 (Bookworm) to 13 (Trixie)"
description = "How to do an in place upgrade of debian to the next version"
slug = "place-update-debian-12-bookworm-13-trixie"
authors = "Ivan Lawrence"
tags = [ "tech", "technology", "os" ]
categories = [ "HowTo" ]
externalLink = ""
series = [ ]
+++
## If you care about your data back it up!
This is just a test box so I'm gonna skip it but here is a good idea

```
# Example: Backup key directories
sudo tar czf /root/backup-etc.tar.gz /etc
sudo tar czf /root/backup-var-lib.tar.gz /var/lib
```

## Upgrade the old version you're about to upgrade
```
sudo apt update
sudo apt full-upgrade
sudo apt autoremove
```

## In place replace the version names in the packages list
```
sudo sed -i 's/bookworm/trixie/g' /etc/apt/sources.list
```

This is what mine looks like
```
#deb cdrom:[Debian GNU/Linux 12.11.0 _Bookworm_ - Official amd64 NETINST with firmware 20250517-09:51]/ trixie contrib main non-free-firmware

deb http://deb.debian.org/debian/ trixie main non-free-firmware
deb-src http://deb.debian.org/debian/ trixie main non-free-firmware

deb http://security.debian.org/debian-security trixie-security main non-free-firmware
deb-src http://security.debian.org/debian-security trixie-security main non-free-firmware

# trixie-updates, to get updates before a point release is made;
# see https://www.debian.org/doc/manuals/debian-reference/ch02.en.html#_updates_and_backports
deb http://deb.debian.org/debian/ trixie-updates main non-free-firmware
deb-src http://deb.debian.org/debian/ trixie-updates main non-free-firmware

# This system was installed using small removable media
# (e.g. netinst, live or single CD). The matching "deb cdrom"
# entries were disabled at the end of the installation process.
# For information about how to configure apt package sources,
# see the sources.list(5) manual.
```

## Update/upgrade
Do an update and then full-upgrade.
Note: start a tmux session if your SSHing into the box so the upgrade can continue uninterrupted.
```
sudo apt update
sudo apt full-upgrade
```

This is gonna take a while, I needed to download 300+ new packages.

I also got a message/prompt asking:
> There are services installed on your system which need to be restarted when certain libraries, such as libpam, libc, and libssl, are upgraded. Since these restarts may cause interruptions of service for the system, you will normally be prompted on each upgrade for the list of services you wish to restart.  You can choose this option to avoid being prompted; instead, all necessary restarts will be done for you automatically so you can avoid being asked questions on each library upgrade.
> 
> Restart services during package upgrades without asking?


I chose `<yes>` 

## Cleanup and reboot
Running the following will dump any no longer needed packages and bounce the server
```
sudo apt --purge autoremove -y
sudo apt clean
sudo reboot
```

## Confirming it worked after reboot
```
cat /etc/os-release

PRETTY_NAME="Debian GNU/Linux 13 (trixie)"
NAME="Debian GNU/Linux"
VERSION_ID="13"
VERSION="13 (trixie)"
VERSION_CODENAME=trixie
DEBIAN_VERSION_FULL=13.1
ID=debian
HOME_URL="https://www.debian.org/"
SUPPORT_URL="https://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"
```

I love debian!