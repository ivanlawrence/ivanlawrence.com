+++
date = "Thu, 12 Sep 2013 19:45:00 +0000"
title = "Creating a StorNext Filesystem cvfs v43 or earlier"
slug = "[SLUG]"
authors = [ "Ivan Lawrence" ]
tags = [ "nerdler" ]
categories = []
series = [ ]
+++

# Creating a StorNext Filesystem cvfs v43 or earlier

Creating a StorNext Filesystem (cvfs v4.3 or earlier)
=====================================================

You may have just provisioned some new [DDN 9900 LUNs](http://nerdler.blogspot.com/2013/09/adding-disks-and-creating-luns-in.html "Adding-Disks-and-Creating-LUNs-in-DDN9900-and-DDN-S7000-Chassis") or otherwise added new storage to your StorNext based SAN.  You now need to create the filesystem.

### Prerequisites:

-   StorNext MDS/MDC (MetaData Server/Controller) in this example this will be built on RHEL
-   A LUN presented to the MDS/MDC system(s) to be used for metadata storage.  This should be fast, capable of large [IOPS](http://en.wikipedia.org/wiki/IOPS "en.wikipedia.org - IOPS"), and as low latency as possible.
-   LUNs presented to all of your client systems to be used for your data pool where the StorNext file system will be created and you data will be stored.

### Overview:

-   [CVLABEL - Label the Disks / LUNs for use with StorNext](#cvlabel)
-   [Making the File System Config File](#fs_cfg)
-   [CVMKFS - Make the File System](#cvmkfs)
-   [Bringing the New File System Online](#fs_online)
-   [Making the New File System Automatically Online and Mount Across Reboots](#fs_automount_online)

### CVLABEL - Label the Disks / LUNs for use with StorNext

**WARNING**: _The CVLABEL command is similar to formatting a disk!  Using the wrong device name could result in data loss!_

First you need to view the LUNs that are available to StorNext.  This should be done via the StorNext command `cvlabel`:

NOTE: Using the command `cvlabel -c` will output information in the same format as is required to make a "cvlabel file" for "labeling" later in the process.

\[user@system\]# **cvlabel -c**
CvfsDisk\_UNKNOWN /dev/sda    # host 2 lun 0 sectors 1170619103 sector\_size 512 inquiry \[DotHill DH3000          T230\] serial 600C0FF000132F1A8BC8EB4E01000000
CvfsDisk\_UNKNOWN /dev/sdb    # host 1 lun 0 sectors 487997407 sector\_size 512 inquiry \[Adaptec systemOS       V1.0\] serial 166D4B28
CvfsDisk\_UNKNOWN /dev/sdd    # host 2 lun 0 sectors 9376430047 sector\_size 512 inquiry \[DDN     S2A 9900        6.11\] serial 60001FF01590A361000001000008BB85
CvfsDisk\_UNKNOWN /dev/sdp    # host 2 lun 1 sectors 9376430047 sector\_size 512 inquiry \[DDN     S2A 9900        6.11\] serial 60001FF0157EA47E010001000008BB85
CvfsDisk\_UNKNOWN /dev/sdv    # host 2 lun 2 sectors 9376430047 sector\_size 512 inquiry \[DDN     S2A 9900        6.11\] serial 60001FF01590A3C8020001000008BB85

Output this same info to a file `cvlabel -c >> /path/to/file.txt` to then manipulate it for "labeling".  Remove all LUNs that are not applicable by running the output through the `grep` command to focus on only the DotHill for metadata LUNs, DDN for data LUNs, or all LUNs from a particular host if you have multiple paths to your storage.  (ex: `cvlabel -c | grep "host 2" | grep DDN >> /path to file` or if you know how the system or OS disk will be presented you can show everything but that `cvlabel -c | grep -v Adaptec >> /path to file`)

The resulting info breaks down as follows:

`CvfsDisk_UNKNOWN` = StorNext Label

`/dev/sda` = Device

`# host 2 lun 0 sectors 1170619103 sector_size 512 inquiry` = commented (unused) info about the LUN to detarmain source

`[DotHill DH3000 T230]` = \[ Vendor info \]

`serial 600C0FF000132F1A8BC8EB4E01000000` = LUN / Disk serial number

Replace `CvfsDisk_UNKNOWN` with the desired label (ex: `LUN14` to `LUN16`) in the text file.

**META\_LUN2** /dev/sda 1170619103 EFI    # host 2 lun 0 sectors 1170619103 sector\_size 512 inquiry \[DotHill DH3000          T230\] serial 600C0FF000132F1A8BC8EB4E01000000
**LUN14** /dev/sdd 9376430047 EFI    # host 2 lun 0 sectors 9376430047 sector\_size 512 inquiry \[DDN     S2A 9900        6.11\] serial 60001FF01590A361000001000008BB85
**LUN15** /dev/sdp 9376430047 EFI    # host 2 lun 1 sectors 9376430047 sector\_size 512 inquiry \[DDN     S2A 9900        6.11\] serial 60001FF0157EA47E010001000008BB85
**LUN16** /dev/sdv 9376430047 EFI    # host 2 lun 2 sectors 9376430047 sector\_size 512 inquiry \[DDN     S2A 9900        6.11\] serial 60001FF01590A3C8020001000008BB85

 NOTE:  If you have redundant paths you only have to label each LUN once on any one of the paths, not once per path.  Also, be sure LUN labels are unique per LUN, meaning, LUN1 may be presented multiple times but in the storage device (DDN) there is only one LUN1.  If multiple unique LUNs are given the same names data corruption may occur!

Apply the label(s) using `cvlabel /path/to/cvlabel/file.txt`: (ex: `cvlabel.txt`)

NOTE: _Be sure the LUN\_label - to - device association in the file is correct or data loss could occur!_  You will be prompted for each LUN prior to making changes, _**please**_ read all the information completely before proceeding.

\[user@system\]# **cvlabel cvlabel.txt**

\*WARNING\* This program will over-write volume labels on the
          devices specified in the file "cvlabel.txt".

          After execution, the devices will only be usable by the
          StorNext. You will have to re-partition the
          devices to use them on a different file system.


Do you want to proceed? (Y / N) -> **y**

/dev/sdd   \[DDN     S2A 9900        6.11\] SNFS-EFI "LUN14"  Controller 'DAE54B28', Serial 'DAE54B28', Sector Size 512, Sectors 9376430047 (4.8TB), GUID 99a524b2-f943-11e2-b832-00259004d1b4 \[Tue Jul 30 11:12:31 2013 00:25:90:04:d1:b4\]

Disk "/dev/sdd" already has a valid label, and is being changed:

- The ctq\_depth is changing from 0 to 16.
  This may change disk performance on IRIX systems.

Warning: The raw capacity (9376430047 sectors) of disk "/dev/sdd" might make it unusable by some older Solaris systems.

Warning: Because it will use an EFI label, disk "/dev/sdd" will not be usable by earlier StorNext releases.

Do you want to re-label it SNFS-EFI - Name: LUN14 Sectors: 9376430047 (Y / N) -> **y**
New Volume Label -Device: /dev/sda  SNFS Label: LUN14  Sectors: 9376430047.

### Making the File System Config File

In some cases you are able to use an existing file system config file (or the following) and simply change a few parts to add additional file systems to an existing SAN.  For this example we will use the following existing file: `volA.cfgx`

<?xml version="1.0"?>
<configDoc xmlns="http://www.quantum.com/snfs" version="1.0">
  <config configVersion="0" name="volA" fsBlockSize="16384" journalSize="16777216">
    <globals>
      <abmFreeLimit>false</abmFreeLimit>
      <allocationStrategy>round</allocationStrategy>
      <haFsType>HaUnmanaged</haFsType>
      <bufferCacheSize>33554432</bufferCacheSize>
      <cvRootDir>/</cvRootDir>
      <storageManager>false</storageManager>
      <dataMigrationThreadPoolSize>128</dataMigrationThreadPoolSize>
      <debug>00000000</debug>
      <dirWarp>true</dirWarp>
      <extentCountThreshold>32768</extentCountThreshold>
      <enforceAcls>false</enforceAcls>
      <fileLocks>false</fileLocks>
      <fileLockResyncTimeOut>20</fileLockResyncTimeOut>
      <forcePerfectFit>false</forcePerfectFit>
      <fsCapacityThreshold>0</fsCapacityThreshold>
      <globalSuperUser>true</globalSuperUser>
      <inodeCacheSize>65536</inodeCacheSize>
      <inodeExpandMin>0</inodeExpandMin>
      <inodeExpandInc>0</inodeExpandInc>
      <inodeExpandMax>0</inodeExpandMax>
      <inodeDeleteMax>0</inodeDeleteMax>
      <inodeStripeWidth>0</inodeStripeWidth>
      <maxConnections>32</maxConnections>
      <maxLogSize>4194304</maxLogSize>
      <maxLogs>4</maxLogs>
      <remoteNotification>false</remoteNotification>
      <reservedSpace>true</reservedSpace>
      <fsmRealTime>false</fsmRealTime>
      <fsmMemLocked>false</fsmMemLocked>
      <opHangLimitSecs>180</opHangLimitSecs>
      <perfectFitSize>131072</perfectFitSize>
      <quotas>false</quotas>
      <restoreJournal>false</restoreJournal>
      <restoreJournalDir/>
      <restoreJournalMaxHours>0</restoreJournalMaxHours>
      <restoreJournalMaxMb>0</restoreJournalMaxMb>
      <stripeAlignSize>0</stripeAlignSize>
      <trimOnClose>0</trimOnClose>
      <threadPoolSize>64</threadPoolSize>
      <unixDirectoryCreationModeOnWindows>777</unixDirectoryCreationModeOnWindows>
      <unixIdFabricationOnWindows>false</unixIdFabricationOnWindows>
      <unixFileCreationModeOnWindows>777</unixFileCreationModeOnWindows>
      <unixNobodyUidOnWindows>501</unixNobodyUidOnWindows>
      <unixNobodyGidOnWindows>501</unixNobodyGidOnWindows>
      <windowsSecurity>false</windowsSecurity>
      <eventFiles>true</eventFiles>
      <eventFileDir/>
      <allocSessionReservationSize>1073741824</allocSessionReservationSize>
    </globals>
    <diskTypes>
      <diskType typeName="MetaDrive" sectors="1170619103" sectorSize="512"/>
      <diskType typeName="DataDrive" sectors="9376430047" sectorSize="512"/>
    </diskTypes>
    <stripeGroups>
      <stripeGroup index="0" name="MetaFiles" status="up" stripeBreadth="65536" read="true" write="true" metadata="true" journal="true" userdata="false" realTimeIOs="0" realTimeIOsReserve="0" realTimeMB="0" realTimeMBReserve="0" realTimeTokenTimeout="0" multipathMethod="rotate">
        <disk index="0" diskLabel="META\_LUN0" diskType="MetaDrive" ordinal="0"/>
      </stripeGroup>
      <stripeGroup index="1" name="DataFiles\_0" status="up" stripeBreadth="1048576" read="true" write="true" metadata="false" journal="false" userdata="true" realTimeIOs="0" realTimeIOsReserve="0" realTimeMB="0" realTimeMBReserve="0" realTimeTokenTimeout="0" multipathMethod="rotate">
        <affinities exclusive="false">
          <affinity>data0</affinity>
        </affinities>
        <disk index="0" diskLabel="LUN0" diskType="DataDrive" ordinal="1"/>
        <disk index="1" diskLabel="LUN1" diskType="DataDrive" ordinal="2"/>
        <disk index="2" diskLabel="LUN2" diskType="DataDrive" ordinal="3"/>
        <disk index="3" diskLabel="LUN3" diskType="DataDrive" ordinal="4"/>
        <disk index="4" diskLabel="LUN4" diskType="DataDrive" ordinal="5"/>
        <disk index="5" diskLabel="LUN5" diskType="DataDrive" ordinal="6"/>
        <disk index="6" diskLabel="LUN6" diskType="DataDrive" ordinal="7"/>
        <disk index="7" diskLabel="LUN7" diskType="DataDrive" ordinal="8"/>
        <disk index="8" diskLabel="LUN8" diskType="DataDrive" ordinal="9"/>
        <disk index="9" diskLabel="LUN9" diskType="DataDrive" ordinal="10"/>
      </stripeGroup>
    </stripeGroups>
  </config>
</configDoc>

The above file can be copied to make a new file system config file with some changes: `volB.cfgx`

<?xml version="1.0"?>
<configDoc xmlns="http://www.quantum.com/snfs" version="1.0">
  <config configVersion="0" name="volB" fsBlockSize="16384" journalSize="16777216">
    <globals>
      <abmFreeLimit>false</abmFreeLimit>
      <allocationStrategy>round</allocationStrategy>
      <haFsType>HaUnmanaged</haFsType>
      <bufferCacheSize>33554432</bufferCacheSize>
      <cvRootDir>/</cvRootDir>
      <storageManager>false</storageManager>
      <dataMigrationThreadPoolSize>128</dataMigrationThreadPoolSize>
      <debug>00000000</debug>
      <dirWarp>true</dirWarp>
      <extentCountThreshold>32768</extentCountThreshold>
      <enforceAcls>false</enforceAcls>
      <fileLocks>false</fileLocks>
      <fileLockResyncTimeOut>20</fileLockResyncTimeOut>
      <forcePerfectFit>false</forcePerfectFit>
      <fsCapacityThreshold>0</fsCapacityThreshold>
      <globalSuperUser>true</globalSuperUser>
      <inodeCacheSize>65536</inodeCacheSize>
      <inodeExpandMin>0</inodeExpandMin>
      <inodeExpandInc>0</inodeExpandInc>
      <inodeExpandMax>0</inodeExpandMax>
      <inodeDeleteMax>0</inodeDeleteMax>
      <inodeStripeWidth>0</inodeStripeWidth>
      <maxConnections>32</maxConnections>
      <maxLogSize>4194304</maxLogSize>
      <maxLogs>4</maxLogs>
      <remoteNotification>false</remoteNotification>
      <reservedSpace>true</reservedSpace>
      <fsmRealTime>false</fsmRealTime>
      <fsmMemLocked>false</fsmMemLocked>
      <opHangLimitSecs>180</opHangLimitSecs>
      <perfectFitSize>131072</perfectFitSize>
      <quotas>false</quotas>
      <restoreJournal>false</restoreJournal>
      <restoreJournalDir/>
      <restoreJournalMaxHours>0</restoreJournalMaxHours>
      <restoreJournalMaxMb>0</restoreJournalMaxMb>
      <stripeAlignSize>0</stripeAlignSize>
      <trimOnClose>0</trimOnClose>
      <threadPoolSize>64</threadPoolSize>
      <unixDirectoryCreationModeOnWindows>777</unixDirectoryCreationModeOnWindows>
      <unixIdFabricationOnWindows>false</unixIdFabricationOnWindows>
      <unixFileCreationModeOnWindows>777</unixFileCreationModeOnWindows>
      <unixNobodyUidOnWindows>501</unixNobodyUidOnWindows>
      <unixNobodyGidOnWindows>501</unixNobodyGidOnWindows>
      <windowsSecurity>false</windowsSecurity>
      <eventFiles>true</eventFiles>
      <eventFileDir/>
      <allocSessionReservationSize>1073741824</allocSessionReservationSize>
    </globals>
    <diskTypes>
      <diskType typeName="MetaDrive" sectors="xxxxxxxxxx" sectorSize="512"/>
      <diskType typeName="DataDrive" sectors="xxxxxxxxxx" sectorSize="512"/>
    </diskTypes>
    <stripeGroups>
      <stripeGroup index="0" name="MetaFiles" status="up" stripeBreadth="65536" read="true" write="true" metadata="true" journal="true" userdata="false" realTimeIOs="0" realTimeIOsReserve="0" realTimeMB="0" realTimeMBReserve="0" realTimeTokenTimeout="0" multipathMethod="rotate">
        <disk index="0" diskLabel="META\_LUN2" diskType="MetaDrive" ordinal="0"/>
      </stripeGroup>
      <stripeGroup index="1" name="DataFiles\_0" status="up" stripeBreadth="1048576" read="true" write="true" metadata="false" journal="false" userdata="true" realTimeIOs="0" realTimeIOsReserve="0" realTimeMB="0" realTimeMBReserve="0" realTimeTokenTimeout="0" multipathMethod="rotate">
        <affinities exclusive="false">
          <affinity>data0</affinity>
        </affinities>
        <disk index="0" diskLabel="LUN14" diskType="DataDrive" ordinal="1"/>
        <disk index="1" diskLabel="LUN15" diskType="DataDrive" ordinal="2"/>
        <disk index="2" diskLabel="LUN16" diskType="DataDrive" ordinal="3"/>
      </stripeGroup>
    </stripeGroups>
  </config>
</configDoc>

 NOTE: _The following is the minimum amount of info that needs to be changed (needs to be unique across file systems or data loss can occur)!_  The name of the file needs to match the file system name.  Additionally, based on the info gathered from running the [CVLABEL](#cvlabel) command earlier, be sure to reflect the new metadata label and sectors and data label and sectors from the `cvlabel` output.

### CVMKFS - Make the File System

The `cvmkfs` command is used to create / format the StorNext file system using the `.cfgx` file. Be sure to use the **_file system_** name and not the _file_ name (ex: use `cvmkfs volB` not `cvmkfs volB.cfgx`)

\[user@system\]# **cvmkfs volB**
Checked Build disabled - default.
StorNext File System Initializer.

\*\* WARNING \*\* This will destroy all contents and data of the

              --- \[volB\] ---

              file system. Re-initialization cannot be un-done!

Do you want to proceed? (Y/\[N\]) -> **y**

Re-initializing file system 'volB'.


\*Warning\*: Inode Expand Min is deprecated and will no longer be supported in future releases
\*Warning\*: Inode Expand Max is deprecated and will no longer be supported in future releases
\*Warning\*: Inode Expand Inc is deprecated and will no longer be supported in future releases
\*Warning\*: AllocationStrategy must be Round Robin ("round") when AllocSessionReservation is enabled.  Ignoring current setting.

Shared Meta Data File System.
Meta Data Root is on "MetaFiles".
Resetting Arbitration Block.
Writing Configuration Information Block.
Writing Root Directory Block.
Writing Disk Information Block.
Writing Stripe Group Index "MetaFiles".
Writing Stripe Group Index "DataFiles\_0".
Resetting File System Journal.
Creating Free List.
Writing Initial 512 Inodes.
Inode block size is 1024 bytes.
Writing Initialization Control Block.
Setting "Force Stripe Alignment"
Resetting Super Block.
Writing Reserved Allocations.

File system 'volB' successfully created.

### Bringing the New File System Online

Using the `cvadmin` interactive console you need to `start` then `activate` the file system:

\[user@system\]# **cvadmin**
StorNext Administrator

Enter command(s)
For command help, enter "help" or "?".

List FSS

File System Services (\* indicates service is in control of FS):

No FSSs are active.
Select FSM "none"


snadmin> **start volB**
Starting FSS locally.
Start FSS "volB"

FSS 'volB' start initiated.
FSS 'volB' started.

snadmin> **select**
List FSS

File System Services (\* indicates service is in control of FS):
 1> volB\[0\]              located on Venice02:36526 (pid 4814)

No FSSs are active.

snadmin> **activate volB**
Activate FSM "volB"

 Created           :    Wed Sep 11 17:37:36 2013
 Active Connections:    0
 Fs Block Size     :    4K
 Msg Buffer Size   :    4K
 Disk Devices      :    4
 Stripe Groups     :    2
 Fs Blocks         :    3071783232 (11.44 TB)
 Fs Blocks Free    :    3070700352 (11.44 TB) (99%)


snadmin (volB) > **select**
List FSS

File System Services (\* indicates service is in control of FS):
 1>\*volB\[0\]              located on Venice02:36526 (pid 4814)


snadmin (volB) > \_

NOTE: the `select` command can be used to "refresh" the list of file systems.  Notice there is no `*` next to the file system prior to activation, the presence of an `*` indicates the file system is active.

Now the file system needs to me mounted for use:

\[user@system\]# **mkdir /media/volB**
\[root@Venice02 ~\]# **mount -t cvfs volB /media/volB/**
mount.cvfs: Filesystem volB mounted on /media/volB
\[user@system\]# **mount**
/dev/sdb1 on / type ext3 (rw)
proc on /proc type proc (rw)
sysfs on /sys type sysfs (rw)
devpts on /dev/pts type devpts (rw,gid=5,mode=620)
tmpfs on /dev/shm type tmpfs (rw)
none on /proc/sys/fs/binfmt\_misc type binfmt\_misc (rw)
sunrpc on /var/lib/nfs/rpc\_pipefs type rpc\_pipefs (rw)
nfsd on /proc/fs/nfsd type nfsd (rw)
/dev/cvfsctl1\_volB on /media/volB type cvfs (rw,sparse=yes)

Your new file system should now me usable / mountable by any of your SAN clients.

### Making the New File System Automatically Online and Mount Across Reboots

 The file system needs to be listed among the file systems being managed my this server (by the FSM service).  This means the file system name needs to be added to `/usr/cvfs/config/fsmlist` file within the MDS.

NOTE: If running multiple MDSes for failover / high availability you will need to do the following on both servers.

Edit the file `/usr/cvfs/config/fsmlist` and add the following:

volB . 0

NOTE: On the secondary MDS change the `0` to a `1`:

volB . 1

You then need to add the mount to `/etc/fstab`:

LABEL=/                 /                       ext3    defaults        1 1
LABEL=backup            /mnt/backup             ext3    noauto          0 0
tmpfs                   /dev/shm                tmpfs   defaults        0 0
devpts                  /dev/pts                devpts  gid=5,mode=620  0 0
sysfs                   /sys                    sysfs   defaults        0 0
proc                    /proc                   proc    defaults        0 0
LABEL=swap              swap                    swap    defaults        0 0
volB                    /media/volB             cvfs    rw              0 0

 NOTE: It is a good idea to backup the file before making changes.

Your new filesystem should now be usable locally and persistent after reboot.