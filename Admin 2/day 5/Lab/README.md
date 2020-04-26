# Day :five: Lab:

### LVM:

28. Use the fdisk command to create 2 Linux LVM (0x8e) partitions using "unpartitioned" space on your hard disk. These partitions should all be the same size; to speed up the lab, do not make them
larger than 300 MB each. Make sure to write the changes to disk by using the w command to exit the fdisk utility. Run the partprobe command after exiting the fdisk utility.

using virtualbox 2 partitions each of size 300 MB were added to the vagrant instance

<details>
<summary>lsblk</summary>
<p> <img src=""> </p>
</details>

29. Initialize your Linux LVM partitions as physical volumes with the `pvcreate` command. You can use the pvdisplay command to verify that the partitions have been initialized as physical volumes.

```bash
sudo yum install -y lvm2

[vagrant@CentOS-7 ~]$ sudo pvcreate /dev/sdb
  Physical volume "/dev/sdb" successfully created.
[vagrant@CentOS-7 ~]$ sudo pvcreate /dev/sdc
  Physical volume "/dev/sdc" successfully created.

[vagrant@CentOS-7 ~]$ sudo pvdisplay 
  "/dev/sdb" is a new physical volume of "300.00 MiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb
  VG Name               
  PV Size               300.00 MiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               yaT5M4-yZs6-oh2G-NfVd-J7rU-sJWt-YtOCGG
   
  "/dev/sdc" is a new physical volume of "300.00 MiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdc
  VG Name               
  PV Size               300.00 MiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               u8RKi1-fqAJ-KHde-lTHO-5fw0-OcOx-WaIxBU
```

30. Using only one of your physical volumes, create a `volume group` called test0. Use the vgdisplay command to verify that the volume group was created.

```bash
[vagrant@CentOS-7 ~]$ sudo vgcreate test0 /dev/sdb
  Volume group "test0" successfully created

[vagrant@CentOS-7 ~]$ sudo vgdisplay 
  --- Volume group ---
  VG Name               test0
  System ID             
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               296.00 MiB
  PE Size               4.00 MiB
  Total PE              74
  Alloc PE / Size       0 / 0   
  Free  PE / Size       74 / 296.00 MiB
  VG UUID               jewuIL-llBa-rRPj-XC08-Q5st-APKx-mvchn0
```

32. Create a small logical volume (LV) called data that uses about 30 percent of the available space of the test0 volume group. Look for VG Size and Free PE/Size in the output of the vgdisplay command to assist you with this. Use the lvdisplay command to verify your work.

```bash
[vagrant@CentOS-7 ~]$ sudo lvcreate -n data -L 90M test0
  Rounding up size to full physical extent 92.00 MiB
  Logical volume "data" created.
[vagrant@CentOS-7 ~]$ sudo lvdisplay 
  --- Logical volume ---
  LV Path                /dev/test0/data
  LV Name                data
  VG Name                test0
  LV UUID                6G8w7q-q7XE-74Sw-HmMJ-DvBT-L8ZG-rc13ME
  LV Write Access        read/write
  LV Creation host, time CentOS-7, 2020-04-26 19:58:05 +0000
  LV Status              available
  # open                 0
  LV Size                92.00 MiB
  Current LE             23
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:0
```

33. Create an ext2 filesystem on your new LV.
```bash
root@CentOS-7 test0]# cd /dev/test0/
[root@CentOS-7 test0]# mkfs.ext2 data 
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=1024 (log=0)
Fragment size=1024 (log=0)
Stride=0 blocks, Stripe width=0 blocks
23616 inodes, 94208 blocks
4710 blocks (5.00%) reserved for the super user
First data block=1
Maximum filesystem blocks=67371008
12 block groups
8192 blocks per group, 8192 fragments per group
1968 inodes per group
Superblock backups stored on blocks: 
	8193, 24577, 40961, 57345, 73729

Allocating group tables: done                            
Writing inode tables: done                            
Writing superblocks and filesystem accounting information: done 
```

34. Make a new directory called /data and then mount the new LV under the /data directory. Create a "large file" in this volume.

```bash
[root@CentOS-7 test0]# mkdir /data
[root@CentOS-7 test0]# mount /dev/test0/data /data/
```

35. Enlarge the LV that you created in Sequence 1 (/dev/test0/data) by using approximately 25 percent of the remaining free space in the test0 volume group. Then, use the ext2online command to enlarge the filesystem of the LV.
```bash
[root@CentOS-7 test0]# sudo lvextend -L +51M /dev/test0/data 
  Rounding size to boundary between physical extents: 52.00 MiB.
  Size of logical volume test0/data changed from 92.00 MiB (23 extents) to 144.00 MiB (36 extents).
  Logical volume test0/data successfully resized.
[root@CentOS-7 test0]# lvs
  LV   VG    Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  data test0 -wi-ao---- 144.00m                                                    

```

37. Use the remaining extents in the test0 volume group to create a second LV called docs.
```bash
[root@CentOS-7 test0]# sudo lvcreate -n docs -L 152M test0 
  Logical volume "docs" created.
```

38. Run the vgdisplay command to verify that there are no free extents left in the test0 volume group.
```bash
[root@CentOS-7 test0]# vgdisplay 
  --- Volume group ---
  VG Name               test0
  System ID             
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  4
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               1
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               296.00 MiB
  PE Size               4.00 MiB
  Total PE              74
  Alloc PE / Size       74 / 296.00 MiB
  Free  PE / Size       0 / 0   
  VG UUID               jewuIL-llBa-rRPj-XC08-Q5st-APKx-mvchn0
```

A better looking command that achieves the same result
```bash
[root@CentOS-7 test0]# vgs
  VG    #PV #LV #SN Attr   VSize   VFree
  test0   1   2   0 wz--n- 296.00m    0 
```

39. Create an ext2 filesystem on the new LV, make a mount point called /docs and mount the docs LV using this mount point.
```bash
[root@CentOS-7 test0]# cd /dev/test0/
[root@CentOS-7 test0]# mkfs.ext2 docs 
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=1024 (log=0)
Fragment size=1024 (log=0)
Stride=0 blocks, Stripe width=0 blocks
38912 inodes, 155648 blocks
7782 blocks (5.00%) reserved for the super user
First data block=1
Maximum filesystem blocks=67371008
19 block groups
8192 blocks per group, 8192 fragments per group
2048 inodes per group
Superblock backups stored on blocks: 
	8193, 24577, 40961, 57345, 73729

Allocating group tables: done                            
Writing inode tables: done                            
Writing superblocks and filesystem accounting information: done 
[root@CentOS-7 test0]# mkdir /docs 
[root@CentOS-7 test0]# mount /dev/test0/docs /docs/
```

40. Add all of the remaining unused physical volumes that you created in Sequence 1 to the test0 volume group.
```bash
[root@CentOS-7 test0]# vgextend test0 /dev/sdc 
  Volume group "test0" successfully extended
[root@CentOS-7 test0]# vgs
  VG    #PV #LV #SN Attr   VSize   VFree  
  test0   2   2   0 wz--n- 592.00m 296.00m
```

41. If you run vgdisplay again, there now should be free extents (provided by the new physical
volumes) in the test0 volume group. Extend the docs LV and underlying filesystem to make use of all
of the free extents of the test0 volume group. Verify your actions.

```bash
[root@CentOS-7 test0]# lvextend -l +74 /dev/test0/docs 
  Size of logical volume test0/docs changed from 152.00 MiB (38 extents) to 448.00 MiB (112 extents).
  Logical volume test0/docs successfully resized.

[root@CentOS-7 test0]# vgdisplay 
  --- Volume group ---
  VG Name               test0
  System ID             
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  6
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               592.00 MiB
  PE Size               4.00 MiB
  Total PE              148
  Alloc PE / Size       148 / 592.00 MiB
  Free  PE / Size       0 / 0   
  VG UUID               jewuIL-llBa-rRPj-XC08-Q5st-APKx-mvchn0

```

Before moving on to the RAID sequence, disassemble your LVM-managed volumes by taking the following actions:
Remove any /etc/fstab entries you created.
umount /dev/test0/data
lvremove /dev/test0/data
umount /dev/test0/docs
lvremove /dev/test0/docs
vgchange -an test0 (this deactivates the volume group)
vgremove test0
(this deletes the volume group)
```bash
root@CentOS-7 test0]# umount /dev/test0/data 
[root@CentOS-7 test0]# umount /dev/test0/docs 
[root@CentOS-7 test0]# lvremove /dev/test0/docs 
Do you really want to remove active logical volume test0/docs? [y/n]: y
  Logical volume "docs" successfully removed
[root@CentOS-7 test0]# lvremove /dev/test0/data 
Do you really want to remove active logical volume test0/data? [y/n]: y
  Logical volume "data" successfully removed
[root@CentOS-7 test0]# vgchange -an test0
  0 logical volume(s) in volume group "test0" now active
[root@CentOS-7 test0]# vgremove test0 
  Volume group "test0" successfully removed
[root@CentOS-7 test0]# 
```

42. Run the fdisk command and convert the Linux LVM (0x8e) partitions that were created in above into Linux raid auto (0xfd) partitions. Save your changes and run the partprobe command.
```bash
[root@CentOS-7 test0]# fdisk /dev/sdb 
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table
Building a new DOS disklabel with disk identifier 0xd9799b6b.

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): 
Using default response p
Partition number (1-4, default 1): 
First sector (2048-614399, default 2048): 
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-614399, default 614399): 
Using default value 614399
Partition 1 of type Linux and of size 299 MiB is set

Command (m for help): t
Selected partition 1
Hex code (type L to list all codes): L

 0  Empty           24  NEC DOS         81  Minix / old Lin bf  Solaris        
 1  FAT12           27  Hidden NTFS Win 82  Linux swap / So c1  DRDOS/sec (FAT-
 2  XENIX root      39  Plan 9          83  Linux           c4  DRDOS/sec (FAT-
 3  XENIX usr       3c  PartitionMagic  84  OS/2 hidden C:  c6  DRDOS/sec (FAT-
 4  FAT16 <32M      40  Venix 80286     85  Linux extended  c7  Syrinx         
 5  Extended        41  PPC PReP Boot   86  NTFS volume set da  Non-FS data    
 6  FAT16           42  SFS             87  NTFS volume set db  CP/M / CTOS / .
 7  HPFS/NTFS/exFAT 4d  QNX4.x          88  Linux plaintext de  Dell Utility   
 8  AIX             4e  QNX4.x 2nd part 8e  Linux LVM       df  BootIt         
 9  AIX bootable    4f  QNX4.x 3rd part 93  Amoeba          e1  DOS access     
 a  OS/2 Boot Manag 50  OnTrack DM      94  Amoeba BBT      e3  DOS R/O        
 b  W95 FAT32       51  OnTrack DM6 Aux 9f  BSD/OS          e4  SpeedStor      
 c  W95 FAT32 (LBA) 52  CP/M            a0  IBM Thinkpad hi eb  BeOS fs        
 e  W95 FAT16 (LBA) 53  OnTrack DM6 Aux a5  FreeBSD         ee  GPT            
 f  W95 Ext'd (LBA) 54  OnTrackDM6      a6  OpenBSD         ef  EFI (FAT-12/16/
10  OPUS            55  EZ-Drive        a7  NeXTSTEP        f0  Linux/PA-RISC b
11  Hidden FAT12    56  Golden Bow      a8  Darwin UFS      f1  SpeedStor      
12  Compaq diagnost 5c  Priam Edisk     a9  NetBSD          f4  SpeedStor      
14  Hidden FAT16 <3 61  SpeedStor       ab  Darwin boot     f2  DOS secondary  
16  Hidden FAT16    63  GNU HURD or Sys af  HFS / HFS+      fb  VMware VMFS    
17  Hidden HPFS/NTF 64  Novell Netware  b7  BSDI fs         fc  VMware VMKCORE 
18  AST SmartSleep  65  Novell Netware  b8  BSDI swap       fd  Linux raid auto
1b  Hidden W95 FAT3 70  DiskSecure Mult bb  Boot Wizard hid fe  LANstep        
1c  Hidden W95 FAT3 75  PC/IX           be  Solaris boot    ff  BBT            
1e  Hidden W95 FAT1 80  Old Minix      
Hex code (type L to list all codes): fd
Changed type of partition 'Linux' to 'Linux raid autodetect'

Command (m for help): w   
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
[root@CentOS-7 test0]# partprobe 
```

43. Initialize your RAID array (RAID 0).
```bash
[root@CentOS-7 test0]# mdadm --create -v --level=0 --raid-devices=2 /dev/md0 /dev/sdb1 /dev/sdc1 
mdadm: chunk size defaults to 512K
mdadm: /dev/sdb1 appears to contain an ext2fs file system
       size=94208K  mtime=Sun Apr 26 20:51:50 2020
Continue creating array? y
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.
```

44. Format the RAID device with an ext3 filesystem.
```bash
[root@CentOS-7 test0]# mkfs.ext3 /dev/md0 
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=128 blocks, Stripe width=256 blocks
38080 inodes, 152064 blocks
7603 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=159383552
5 block groups
32768 blocks per group, 32768 fragments per group
7616 inodes per group
Superblock backups stored on blocks: 
	32768, 98304

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (4096 blocks): done
Writing superblocks and filesystem accounting information: done

```

45. Use the /data directory as a mount point for the /dev/md0 RAID device. Use the df command to check the size of the filesystem.

```bash
[root@CentOS-7 test0]# mount /dev/md0 /data
[root@CentOS-7 test0]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        40G  3.0G   37G   8% /
devtmpfs        236M     0  236M   0% /dev
tmpfs           244M     0  244M   0% /dev/shm
tmpfs           244M  4.5M  240M   2% /run
tmpfs           244M     0  244M   0% /sys/fs/cgroup
tmpfs            49M     0   49M   0% /run/user/1000
/dev/md0        569M  488K  539M   1% /data

```