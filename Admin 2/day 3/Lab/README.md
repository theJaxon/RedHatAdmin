# Day 3 Lab:
### Part 1 [proc FS]:
1. Check the present value of `/proc/sys/net/ipv4/icmp_echo_ignore_all`

```bash
cat /proc/sys/net/ipv4/icmp_echo_ignore_all
```

2. It should be 0, change it to 1 which will prevent other hosts from successfully pinging your host while not affecting your ability to ping them.

```bash
sudo su # for some reason sudo echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all didn't work
echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all
```

3. Try to ping your colleague, let your colleague try to ping your host.

<details>
<summary>Screenshot</summary>
<p>

<img src=""> 

</p>
</details>

4. Reboot and try the last step. What happened? Why?

`ping` is working again, because the `proc file system` is a virtual FS that gets rebuilt during boot so making any changes there is temporary and gets reset on reboot

5. Edit /etc/sysctl.conf and put the following line at the bottom:
`net.ipv4.icmp_echo_ignore_all=1`
6. Execute sysctl –p command.

```bash
echo "net.ipv4.icmp_echo_ignore_all=1" >> /etc/sysctl.conf && sysctl -p # Writing settings in a permanent way
```

7. Check the value of /proc/sys/net/ipv4/icmp_echo_ignore_all.

```bash
cat /proc/sys/net/ipv4/icmp_echo_ignore_all # The value is 1
```

---

### Part 2 [Partitioning & archiving]:
1. Use fdisk -l to locate information about the partition sizes.

```bash 
fdisk -l

Disk /dev/sda: 42.9 GB, 42949672960 bytes, 83886080 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x0009ef88

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048    83886079    41942016   83  Linux
```


<details>
<summary>2. Use fdisk to add a new logical partition that is 1GB in size.</summary>
<p>

```bash
[root@Centos vagrant]# mkfs.ext4 /dev/sdb
mke2fs 1.42.9 (28-Dec-2013)
/dev/sdb is entire device, not just one partition!
Proceed anyway? (y,n) y
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
131072 inodes, 524288 blocks
26214 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=536870912
16 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912

Allocating group tables: done
Writing inode tables: done
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done

[root@Centos vagrant]# mount /dev/sdb /usb/
[root@Centos vagrant]# ls /dev/sdb
/dev/sdb
[root@Centos vagrant]#
[root@Centos vagrant]# lsblk
NAME   MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
sda      8:0    0  40G  0 disk
└─sda1   8:1    0  40G  0 part /
sdb      8:16   0   2G  0 disk /usb
[root@Centos vagrant]# fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table
Building a new DOS disklabel with disk identifier 0xd0c01ec9.

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): e
Partition number (1-4, default 1): 4
First sector (2048-4194303, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-4194303, default 4194303): +1G
Partition 4 of type Extended and of size 1 GiB is set

Command (m for help): print

Disk /dev/sdb: 2147 MB, 2147483648 bytes, 4194304 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xd0c01ec9

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb4            2048     2099199     1048576    5  Extended

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
[root@Centos vagrant]# fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): p

Disk /dev/sdb: 2147 MB, 2147483648 bytes, 4194304 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xd0c01ec9

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb4            2048     2099199     1048576    5  Extended

Command (m for help): n
Partition type:
   p   primary (0 primary, 1 extended, 3 free)
   l   logical (numbered from 5)
Select (default p): l
Adding logical partition 5
First sector (4096-2099199, default 4096):
Using default value 4096
Last sector, +sectors or +size{K,M,G} (4096-2099199, default 2099199):
Using default value 2099199
Partition 5 of type Linux and of size 1023 MiB is set

Command (m for help): p

Disk /dev/sdb: 2147 MB, 2147483648 bytes, 4194304 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xd0c01ec9

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb4            2048     2099199     1048576    5  Extended
/dev/sdb5            4096     2099199     1047552   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.

[root@Centos vagrant]# partprobe
[root@Centos vagrant]# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   40G  0 disk
└─sda1   8:1    0   40G  0 part /
sdb      8:16   0    2G  0 disk
├─sdb4   8:20   0  512B  0 part
└─sdb5   8:21   0 1023M  0 part
```

</p>
</details>


3. Did the kernel feel the changes? Display the content of /proc/partitions file? What did you notice? How to overcome that?

i did `partprobe` directly after finishing so i didn't notice anything, changes were successfully made

```bash
[root@Centos vagrant]# cat /proc/partitions
major minor  #blocks  name

   8        0   41943040 sda
   8        1   41942016 sda1
   8       16    2097152 sdb
   8       20          0 sdb4
   8       21    1047552 sdb5
```

4. Make a new ext2 file system on the new logical partition you just created.

```bash
mkfs.ext2 /dev/sdb5
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
65536 inodes, 261888 blocks
13094 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=268435456
8 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376

Allocating group tables: done
Writing inode tables: done
Writing superblocks and filesystem accounting information: done
```

5. Create a directory, name it /data.

`mkdir /data`

6. Add a label to the new filesystem, name it data.
` e2label /dev/sdb5 data `

7. Add a new entry to /etc/fstab for the new filesystem using the label you just created.
```bash
nano /etc/fstab # then i appended the line
LABEL=data /data ext2 defaults 1 1 # wrote the changes 
```

8. Mount the new filesystem.

`mount LABEL=data /data`

9. Display your swap size.
```bash
[root@Centos vagrant]# cat /proc/swaps
Filename                                Type            Size    Used    Priority
/swapfile                               file            2097148 1544    -2
```

10. Create a swap file of size 512MB.
```bash
[root@Centos vagrant]# fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): n
Partition type:
   p   primary (0 primary, 1 extended, 3 free)
   l   logical (numbered from 5)
Select (default p): p
Partition number (1-3, default 1): 1
First sector (2099200-4194303, default 2099200):
Using default value 2099200
Last sector, +sectors or +size{K,M,G} (2099200-4194303, default 4194303): +512M
Partition 1 of type Linux and of size 512 MiB is set

Command (m for help): t
Partition number (1,4,5, default 5): 1
Hex code (type L to list all codes): 82
Changed type of partition 'Empty' to 'Linux swap / Solaris'

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.

[root@Centos vagrant]# partprobe
[root@Centos vagrant]# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   40G  0 disk
└─sda1   8:1    0   40G  0 part /
sdb      8:16   0    2G  0 disk
├─sdb1   8:17   0  512M  0 part
├─sdb4   8:20   0  512B  0 part
└─sdb5   8:21   0 1023M  0 part /data
```

11. Add the swap file to the virtual memory of the system.

```bash
[root@Centos vagrant]# mkswap /dev/sdb1
Setting up swapspace version 1, size = 524284 KiB
no label, UUID=a348e601-46e8-4cb2-99c3-93b0657ee08a
[root@Centos vagrant]# swapon /dev/sdb1
```

12. Display the swap size
```bash
[root@Centos vagrant]# cat /proc/swaps
Filename                                Type            Size    Used    Priority
/swapfile                               file            2097148 1544    -2
/dev/sdb1                               partition       524284  0       -3
[root@Centos vagrant]#

```

13. Backup /etc directory using tar utility.
```bash
tar czvf myetc.tar.gz /etc
```