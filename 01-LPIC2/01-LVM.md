
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

### 8- Write files in directories and use your LVM.
---

## Example 3: Manipulate LVM
### 1- Add another virtual Hard Drive to your Virtual Machine with 10 GiB Space.
### 2- Make following partitions using fdisk:
     a. sdd1 => 5 GiB
     b. sdd2 => 5 GiB

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
sdd                    8:48   0   10G  0 disk 
sr0                   11:0    1 1024M  0 rom
```

```bash
[root@localhost ~]# df -h
Filesystem           Size  Used Avail Use% Mounted on
devtmpfs             4.0M     0  4.0M   0% /dev
tmpfs                1.8G     0  1.8G   0% /dev/shm
tmpfs                730M  9.2M  721M   2% /run
/dev/mapper/rl-root   17G  5.7G   12G  34% /
/dev/sda1            960M  344M  617M  36% /boot
tmpfs                365M  100K  365M   1% /run/user/1000
```

```bash
[root@localhost ~]# vim /etc/fstab
#
# /etc/fstab
# Created by anaconda on Sat Dec 30 13:37:26 2023
#
# Accessible filesystems, by reference, are maintained under '/dev/disk/'.
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info.
#
# After editing this file, run 'systemctl daemon-reload' to update systemd
# units generated from this file.
#
/dev/mapper/rl-root     /                       xfs     defaults        0 0
UUID=d5da29d5-5f65-4d1e-90d8-74db210d5b8d /boot                   xfs     defaults        0 0
/dev/mapper/rl-swap     none                    swap    defaults        0 0
/dev/mapper/anisa-monitoring  /mnt/monitoring  ext4  defaults  0 0
/dev/mapper/anisa-database  /mnt/database  ext4  defaults  0  0
/dev/anisa/backup  /mnt/backup  xfs  defaults  0  0
```

```bash
[root@localhost ~]# mount -a
mount: (hint) your fstab has been modified, but systemd still uses
       the old version; use 'systemctl daemon-reload' to reload.
```

```bash
[root@localhost ~]# systemctl daemon-reload
```

```bash
[root@localhost ~]# mount -a
```

```bash
[root@localhost ~]# df -h
Filesystem                    Size  Used Avail Use% Mounted on
devtmpfs                      4.0M     0  4.0M   0% /dev
tmpfs                         1.8G     0  1.8G   0% /dev/shm
tmpfs                         730M  9.2M  721M   2% /run
/dev/mapper/rl-root            17G  5.7G   12G  34% /
/dev/sda1                     960M  344M  617M  36% /boot
tmpfs                         365M  100K  365M   1% /run/user/1000
/dev/mapper/anisa-monitoring  3.9G   24K  3.7G   1% /mnt/monitoring
/dev/mapper/anisa-database    6.8G   24K  6.5G   1% /mnt/database
/dev/mapper/anisa-backup      9.0G   96M  8.9G   2% /mnt/backup
```

```bash
[root@localhost ~]# fdisk /dev/sdd 

Welcome to fdisk (util-linux 2.37.4).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0xc55a217c.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-20971519, default 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-20971519, default 20971519): +5G

Created a new partition 1 of type 'Linux' and of size 5 GiB.

Command (m for help): p
Disk /dev/sdd: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xc55a217c

Device     Boot Start      End  Sectors Size Id Type
/dev/sdd1        2048 10487807 10485760   5G 83 Linux

Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (2-4, default 2): 2
First sector (10487808-20971519, default 10487808): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (10487808-20971519, default 20971519): 

Created a new partition 2 of type 'Linux' and of size 5 GiB.

Command (m for help): p
Disk /dev/sdd: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xc55a217c

Device     Boot    Start      End  Sectors Size Id Type
/dev/sdd1           2048 10487807 10485760   5G 83 Linux
/dev/sdd2       10487808 20971519 10483712   5G 83 Linux

Command (m for help): t
Partition number (1,2, default 2): 1
Hex code or alias (type L to list all): lvm

Changed type of partition 'Linux' to 'Linux LVM'.

Command (m for help): p
Disk /dev/sdd: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xc55a217c

Device     Boot    Start      End  Sectors Size Id Type
/dev/sdd1           2048 10487807 10485760   5G 8e Linux LVM
/dev/sdd2       10487808 20971519 10483712   5G 83 Linux

Command (m for help): t
Partition number (1,2, default 2): 2
Hex code or alias (type L to list all): lvm

Changed type of partition 'Linux' to 'Linux LVM'.

Command (m for help): p
Disk /dev/sdd: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xc55a217c

Device     Boot    Start      End  Sectors Size Id Type
/dev/sdd1           2048 10487807 10485760   5G 8e Linux LVM
/dev/sdd2       10487808 20971519 10483712   5G 8e Linux LVM

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

```bash
[root@localhost ~]# fdisk -l /dev/sdd
Disk /dev/sdd: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xc55a217c

Device     Boot    Start      End  Sectors Size Id Type
/dev/sdd1           2048 10487807 10485760   5G 8e Linux LVM
/dev/sdd2       10487808 20971519 10483712   5G 8e Linux LVM
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
sdd                    8:48   0   10G  0 disk 
├─sdd1                 8:49   0    5G  0 part 
└─sdd2                 8:50   0    5G  0 part 
sr0                   11:0    1 1024M  0 rom
```

### 3- Make physical volume on sdd1 and sdd2:

```bash
[root@localhost ~]# pvcreate /dev/sdd[12]
  Physical volume "/dev/sdd1" successfully created.
  Physical volume "/dev/sdd2" successfully created.
```

```bash
[root@localhost ~]# pvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  PV         VG    Fmt  Attr PSize  PFree 
  /dev/sdb1  anisa lvm2 a--  <4.00g     0 
  /dev/sdb2  anisa lvm2 a--  <6.00g     0 
  /dev/sdc1  anisa lvm2 a--  <7.00g     0 
  /dev/sdc2  anisa lvm2 a--  <3.00g     0 
  /dev/sdd1        lvm2 ---   5.00g  5.00g
  /dev/sdd2        lvm2 ---  <5.00g <5.00g
```

### 4- Remove physical volume /dev/sdd2

```bash
[root@localhost ~]# pvremove /dev/sdd2
  Labels on physical volume "/dev/sdd2" successfully wiped.
```

```bash
[root@localhost ~]# pvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  PV         VG    Fmt  Attr PSize  PFree
  /dev/sdb1  anisa lvm2 a--  <4.00g    0 
  /dev/sdb2  anisa lvm2 a--  <6.00g    0 
  /dev/sdc1  anisa lvm2 a--  <7.00g    0 
  /dev/sdc2  anisa lvm2 a--  <3.00g    0 
  /dev/sdd1        lvm2 ---   5.00g 5.00g
```

### 5- Add PV /dev/sdd1 to volume group anisa:

```bash
[root@localhost ~]# vgextend anisa /dev/sdd1
  Volume group "anisa" successfully extended
```

```bash
[root@localhost ~]# vgs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  VG    #PV #LV #SN Attr   VSize  VFree 
  anisa   5   3   0 wz--n- 24.98g <5.00g
```

```bash
[root@localhost ~]# pvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  PV         VG    Fmt  Attr PSize  PFree 
  /dev/sdb1  anisa lvm2 a--  <4.00g     0 
  /dev/sdb2  anisa lvm2 a--  <6.00g     0 
  /dev/sdc1  anisa lvm2 a--  <7.00g     0 
  /dev/sdc2  anisa lvm2 a--  <3.00g     0 
  /dev/sdd1  anisa lvm2 a--  <5.00g <5.00g
```

### 6- Remove PV /dev/sdd1 from volume group anisa:

```bash
[root@localhost ~]# vgreduce anisa /dev/sdd1
  Removed "/dev/sdd1" from volume group "anisa"
```

```bash
[root@localhost ~]# vgs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  VG    #PV #LV #SN Attr   VSize  VFree
  anisa   4   3   0 wz--n- 19.98g    0 
```

```bash
[root@localhost ~]# pvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  PV         VG    Fmt  Attr PSize  PFree
  /dev/sdb1  anisa lvm2 a--  <4.00g    0 
  /dev/sdb2  anisa lvm2 a--  <6.00g    0 
  /dev/sdc1  anisa lvm2 a--  <7.00g    0 
  /dev/sdc2  anisa lvm2 a--  <3.00g    0 
  /dev/sdd1        lvm2 ---   5.00g 5.00g
```

### 7- Add PV /dev/sdd1 to volume group anisa again:

```bash
[root@localhost ~]# vgextend anisa /dev/sdd1
  Volume group "anisa" successfully extended
```

```bash
[root@localhost ~]# vgs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  VG    #PV #LV #SN Attr   VSize  VFree 
  anisa   5   3   0 wz--n- 24.98g <5.00g
```

```bash
[root@localhost ~]# pvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  PV         VG    Fmt  Attr PSize  PFree 
  /dev/sdb1  anisa lvm2 a--  <4.00g     0 
  /dev/sdb2  anisa lvm2 a--  <6.00g     0 
  /dev/sdc1  anisa lvm2 a--  <7.00g     0 
  /dev/sdc2  anisa lvm2 a--  <3.00g     0 
  /dev/sdd1  anisa lvm2 a--  <5.00g <5.00g
```

### 8- Rename VG anisa to lpic2:

```bash
[root@localhost ~]# ls -l /dev/mapper/anisa-*
lrwxrwxrwx 1 root root 7 Dec 16 20:51 /dev/mapper/anisa-backup -> ../dm-4
lrwxrwxrwx 1 root root 7 Dec 16 20:51 /dev/mapper/anisa-database -> ../dm-3
lrwxrwxrwx 1 root root 7 Dec 16 20:51 /dev/mapper/anisa-monitoring -> ../dm-2
```

```bash
[root@localhost ~]# ls -l /dev/anisa
total 0
lrwxrwxrwx 1 root root 7 Dec 16 20:51 backup -> ../dm-4
lrwxrwxrwx 1 root root 7 Dec 16 20:51 database -> ../dm-3
lrwxrwxrwx 1 root root 7 Dec 16 20:51 monitoring -> ../dm-2
```

```bash
[root@localhost ~]# vgrename anisa lpic2
  Volume group "anisa" successfully renamed to "lpic2"
```

```bash
[root@localhost ~]# ls -l /dev/mapper/anisa*
ls: cannot access '/dev/mapper/anisa*': No such file or directory
```

```bash
[root@localhost ~]# ls -l /dev/anisa/
ls: cannot access '/dev/anisa/': No such file or directory
```

```bash
[root@localhost ~]# ls -l /dev/mapper/lpic2-*
lrwxrwxrwx 1 root root 7 Dec 16 21:53 /dev/mapper/lpic2-backup -> ../dm-4
lrwxrwxrwx 1 root root 7 Dec 16 21:53 /dev/mapper/lpic2-database -> ../dm-3
lrwxrwxrwx 1 root root 7 Dec 16 21:53 /dev/mapper/lpic2-monitoring -> ../dm-2
```

```bash
[root@localhost ~]# ls -l /dev/lpic2/*
lrwxrwxrwx 1 root root 7 Dec 16 21:53 /dev/lpic2/backup -> ../dm-4
lrwxrwxrwx 1 root root 7 Dec 16 21:53 /dev/lpic2/database -> ../dm-3
lrwxrwxrwx 1 root root 7 Dec 16 21:53 /dev/lpic2/monitoring -> ../dm-2
```

### 9- Rename VG lpic2 to anisa:

```bash
[root@localhost ~]# vgrename lpic2 anisa
  Volume group "lpic2" successfully renamed to "anisa"
```

```bash
[root@localhost ~]# df -h
Filesystem           Size  Used Avail Use% Mounted on
devtmpfs             4.0M     0  4.0M   0% /dev
tmpfs                1.8G     0  1.8G   0% /dev/shm
tmpfs                730M  9.3M  721M   2% /run
/dev/mapper/rl-root   17G  5.7G   12G  34% /
/dev/sda1            960M  344M  617M  36% /boot
tmpfs                365M  104K  365M   1% /run/user/1000
```

```bash
[root@localhost ~]# mount -a
```

```bash
[root@localhost ~]# df -h
Filesystem                    Size  Used Avail Use% Mounted on
devtmpfs                      4.0M     0  4.0M   0% /dev
tmpfs                         1.8G     0  1.8G   0% /dev/shm
tmpfs                         730M  9.3M  721M   2% /run
/dev/mapper/rl-root            17G  5.7G   12G  34% /
/dev/sda1                     960M  344M  617M  36% /boot
tmpfs                         365M  104K  365M   1% /run/user/1000
/dev/mapper/anisa-monitoring  3.9G   24K  3.7G   1% /mnt/monitoring
/dev/mapper/anisa-database    6.8G   24K  6.5G   1% /mnt/database
/dev/mapper/anisa-backup      9.0G   96M  8.9G   2% /mnt/backup
```

---
## Stages for Extending LVs:
### 1- [optional] unmount LV => umount
### 2- [optional] deactivate LV => lvchange
### 3- Extend size for LV => lvextend, lvresize
### 4- [optional] activate LV => lvchange
### 5- Resize the filesystem => depends on filesystem
### 6- [optional] mount => mount

### 1- Prepare LV to change (Deactivating) → Recommended but not nessessary!

```bash
[root@localhost ~]# umount /dev/mapper/anisa-monitoring
```

```bash
[root@localhost ~]# lvchange -an /dev/anisa/monitoring
```

```bash
[root@localhost ~]# lvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  LV         VG    Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  backup     anisa -wi-ao---- 8.98g                                                    
  database   anisa -wi-ao---- 7.00g                                                    
  monitoring anisa -wi------- 4.00g
```

### 2- Extend LV monitoring extra 2 GiB:

```bash
[root@localhost ~]# lvextend -L +2G /dev/anisa/monitoring 
  Size of logical volume anisa/monitoring changed from 4.00 GiB (1024 extents) to 6.00 GiB (1536 extents).
  Logical volume anisa/monitoring successfully resized.
```

```bash
[root@localhost ~]# lvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  LV         VG    Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  backup     anisa -wi-ao---- 8.98g                                                    
  database   anisa -wi-ao---- 7.00g                                                    
  monitoring anisa -wi------- 6.00g
  ```

```bash
[root@localhost ~]# pvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  PV         VG    Fmt  Attr PSize  PFree 
  /dev/sdb1  anisa lvm2 a--  <4.00g     0 
  /dev/sdb2  anisa lvm2 a--  <6.00g     0 
  /dev/sdc1  anisa lvm2 a--  <7.00g     0 
  /dev/sdc2  anisa lvm2 a--  <3.00g     0 
  /dev/sdd1  anisa lvm2 a--  <5.00g <3.00g
```

```bash
[root@localhost ~]# lvchange -ay /dev/anisa/monitoring
```

```bash
[root@localhost ~]# lvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  LV         VG    Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  backup     anisa -wi-ao---- 8.98g                                                    
  database   anisa -wi-ao---- 7.00g                                                    
  monitoring anisa -wi-a----- 6.00g
```

### 3- Resize LV database to 10 GiB:

```bash
[root@localhost ~]# lvresize -l +100%FREE /dev/anisa/database
  Size of logical volume anisa/database changed from 7.00 GiB (1792 extents) to <10.00 GiB (2559 extents).
  Logical volume anisa/database successfully resized.
```

```bash
[root@localhost ~]# lvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  LV         VG    Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  backup     anisa -wi-ao----   8.98g                                                    
  database   anisa -wi-ao---- <10.00g                                                    
  monitoring anisa -wi-a-----   6.00g
  ```

```bash
[root@localhost ~]# pvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  PV         VG    Fmt  Attr PSize  PFree
  /dev/sdb1  anisa lvm2 a--  <4.00g    0 
  /dev/sdb2  anisa lvm2 a--  <6.00g    0 
  /dev/sdc1  anisa lvm2 a--  <7.00g    0 
  /dev/sdc2  anisa lvm2 a--  <3.00g    0 
  /dev/sdd1  anisa lvm2 a--  <5.00g    0
```

```bash
[root@localhost ~]# df -h
Filesystem                  Size  Used Avail Use% Mounted on
devtmpfs                    4.0M     0  4.0M   0% /dev
tmpfs                       1.8G     0  1.8G   0% /dev/shm
tmpfs                       730M  9.3M  721M   2% /run
/dev/mapper/rl-root          17G  5.7G   12G  34% /
/dev/sda1                   960M  344M  617M  36% /boot
tmpfs                       365M  104K  365M   1% /run/user/1000
/dev/mapper/anisa-database  6.8G   24K  6.5G   1% /mnt/database
/dev/mapper/anisa-backup    9.0G   96M  8.9G   2% /mnt/backup
```

```bash
[root@localhost ~]# mount -a
```

```bash
[root@localhost ~]# df -hT
Filesystem                   Type      Size  Used Avail Use% Mounted on
devtmpfs                     devtmpfs  4.0M     0  4.0M   0% /dev
tmpfs                        tmpfs     1.8G     0  1.8G   0% /dev/shm
tmpfs                        tmpfs     730M  9.2M  721M   2% /run
/dev/mapper/rl-root          xfs        17G  5.7G   12G  34% /
/dev/sda1                    xfs       960M  344M  617M  36% /boot
tmpfs                        tmpfs     365M  104K  365M   1% /run/user/1000
/dev/mapper/anisa-database   ext4      6.8G   24K  6.5G   1% /mnt/database
/dev/mapper/anisa-backup     xfs       9.0G   96M  8.9G   2% /mnt/backup
/dev/mapper/anisa-monitoring ext4      3.9G   24K  3.7G   1% /mnt/monitoring
```

### (* We notice that the filesystem size is not chnaged on /dev/anisa/monitoring and /dev/anisa/database)

```bash
[root@localhost ~]# resize2fs /dev/anisa/monitoring 
resize2fs 1.46.5 (30-Dec-2021)
Filesystem at /dev/anisa/monitoring is mounted on /mnt/monitoring; on-line resizing required
old_desc_blocks = 1, new_desc_blocks = 1
The filesystem on /dev/anisa/monitoring is now 1572864 (4k) blocks long.
```

```bash
[root@localhost ~]# resize2fs /dev/anisa/database 
resize2fs 1.46.5 (30-Dec-2021)
Filesystem at /dev/anisa/database is mounted on /mnt/database; on-line resizing required
old_desc_blocks = 1, new_desc_blocks = 2
The filesystem on /dev/anisa/database is now 2620416 (4k) blocks long.
```

```bash
[root@localhost ~]# df -hT
Filesystem                   Type      Size  Used Avail Use% Mounted on
devtmpfs                     devtmpfs  4.0M     0  4.0M   0% /dev
tmpfs                        tmpfs     1.8G     0  1.8G   0% /dev/shm
tmpfs                        tmpfs     730M  9.2M  721M   2% /run
/dev/mapper/rl-root          xfs        17G  5.7G   12G  34% /
/dev/sda1                    xfs       960M  344M  617M  36% /boot
tmpfs                        tmpfs     365M  104K  365M   1% /run/user/1000
/dev/mapper/anisa-database   ext4      9.8G   24K  9.3G   1% /mnt/database
/dev/mapper/anisa-backup     xfs       9.0G   96M  8.9G   2% /mnt/backup
/dev/mapper/anisa-monitoring ext4      5.9G   24K  5.6G   1% /mnt/monitoring
```

---
## Reducing LVs:
### 1- unmount LV => umount
### 2- check filesystem => fsck
### 3- Resize filesystem => depend on filesystem
### 4- Reduce LV => lvreduce / lvresize
### 5- mount LV => mount

## Example 4: Remove and Reduce LVM
### * On reducing we have to do all extending actions vice versa 
### ** Reducing can not be done without outage.

### 1- Unmount and do filesystem check the logical volume monitoring:

```bash
[root@localhost ~]# umount /dev/anisa/monitoring
```

```bash
[root@localhost ~]# echo $?
0
```

```bash
[root@localhost ~]# e2fsck -f /dev/anisa/monitoring
e2fsck 1.46.5 (30-Dec-2021)
Pass 1: Checking inodes, blocks, and sizes
Pass 2: Checking directory structure
Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information
/dev/anisa/monitoring: 11/393216 files (0.0% non-contiguous), 45166/1572864 blocks
```

```bash
[root@localhost ~]# echo $?
0
```

### 2- Shrink the Logical Volume Block file (reduce 4 GiB):

```bash
[root@localhost ~]# resize2fs -p /dev/anisa/monitoring 5G
resize2fs 1.46.5 (30-Dec-2021)
Resizing the filesystem on /dev/anisa/monitoring to 1310720 (4k) blocks.
Begin pass 3 (max = 48)
Scanning inode table          XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
The filesystem on /dev/anisa/monitoring is now 1310720 (4k) blocks long.
```

### 3- Reduce Logical Volume /dev/anisa/monitoring
```bash
[root@localhost ~]# lvreduce -L 5G /dev/anisa/monitoring 
  File system ext4 found on anisa/monitoring.
  File system size (5.00 GiB) is equal to the requested size (5.00 GiB).
  File system reduce is not needed, skipping.
  Size of logical volume anisa/monitoring changed from 6.00 GiB (1536 extents) to 5.00 GiB (1280 extents).
  Logical volume anisa/monitoring successfully resized.
```

```bash
[root@localhost ~]# mount -a
```

```bash
[root@localhost ~]# df -h
Filesystem                    Size  Used Avail Use% Mounted on
devtmpfs                      4.0M     0  4.0M   0% /dev
tmpfs                         1.8G     0  1.8G   0% /dev/shm
tmpfs                         730M  9.3M  721M   2% /run
/dev/mapper/rl-root            17G  5.7G   12G  34% /
/dev/mapper/anisa-backup      9.0G   96M  8.9G   2% /mnt/backup
/dev/sda1                     960M  344M  617M  36% /boot
/dev/mapper/anisa-database    9.8G   24K  9.3G   1% /mnt/database
tmpfs                         365M  100K  365M   1% /run/user/1000
/dev/mapper/anisa-monitoring  4.9G   24K  4.6G   1% /mnt/monitoring
```

```bash
[root@localhost ~]# lvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  LV         VG    Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  backup     anisa -wi-ao----   8.98g                                                    
  database   anisa -wi-ao---- <10.00g                                                    
  monitoring anisa -wi-ao----   5.00g 
```

```bash
[root@localhost ~]# pvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  PV         VG    Fmt  Attr PSize  PFree
  /dev/sdb1  anisa lvm2 a--  <4.00g    0 
  /dev/sdb2  anisa lvm2 a--  <6.00g    0 
  /dev/sdc1  anisa lvm2 a--  <7.00g    0 
  /dev/sdc2  anisa lvm2 a--  <3.00g    0 
  /dev/sdd1  anisa lvm2 a--  <5.00g 1.00g
```

```bash
[root@localhost ~]# vgs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  VG    #PV #LV #SN Attr   VSize  VFree
  anisa   5   3   0 wz--n- 24.98g 1.00g
```

## Example 5: Remove a disk

### 1- Check the Physical Volumes:

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
│ ├─anisa-database   253:3    0   10G  0 lvm  /mnt/database
│ └─anisa-backup     253:4    0    9G  0 lvm  /mnt/backup
└─sdb2                 8:18   0    6G  0 part 
  ├─anisa-monitoring 253:2    0    5G  0 lvm  /mnt/monitoring
  └─anisa-backup     253:4    0    9G  0 lvm  /mnt/backup
sdc                    8:32   0   10G  0 disk 
├─sdc1                 8:33   0    7G  0 part 
│ └─anisa-database   253:3    0   10G  0 lvm  /mnt/database
└─sdc2                 8:34   0    3G  0 part 
  └─anisa-backup     253:4    0    9G  0 lvm  /mnt/backup
sdd                    8:48   0   10G  0 disk 
├─sdd1                 8:49   0    5G  0 part 
│ ├─anisa-monitoring 253:2    0    5G  0 lvm  /mnt/monitoring
│ └─anisa-database   253:3    0   10G  0 lvm  /mnt/database
└─sdd2                 8:50   0    5G  0 part 
sr0                   11:0    1 1024M  0 rom 
```

```bash
[root@localhost ~]# ls /mnt/database/
lost+found
```

```bash
[root@localhost ~]# echo test > /mnt/database/file1
```

```bash
[root@localhost ~]# pvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  PV         VG    Fmt  Attr PSize  PFree
  /dev/sdb1  anisa lvm2 a--  <4.00g    0 
  /dev/sdb2  anisa lvm2 a--  <6.00g    0 
  /dev/sdc1  anisa lvm2 a--  <7.00g    0 
  /dev/sdc2  anisa lvm2 a--  <3.00g    0 
  /dev/sdd1  anisa lvm2 a--  <5.00g 1.00g
```

```bash
[root@localhost ~]# pvcreate /dev/sdd2
  Physical volume "/dev/sdd2" successfully created.
```

```bash
[root@localhost ~]# pvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  PV         VG    Fmt  Attr PSize  PFree 
  /dev/sdb1  anisa lvm2 a--  <4.00g     0 
  /dev/sdb2  anisa lvm2 a--  <6.00g     0 
  /dev/sdc1  anisa lvm2 a--  <7.00g     0 
  /dev/sdc2  anisa lvm2 a--  <3.00g     0 
  /dev/sdd1  anisa lvm2 a--  <5.00g  1.00g
  /dev/sdd2        lvm2 ---  <5.00g <5.00g
```

```bash
[root@localhost ~]# vgextend anisa /dev/sdd2
  Volume group "anisa" successfully extended
```

```bash
[root@localhost ~]# pvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  PV         VG    Fmt  Attr PSize  PFree 
  /dev/sdb1  anisa lvm2 a--  <4.00g     0 
  /dev/sdb2  anisa lvm2 a--  <6.00g     0 
  /dev/sdc1  anisa lvm2 a--  <7.00g     0 
  /dev/sdc2  anisa lvm2 a--  <3.00g     0 
  /dev/sdd1  anisa lvm2 a--  <5.00g  1.00g
  /dev/sdd2  anisa lvm2 a--  <5.00g <5.00g
```

```bash
[root@localhost ~]# vgs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  VG    #PV #LV #SN Attr   VSize   VFree 
  anisa   6   3   0 wz--n- <29.98g <6.00g
```

### 2- Move extends to get free extends on first PV:

```bash
[root@localhost ~]# pvmove /dev/sdb1
  /dev/sdb1: Moved: 0.10%
  /dev/sdb1: Moved: 25.02%
  /dev/sdb1: Moved: 100.00%
```

```bash
[root@localhost ~]# pvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  PV         VG    Fmt  Attr PSize  PFree 
  /dev/sdb1  anisa lvm2 a--  <4.00g <4.00g
  /dev/sdb2  anisa lvm2 a--  <6.00g     0 
  /dev/sdc1  anisa lvm2 a--  <7.00g     0 
  /dev/sdc2  anisa lvm2 a--  <3.00g     0 
  /dev/sdd1  anisa lvm2 a--  <5.00g     0 
  /dev/sdd2  anisa lvm2 a--  <5.00g  2.00g
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
  ├─anisa-monitoring 253:2    0    5G  0 lvm  /mnt/monitoring
  └─anisa-backup     253:4    0    9G  0 lvm  /mnt/backup
sdc                    8:32   0   10G  0 disk 
├─sdc1                 8:33   0    7G  0 part 
│ └─anisa-database   253:3    0   10G  0 lvm  /mnt/database
└─sdc2                 8:34   0    3G  0 part 
  └─anisa-backup     253:4    0    9G  0 lvm  /mnt/backup
sdd                    8:48   0   10G  0 disk 
├─sdd1                 8:49   0    5G  0 part 
│ ├─anisa-monitoring 253:2    0    5G  0 lvm  /mnt/monitoring
│ ├─anisa-database   253:3    0   10G  0 lvm  /mnt/database
│ └─anisa-backup     253:4    0    9G  0 lvm  /mnt/backup
└─sdd2                 8:50   0    5G  0 part 
  └─anisa-backup     253:4    0    9G  0 lvm  /mnt/backup
sr0                   11:0    1 1024M  0 rom
```

### 3- Remove PV sdb1

```bash
[root@localhost ~]# vgreduce anisa /dev/sdb1
  Removed "/dev/sdb1" from volume group "anisa"
```

```bash
[root@localhost ~]# vgs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  VG    #PV #LV #SN Attr   VSize  VFree
  anisa   5   3   0 wz--n- 25.98g 2.00g
```

```bash
[root@localhost ~]# pvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  PV         VG    Fmt  Attr PSize  PFree
  /dev/sdb1        lvm2 ---   4.00g 4.00g
  /dev/sdb2  anisa lvm2 a--  <6.00g    0 
  /dev/sdc1  anisa lvm2 a--  <7.00g    0 
  /dev/sdc2  anisa lvm2 a--  <3.00g    0 
  /dev/sdd1  anisa lvm2 a--  <5.00g    0 
  /dev/sdd2  anisa lvm2 a--  <5.00g 2.00g
```

```bash
[root@localhost ~]# pvremove /dev/sdb1
  Labels on physical volume "/dev/sdb1" successfully wiped.
```

```bash
[root@localhost ~]# pvs
  Devices file sys_wwid t10.ATA_VBOX_HARDDISK_VBabb061c0-806b36a9 PVID vmaiA1d5KMNbia8sdTaaE6yFw02Adxyf last seen on /dev/sda2 not found.
  PV         VG    Fmt  Attr PSize  PFree
  /dev/sdb2  anisa lvm2 a--  <6.00g    0 
  /dev/sdc1  anisa lvm2 a--  <7.00g    0 
  /dev/sdc2  anisa lvm2 a--  <3.00g    0 
  /dev/sdd1  anisa lvm2 a--  <5.00g    0 
  /dev/sdd2  anisa lvm2 a--  <5.00g 2.00g
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
  ├─anisa-monitoring 253:2    0    5G  0 lvm  /mnt/monitoring
  └─anisa-backup     253:4    0    9G  0 lvm  /mnt/backup
sdc                    8:32   0   10G  0 disk 
├─sdc1                 8:33   0    7G  0 part 
│ └─anisa-database   253:3    0   10G  0 lvm  /mnt/database
└─sdc2                 8:34   0    3G  0 part 
  └─anisa-backup     253:4    0    9G  0 lvm  /mnt/backup
sdd                    8:48   0   10G  0 disk 
├─sdd1                 8:49   0    5G  0 part 
│ ├─anisa-monitoring 253:2    0    5G  0 lvm  /mnt/monitoring
│ ├─anisa-database   253:3    0   10G  0 lvm  /mnt/database
│ └─anisa-backup     253:4    0    9G  0 lvm  /mnt/backup
└─sdd2                 8:50   0    5G  0 part 
  └─anisa-backup     253:4    0    9G  0 lvm  /mnt/backup
sr0                   11:0    1 1024M  0 rom
```