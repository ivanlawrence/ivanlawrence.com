---
title: ZFS options saved my NAS
description: Well, actually the change saved my internal NVMe data drive, but you get the point.
date: 2026-01-31T08:52:08.001Z
preview: ""
tags:
    - btrfs
    - incus
    - LXC
    - technology
    - zfs
    - LXD
categories: []
authors: Ivan Lawrence
externalLink: ""
series: {}
author: Ivan Lawrence
---
# Context
I have a few servers running Ubuntu 24.04 and LXD for virtualization.  
When I first set things up the recommendations I found were to use either BTRFS or ZFS for the data pool.  Well, I already had some btrfs experience and it was the default listed when I first ran `lxd init` for the data pool so `¯\_(ツ)_/¯` 
I ran LXC containers of minimal Ubuntu and had a few MySQL databases running. And the IOPS seemed fine, but then after some time the host showed kernel interrupts resetting the NVMe controller.  Well :poop:  Maybe the drive is just going bad?

I checked the nvme smart-log:
```
root@srv3:~# nvme smart-log /dev/nvme0
Smart Log for NVME device:nvme0 namespace-id:ffffffff
critical_warning                        : 0
temperature                             : 42 °C (315 K)
available_spare                         : 100%
available_spare_threshold               : 1%
percentage_used                         : 9%
endurance group critical warning summary: 0
Data Units Read                         : 6511080360 (3.33 PB)
Data Units Written                      : 46253039 (23.68 TB)
host_read_commands                      : 36934739756
host_write_commands                     : 1080500554
controller_busy_time                    : 162856
power_cycles                            : 18
power_on_hours                          : 5358
unsafe_shutdowns                        : 14
media_errors                            : 0
num_err_log_entries                     : 0
Warning Temperature Time                : 0
Critical Composite Temperature Time     : 0
Temperature Sensor 1           : 42 °C (315 K)
Temperature Sensor 2           : 28 °C (301 K)
Thermal Management T1 Trans Count       : 0
Thermal Management T2 Trans Count       : 0
Thermal Management T1 Total Time        : 0
Thermal Management T2 Total Time        : 0
```
Yikes, 9% used, thats not great... but then again there was a ton of reads and some writes... but that can't be it?

Every once in a while, when using VSCode and some of the RHEL based Java extensions I could get server CPU spikes and things would go all sideways and the container would just be unresponsive and I'd have to kill it.

I did some of the normal things like log parsing to see about coordinating errors and bla bla.  I swapped an NVMe that was in the box with the most kernel interrupts... eventually the problems returned on the new drive!

Everything is falling apart.

# What should I try?
Only after watching an [interview with the creator of LXD/Incus](https://www.youtube.com/watch?v=_wucXs0zYMM) where it seemed like ZFS might be the right way to go... and some other place that mentioned ZFS's ARC was magic and it was worth a shot.

I put the "bad" NVMe in a USB housing and did a big shuffle swap.  I settled on `zpool create -o ashift=12 -O compression=lz4 -O atime=off <pool_name> /dev/disk/by-id/<id>`
- `ashift=12` (4k blocks) on zfs to match the native/actual block size for NVMe
- `compression=lz4` for a the price of a little CPU fewer bits have to go down the PCIe lanes and get written to fewer NVMe blocks.
- `atime=off` further reducing the writes

Google the details since it has to be configured at pool creation and there are some more tuning you can do for DBs (I didn't do that yet since baby steps, but I might not)

# Results?
Night and day.  Before I would have huge periodic CPU spikes and IOPS starvation.  Now its all good.  The ARC cache is doing it's fair share of work ensuring things are warm for reads and that probably helps a lot too...

```
root@server:~# arcstat 1
    time  read  ddread  ddh%  dmread  dmh%  pread  ph%   size      c  avail
09:28:34     0       0     0       0     0      0    0    62G    62G   1.8G
09:28:35   154       0     0     154   100      0    0    62G    62G   1.8G
09:28:36   777     612    93     164   100      1  100    62G    62G   1.8G
09:28:37     0       0     0       0     0      0    0    62G    62G   1.8G
09:28:38     0       0     0       0     0      0    0    62G    62G   1.8G

root@srv2:~# zpool iostat -vl 1
                                              capacity     operations     bandwidth    total_wait     disk_wait    syncq_wait    asyncq_wait  scrub   trim  rebuild
pool                                        alloc   free   read  write   read  write   read  write   read  write   read  write   read  write   wait   wait   wait
------------------------------------------  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----
default                                     1.18T  2.45T     41     16  4.61M   155K  200us   98us  193us   36us    1us    1us  138us   64us  516us      -      -
  nvme-WD_BLACK_SN850X_4000GB_xxxxxxxxxxx  1.18T  2.45T     41     16  4.61M   155K  200us   98us  193us   36us    1us    1us  138us   64us  516us      -      -
------------------------------------------  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----
                                              capacity     operations     bandwidth    total_wait     disk_wait    syncq_wait    asyncq_wait  scrub   trim  rebuild
pool                                        alloc   free   read  write   read  write   read  write   read  write   read  write   read  write   wait   wait   wait
------------------------------------------  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----
default                                     1.18T  2.45T      0      0  91.9K      0  393us      -  393us      -    3us      -      -      -      -      -      -
  nvme-WD_BLACK_SN850X_4000GB_xxxxxxxxxxx  1.18T  2.45T      0      0  91.9K      0  393us      -  393us      -    3us      -      -      -      -      -      -
------------------------------------------  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----
                                              capacity     operations     bandwidth    total_wait     disk_wait    syncq_wait    asyncq_wait  scrub   trim  rebuild
pool                                        alloc   free   read  write   read  write   read  write   read  write   read  write   read  write   wait   wait   wait
------------------------------------------  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----
default                                     1.18T  2.45T      0    107   104K   979K  393us   86us  393us   34us    3us    1us      -   54us      -      -      -
  nvme-WD_BLACK_SN850X_4000GB_xxxxxxxxxxx  1.18T  2.45T      0    107   104K   979K  393us   86us  393us   34us    3us    1us      -   54us      -      -      -
------------------------------------------  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----
                                              capacity     operations     bandwidth    total_wait     disk_wait    syncq_wait    asyncq_wait  scrub   trim  rebuild
pool                                        alloc   free   read  write   read  write   read  write   read  write   read  write   read  write   wait   wait   wait
------------------------------------------  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----
default                                     1.18T  2.45T      3      0   363K      0  393us      -  393us      -    3us      -    1us      -      -      -      -
  nvme-WD_BLACK_SN850X_4000GB_xxxxxxxxxxx  1.18T  2.45T      3      0   363K      0  393us      -  393us      -    3us      -    1us      -      -      -      -
------------------------------------------  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----
                                              capacity     operations     bandwidth    total_wait     disk_wait    syncq_wait    asyncq_wait  scrub   trim  rebuild
pool                                        alloc   free   read  write   read  write   read  write   read  write   read  write   read  write   wait   wait   wait
------------------------------------------  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----
default                                     1.18T  2.45T      0      0   104K      0  393us      -  393us      -    3us      -      -      -      -      -      -
  nvme-WD_BLACK_SN850X_4000GB_xxxxxxxxxxx  1.18T  2.45T      0      0   104K      0  393us      -  393us      -    3us      -      -      -      -      -      -
------------------------------------------  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----  -----
```

If this helps even one single other person then it's worth it.