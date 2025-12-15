
# LVM (Logical Volume Management)

## Example 1: create physical partition
### 1- Add 2 virtual hard drive with 10 GiB to your VM

```bash
[root@localhost ~]# lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda           8:0    0   20G  0 disk 
├─sda1        8:1    0    1G  0 part /boot
└─sda2        8:2    0   19G  0 part 
  ├─rl-root 253:0    0   17G  0 lvm  /
  └─rl-swap 253:1    0    2G  0 lvm  [SWAP]
sdb           8:16   0   10G  0 disk 
sdc           8:32   0   10G  0 disk 
sr0          11:0    1 1024M  0 rom 
```

### 2- Make following partitions using fdisk:

           a. partition 1 => 4 GiB
           b. partition 2 => 7 GiB
           c. partition 3 => 9 GiB

```bash
[root@localhost ~]# fdisk -l /dev/sdb
Disk /dev/sdb: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

```bash
[root@localhost ~]# fdisk /dev/sdb

Welcome to fdisk (util-linux 2.37.4).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0x86fffbf6.

Command (m for help): p
Disk /dev/sdb: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x86fffbf6

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-20971519, default 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-20971519, default 20971519): +4G

Created a new partition 1 of type 'Linux' and of size 4 GiB.

Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (2-4, default 2): 2
First sector (8390656-20971519, default 8390656): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (8390656-20971519, default 20971519): +7G
Value out of range.
Last sector, +/-sectors or +/-size{K,M,G,T,P} (8390656-20971519, default 20971519): w
Last sector, +/-sectors or +/-size{K,M,G,T,P} (8390656-20971519, default 20971519): 
Command (m for help): w

The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

```bash
[root@localhost ~]# fdisk -l /dev/sdb
Disk /dev/sdb: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x86fffbf6

Device     Boot Start     End Sectors Size Id Type
/dev/sdb1        2048 8390655 8388608   4G 83 Linux
```

```bash
[root@localhost ~]# fdisk /dev/sdc

Welcome to fdisk (util-linux 2.37.4).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0x2e8bb6af.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-20971519, default 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-20971519, default 20971519): +7G

Created a new partition 1 of type 'Linux' and of size 7 GiB.

Command (m for help): p
Disk /dev/sdc: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x2e8bb6af

Device     Boot Start      End  Sectors Size Id Type
/dev/sdc1        2048 14682111 14680064   7G 83 Linux

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

```bash
[root@localhost ~]# fdisk -l /dev/sdb
Disk /dev/sdb: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x86fffbf6

Device     Boot Start     End Sectors Size Id Type
/dev/sdb1        2048 8390655 8388608   4G 83 Linux
```

```bash
[root@localhost ~]# fdisk -l /dev/sdc
Disk /dev/sdc: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x2e8bb6af

Device     Boot Start      End  Sectors Size Id Type
/dev/sdc1        2048 14682111 14680064   7G 83 Linux
```

## As you noticed, you can not figured out these three partitions using physical partitions.

---

## Example 2: create logical partitions
### 1- Make on both disk, on reset of free space, suitable partitions using fdisk:

```bash
[root@localhost ~]# fdisk /dev/sdb

Welcome to fdisk (util-linux 2.37.4).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): p
Disk /dev/sdb: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x86fffbf6

Device     Boot Start     End Sectors Size Id Type
/dev/sdb1        2048 8390655 8388608   4G 83 Linux

Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (2-4, default 2): 2
First sector (8390656-20971519, default 8390656): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (8390656-20971519, default 20971519): 

Created a new partition 2 of type 'Linux' and of size 6 GiB.

Command (m for help): p
Disk /dev/sdb: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x86fffbf6

Device     Boot   Start      End  Sectors Size Id Type
/dev/sdb1          2048  8390655  8388608   4G 83 Linux
/dev/sdb2       8390656 20971519 12580864   6G 83 Linux

Command (m for help): t
Partition number (1,2, default 2): 1
Hex code or alias (type L to list all): L

00 Empty            24 NEC DOS          81 Minix / old Lin  bf Solaris        
01 FAT12            27 Hidden NTFS Win  82 Linux swap / So  c1 DRDOS/sec (FAT-
02 XENIX root       39 Plan 9           83 Linux            c4 DRDOS/sec (FAT-
03 XENIX usr        3c PartitionMagic   84 OS/2 hidden or   c6 DRDOS/sec (FAT-
04 FAT16 <32M       40 Venix 80286      85 Linux extended   c7 Syrinx         
05 Extended         41 PPC PReP Boot    86 NTFS volume set  da Non-FS data    
06 FAT16            42 SFS              87 NTFS volume set  db CP/M / CTOS / .
07 HPFS/NTFS/exFAT  4d QNX4.x           88 Linux plaintext  de Dell Utility   
08 AIX              4e QNX4.x 2nd part  8e Linux LVM        df BootIt         
09 AIX bootable     4f QNX4.x 3rd part  93 Amoeba           e1 DOS access     
0a OS/2 Boot Manag  50 OnTrack DM       94 Amoeba BBT       e3 DOS R/O        
0b W95 FAT32        51 OnTrack DM6 Aux  9f BSD/OS           e4 SpeedStor      
0c W95 FAT32 (LBA)  52 CP/M             a0 IBM Thinkpad hi  ea Linux extended 
0e W95 FAT16 (LBA)  53 OnTrack DM6 Aux  a5 FreeBSD          eb BeOS fs        
0f W95 Extd (LBA)  54 OnTrackDM6       a6 OpenBSD          ee GPT            
10 OPUS             55 EZ-Drive         a7 NeXTSTEP         ef EFI (FAT-12/16/
11 Hidden FAT12     56 Golden Bow       a8 Darwin UFS       f0 Linux/PA-RISC b
12 Compaq diagnost  5c Priam Edisk      a9 NetBSD           f1 SpeedStor      
14 Hidden FAT16 <3  61 SpeedStor        ab Darwin boot      f4 SpeedStor      
16 Hidden FAT16     63 GNU HURD or Sys  af HFS / HFS+       f2 DOS secondary  
17 Hidden HPFS/NTF  64 Novell Netware   b7 BSDI fs          fb VMware VMFS    
18 AST SmartSleep   65 Novell Netware   b8 BSDI swap        fc VMware VMKCORE 
1b Hidden W95 FAT3  70 DiskSecure Mult  bb Boot Wizard hid  fd Linux raid auto
1c Hidden W95 FAT3  75 PC/IX            bc Acronis FAT32 L  fe LANstep        
1e Hidden W95 FAT1  80 Old Minix        be Solaris boot     ff BBT            

Aliases:
   linux          - 83
   swap           - 82
   extended       - 05
   uefi           - EF
   raid           - FD
   lvm            - 8E
   linuxex        - 85
Hex code or alias (type L to list all): 8e

Changed type of partition 'Linux' to 'Linux LVM'.

Command (m for help): p
Disk /dev/sdb: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x86fffbf6

Device     Boot   Start      End  Sectors Size Id Type
/dev/sdb1          2048  8390655  8388608   4G 8e Linux LVM
/dev/sdb2       8390656 20971519 12580864   6G 83 Linux

Command (m for help): t
Partition number (1,2, default 2): 2
Hex code or alias (type L to list all): 8e

Changed type of partition 'Linux' to 'Linux LVM'.

Command (m for help): p
Disk /dev/sdb: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x86fffbf6

Device     Boot   Start      End  Sectors Size Id Type
/dev/sdb1          2048  8390655  8388608   4G 8e Linux LVM
/dev/sdb2       8390656 20971519 12580864   6G 8e Linux LVM

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

```bash
[root@localhost ~]# fdisk -l /dev/sdb
Disk /dev/sdb: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x86fffbf6

Device     Boot   Start      End  Sectors Size Id Type
/dev/sdb1          2048  8390655  8388608   4G 8e Linux LVM
/dev/sdb2       8390656 20971519 12580864   6G 8e Linux LVM
```

```bash
[root@localhost ~]# fdisk /dev/sdc

Welcome to fdisk (util-linux 2.37.4).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): p
Disk /dev/sdc: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x2e8bb6af

Device     Boot Start      End  Sectors Size Id Type
/dev/sdc1        2048 14682111 14680064   7G 83 Linux

Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (2-4, default 2): 2
First sector (14682112-20971519, default 14682112): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (14682112-20971519, default 20971519): 

Created a new partition 2 of type 'Linux' and of size 3 GiB.

Command (m for help): p
Disk /dev/sdc: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x2e8bb6af

Device     Boot    Start      End  Sectors Size Id Type
/dev/sdc1           2048 14682111 14680064   7G 83 Linux
/dev/sdc2       14682112 20971519  6289408   3G 83 Linux

Command (m for help): t
Partition number (1,2, default 2): 1
Hex code or alias (type L to list all): lvm

Changed type of partition 'Linux' to 'Linux LVM'.

Command (m for help): p
Disk /dev/sdc: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x2e8bb6af

Device     Boot    Start      End  Sectors Size Id Type
/dev/sdc1           2048 14682111 14680064   7G 8e Linux LVM
/dev/sdc2       14682112 20971519  6289408   3G 83 Linux

Command (m for help): t
Partition number (1,2, default 2): 2
Hex code or alias (type L to list all): lvm

Changed type of partition 'Linux' to 'Linux LVM'.

Command (m for help): p
Disk /dev/sdc: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x2e8bb6af

Device     Boot    Start      End  Sectors Size Id Type
/dev/sdc1           2048 14682111 14680064   7G 8e Linux LVM
/dev/sdc2       14682112 20971519  6289408   3G 8e Linux LVM

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

```bash
[root@localhost ~]# fdisk -l /dev/sdc
Disk /dev/sdc: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x2e8bb6af

Device     Boot    Start      End  Sectors Size Id Type
/dev/sdc1           2048 14682111 14680064   7G 8e Linux LVM
/dev/sdc2       14682112 20971519  6289408   3G 8e Linux LVM
```

```bash
[root@localhost ~]# lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda           8:0    0   20G  0 disk 
├─sda1        8:1    0    1G  0 part /boot
└─sda2        8:2    0   19G  0 part 
  ├─rl-root 253:0    0   17G  0 lvm  /
  └─rl-swap 253:1    0    2G  0 lvm  [SWAP]
sdb           8:16   0   10G  0 disk 
├─sdb1        8:17   0    4G  0 part 
└─sdb2        8:18   0    6G  0 part 
sdc           8:32   0   10G  0 disk 
├─sdc1        8:33   0    7G  0 part 
└─sdc2        8:34   0    3G  0 part 
sr0          11:0    1 1024M  0 rom
```
### 2- Making some Physical Volumes using /dev/sdb1, /dev/sdb2, /dev/sdc1 and /dev/sdc2:  

```bash
[root@localhost ~]# pvcreate /dev/sd[bc][12]
  Physical volume "/dev/sdb1" successfully created.
  Physical volume "/dev/sdb2" successfully created.
  Physical volume "/dev/sdc1" successfully created.
  Physical volume "/dev/sdc2" successfully created.
```

```bash
[root@localhost ~]# echo $?
0
```

```bash
[root@localhost ~]# pvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  PV         VG Fmt  Attr PSize  PFree 
  /dev/sdb1     lvm2 ---   4.00g  4.00g
  /dev/sdb2     lvm2 ---  <6.00g <6.00g
  /dev/sdc1     lvm2 ---   7.00g  7.00g
  /dev/sdc2     lvm2 ---  <3.00g <3.00g
```

```bash
[root@localhost ~]# pvscan
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  PV /dev/sdb1                      lvm2 [4.00 GiB]
  PV /dev/sdb2                      lvm2 [<6.00 GiB]
  PV /dev/sdc1                      lvm2 [7.00 GiB]
  PV /dev/sdc2                      lvm2 [<3.00 GiB]
  Total: 4 [<20.00 GiB] / in use: 0 [0   ] / in no VG: 4 [<20.00 GiB]
  ```

```bash
[root@localhost ~]# pvdisplay
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  "/dev/sdb1" is a new physical volume of "4.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb1
  VG Name               
  PV Size               4.00 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               pdQN98-LmHS-6sqS-zlkf-PuCF-9rsE-Tfqs9L
   
  "/dev/sdb2" is a new physical volume of "<6.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb2
  VG Name               
  PV Size               <6.00 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               u4Z6TK-nTTj-jIhK-Fypt-fMIK-vQnQ-HauIGK
   
  "/dev/sdc1" is a new physical volume of "7.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdc1
  VG Name               
  PV Size               7.00 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               MhrtB1-penB-kVBa-JCA6-kiJM-VbNm-t8jOdO
   
  "/dev/sdc2" is a new physical volume of "<3.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdc2
  VG Name               
  PV Size               <3.00 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               n3Q3Lo-9WuA-qSHl-NwCw-ywwg-GDti-530WRa
  ```

### 3- Creating Volume Group Anisa using Physical Volumes /dev/sdb1, /dev/sdb2, /dev/sdc1 and /dev/sdc2:

```bash
[root@localhost ~]# vgcreate anisa /dev/sd[bc][12]
  Volume group "anisa" successfully created
```

```bash
[root@localhost ~]# pvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  PV         VG    Fmt  Attr PSize  PFree 
  /dev/sdb1  anisa lvm2 a--  <4.00g <4.00g
  /dev/sdb2  anisa lvm2 a--  <6.00g <6.00g
  /dev/sdc1  anisa lvm2 a--  <7.00g <7.00g
  /dev/sdc2  anisa lvm2 a--  <3.00g <3.00g
```

```bash
[root@localhost ~]# vgs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  VG    #PV #LV #SN Attr   VSize  VFree 
  anisa   4   0   0 wz--n- 19.98g 19.98g
```

```bash
[root@localhost ~]# vgscan
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  Found volume group "anisa" using metadata type lvm2
```

```bash
[root@localhost ~]# vgdisplay
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  --- Volume group ---
  VG Name               anisa
  System ID             
  Format                lvm2
  Metadata Areas        4
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                4
  Act PV                4
  VG Size               19.98 GiB
  PE Size               4.00 MiB
  Total PE              5116
  Alloc PE / Size       0 / 0   
  Free  PE / Size       5116 / 19.98 GiB
  VG UUID               Npw6Xr-r0v3-KWVT-zUTt-bfWy-abTo-IwhTJI
```

### 4- Create Logical Volumes on anisa Volume Group as follow:
      a. monitoring: 4GiB
      b. database: 7GiB
      c. backup: 9GiB

```bash
[root@localhost ~]# lvcreate -n monitoring -L 4g anisa
  Logical volume "monitoring" created.
```

```bash
[root@localhost ~]# pvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  PV         VG    Fmt  Attr PSize  PFree 
  /dev/sdb1  anisa lvm2 a--  <4.00g <4.00g
  /dev/sdb2  anisa lvm2 a--  <6.00g <2.00g
  /dev/sdc1  anisa lvm2 a--  <7.00g <7.00g
  /dev/sdc2  anisa lvm2 a--  <3.00g <3.00g
```

```bash
[root@localhost ~]# lvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  LV         VG    Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  monitoring anisa -wi-a----- 4.00g 
```

```bash
[root@localhost ~]# lsblk
NAME                 MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda                    8:0    0   20G  0 disk 
├─sda1                 8:1    0    1G  0 part /boot
└─sda2                 8:2    0   19G  0 part 
  ├─rl-root          253:0    0   17G  0 lvm  /
  └─rl-swap          253:1    0    2G  0 lvm  [SWAP]
sdb                    8:16   0   10G  0 disk 
├─sdb1                 8:17   0    4G  0 part 
└─sdb2                 8:18   0    6G  0 part 
  └─anisa-monitoring 253:2    0    4G  0 lvm  
sdc                    8:32   0   10G  0 disk 
├─sdc1                 8:33   0    7G  0 part 
└─sdc2                 8:34   0    3G  0 part 
sr0                   11:0    1 1024M  0 rom
```

```bash
[root@localhost ~]# lvcreate -n database -L 7g anisa
  Logical volume "database" created.
```

```bash
[root@localhost ~]# pvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  PV         VG    Fmt  Attr PSize  PFree 
  /dev/sdb1  anisa lvm2 a--  <4.00g  3.99g
  /dev/sdb2  anisa lvm2 a--  <6.00g <2.00g
  /dev/sdc1  anisa lvm2 a--  <7.00g     0 
  /dev/sdc2  anisa lvm2 a--  <3.00g <3.00g
```

```bash
[root@localhost ~]# vgs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  VG    #PV #LV #SN Attr   VSize  VFree
  anisa   4   2   0 wz--n- 19.98g 8.98g
```

```bash
[root@localhost ~]# lsblk
NAME                 MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda                    8:0    0   20G  0 disk 
├─sda1                 8:1    0    1G  0 part /boot
└─sda2                 8:2    0   19G  0 part 
  ├─rl-root          253:0    0   17G  0 lvm  /
  └─rl-swap          253:1    0    2G  0 lvm  [SWAP]
sdb                    8:16   0   10G  0 disk 
├─sdb1                 8:17   0    4G  0 part 
│ └─anisa-database   253:3    0    7G  0 lvm  
└─sdb2                 8:18   0    6G  0 part 
  └─anisa-monitoring 253:2    0    4G  0 lvm  
sdc                    8:32   0   10G  0 disk 
├─sdc1                 8:33   0    7G  0 part 
│ └─anisa-database   253:3    0    7G  0 lvm  
└─sdc2                 8:34   0    3G  0 part 
sr0                   11:0    1 1024M  0 rom 
```

```bash
[root@localhost ~]# lvcreate -n backup -L 9g anisa
  Volume group "anisa" has insufficient free space (2300 extents): 2304 required.
```

```bash
[root@localhost ~]# lvcreate -n backup -l 100%FREE anisa
  Logical volume "backup" created.
```

```bash
[root@localhost ~]# pvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  PV         VG    Fmt  Attr PSize  PFree
  /dev/sdb1  anisa lvm2 a--  <4.00g    0 
  /dev/sdb2  anisa lvm2 a--  <6.00g    0 
  /dev/sdc1  anisa lvm2 a--  <7.00g    0 
  /dev/sdc2  anisa lvm2 a--  <3.00g    0 
```

```bash
[root@localhost ~]# lvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  LV         VG    Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  backup     anisa -wi-a----- 8.98g                                                    
  database   anisa -wi-a----- 7.00g                                                    
  monitoring anisa -wi-a----- 4.00g
```

```bash
[root@localhost ~]# lsblk
NAME                 MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda                    8:0    0   20G  0 disk 
├─sda1                 8:1    0    1G  0 part /boot
└─sda2                 8:2    0   19G  0 part 
  ├─rl-root          253:0    0   17G  0 lvm  /
  └─rl-swap          253:1    0    2G  0 lvm  [SWAP]
sdb                    8:16   0   10G  0 disk 
├─sdb1                 8:17   0    4G  0 part 
│ ├─anisa-database   253:3    0    7G  0 lvm  
│ └─anisa-backup     253:4    0    9G  0 lvm  
└─sdb2                 8:18   0    6G  0 part 
  ├─anisa-monitoring 253:2    0    4G  0 lvm  
  └─anisa-backup     253:4    0    9G  0 lvm  
sdc                    8:32   0   10G  0 disk 
├─sdc1                 8:33   0    7G  0 part 
│ └─anisa-database   253:3    0    7G  0 lvm  
└─sdc2                 8:34   0    3G  0 part 
  └─anisa-backup     253:4    0    9G  0 lvm  
sr0                   11:0    1 1024M  0 rom
```

### 5- Locate logical volumes block device files

```bash
[root@localhost ~]# ll /dev/anisa/
total 0
lrwxrwxrwx 1 root root 7 Dec 15 23:52 backup -> ../dm-4
lrwxrwxrwx 1 root root 7 Dec 15 23:47 database -> ../dm-3
lrwxrwxrwx 1 root root 7 Dec 15 23:43 monitoring -> ../dm-2
```

```bash
[root@localhost ~]# ll /dev/mapper/anisa*
lrwxrwxrwx 1 root root 7 Dec 15 23:52 /dev/mapper/anisa-backup -> ../dm-4
lrwxrwxrwx 1 root root 7 Dec 15 23:47 /dev/mapper/anisa-database -> ../dm-3
lrwxrwxrwx 1 root root 7 Dec 15 23:43 /dev/mapper/anisa-monitoring -> ../dm-2
```

### 6- Format Logical Volumes with nessesary filesystem type:

```bash
[root@localhost ~]# mkfs -t ext4 /dev/anisa/monitoring 
mke2fs 1.46.5 (30-Dec-2021)
Creating filesystem with 1048576 4k blocks and 262144 inodes
Filesystem UUID: 3e6bacf6-45c0-4205-b8b4-f27f93a5c371
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done
```

```bash
[root@localhost ~]# mkfs -t ext4 /dev/anisa/database 
mke2fs 1.46.5 (30-Dec-2021)
Creating filesystem with 1835008 4k blocks and 458752 inodes
Filesystem UUID: 0e5da004-c3ff-4443-b8fd-b8e3cd04c7ed
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done
```

```bash
[root@localhost ~]# mkfs -t xfs /dev/anisa/backup 
meta-data=/dev/anisa/backup      isize=512    agcount=4, agsize=588800 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=1 inobtcount=1 nrext64=0
data     =                       bsize=4096   blocks=2355200, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=16384, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

### 7- Mount Logical Volumes to directory stracture:

```bash
[root@localhost ~]# mkdir /mnt/{monitoring,database,backup}
```

```bash
[root@localhost ~]# for i in monitoring database backup;do mount /dev/anisa/$i /mnt/$i;done
```

```bash
[root@localhost ~]# lsblk
NAME                 MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda                    8:0    0   20G  0 disk 
├─sda1                 8:1    0    1G  0 part /boot
└─sda2                 8:2    0   19G  0 part 
  ├─rl-root          253:0    0   17G  0 lvm  /
  └─rl-swap          253:1    0    2G  0 lvm  [SWAP]
sdb                    8:16   0   10G  0 disk 
├─sdb1                 8:17   0    4G  0 part 
│ ├─anisa-database   253:3    0    7G  0 lvm  /mnt/database
│ └─anisa-backup     253:4    0    9G  0 lvm  /mnt/backup
└─sdb2                 8:18   0    6G  0 part 
  ├─anisa-monitoring 253:2    0    4G  0 lvm  /mnt/monitoring
  └─anisa-backup     253:4    0    9G  0 lvm  /mnt/backup
sdc                    8:32   0   10G  0 disk 
├─sdc1                 8:33   0    7G  0 part 
│ └─anisa-database   253:3    0    7G  0 lvm  /mnt/database
└─sdc2                 8:34   0    3G  0 part 
  └─anisa-backup     253:4    0    9G  0 lvm  /mnt/backup
sr0                   11:0    1 1024M  0 rom 
```