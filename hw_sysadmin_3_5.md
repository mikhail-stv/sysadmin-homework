1. Изучить sparse (разряженные) файлы
Изучил 

2. Могут ли файлы, являющиеся жесткой ссылкой на один объект, иметь разные права доступа и владельца? Почему?
Нет, так как имеет тот же inode, то права будут одни и теже
-
```
vagrant@sysadm-fs:~$ touch test_link
vagrant@sysadm-fs:~$ ln test_link test_link_hard
```
-
```
vagrant@sysadm-fs:~$ stat test_link test_link_hard
  File: test_link
  Size: 0         	Blocks: 0          IO Block: 4096   regular empty file
Device: fd00h/64768d	Inode: 1310812     Links: 2
Access: (0664/-rw-rw-r--)  Uid: ( 1000/ vagrant)   Gid: ( 1000/ vagrant)
Access: 2023-01-15 17:41:06.899158278 +0000
Modify: 2023-01-15 17:41:06.899158278 +0000
Change: 2023-01-15 17:41:30.574989186 +0000
 Birth: -
  File: test_link_hard
  Size: 0         	Blocks: 0          IO Block: 4096   regular empty file
Device: fd00h/64768d	Inode: 1310812     Links: 2
Access: (0664/-rw-rw-r--)  Uid: ( 1000/ vagrant)   Gid: ( 1000/ vagrant)
Access: 2023-01-15 17:41:06.899158278 +0000
Modify: 2023-01-15 17:41:06.899158278 +0000
Change: 2023-01-15 17:41:30.574989186 +0000
 Birth: -
```
-
```
vagrant@sysadm-fs:~$ stat test_link test_link_hard
  File: test_link
  Size: 0         	Blocks: 0          IO Block: 4096   regular empty file
Device: fd00h/64768d	Inode: 1310812     Links: 2
Access: (0665/-rw-rw-r-x)  Uid: ( 1000/ vagrant)   Gid: ( 1000/ vagrant)
Access: 2023-01-15 17:41:06.899158278 +0000
Modify: 2023-01-15 17:41:06.899158278 +0000
Change: 2023-01-15 17:43:50.596958739 +0000
 Birth: -
  File: test_link_hard
  Size: 0         	Blocks: 0          IO Block: 4096   regular empty file
Device: fd00h/64768d	Inode: 1310812     Links: 2
Access: (0665/-rw-rw-r-x)  Uid: ( 1000/ vagrant)   Gid: ( 1000/ vagrant)
Access: 2023-01-15 17:41:06.899158278 +0000
Modify: 2023-01-15 17:41:06.899158278 +0000
Change: 2023-01-15 17:43:50.596958739 +0000
 Birth: -
```

3. Сделайте vagrant destroy на имеющийся инстанс Ubuntu. Замените содержимое Vagrantfile 

4. Используя fdisk, разбейте первый диск на 2 раздела: 2 Гб, оставшееся пространство.
```
vagrant@sysadm-fs:~$ sudo fdisk /dev/sdb

Welcome to fdisk (util-linux 2.34).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0xaede721a.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-5242879, default 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-5242879, default 5242879): +2G

Created a new partition 1 of type 'Linux' and of size 2 GiB.

Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): 

Using default response p.
Partition number (2-4, default 2): 2
First sector (4196352-5242879, default 4196352): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (4196352-5242879, default 5242879): 

Created a new partition 2 of type 'Linux' and of size 511 MiB.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```
5. Используя sfdisk, перенесите данную таблицу разделов на второй диск.
```
vagrant@sysadm-fs:~$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0                       7:0    0 67.8M  1 loop /snap/lxd/22753
loop1                       7:1    0   62M  1 loop /snap/core20/1611
loop3                       7:3    0 49.8M  1 loop /snap/snapd/17950
loop4                       7:4    0 63.3M  1 loop /snap/core20/1778
loop5                       7:5    0 91.9M  1 loop /snap/lxd/24061
sda                         8:0    0   64G  0 disk 
├─sda1                      8:1    0    1M  0 part 
├─sda2                      8:2    0    2G  0 part /boot
└─sda3                      8:3    0   62G  0 part 
  └─ubuntu--vg-ubuntu--lv 253:0    0   31G  0 lvm  /
sdb                         8:16   0  2.5G  0 disk 
├─sdb1                      8:17   0    2G  0 part 
└─sdb2                      8:18   0  511M  0 part 
sdc                         8:32   0  2.5G  0 disk 
``` 
```
vagrant@sysadm-fs:~$ sudo sfdisk -d /dev/sda > sda.txt
vagrant@sysadm-fs:~$ sudo sfdisk -d /dev/sdb > sdb.txt
vagrant@sysadm-fs:~$ sudo sfdisk /dev/sdc < sdb.txt
```
6. Соберите mdadm RAID1 на паре разделов 2 Гб.
```
vagrant@sysadm-fs:~$ sudo mdadm --create --verbose /dev/md1 -l 1 -n 2 /dev/sd{b1,c1}
mdadm: Note: this array has metadata at the start and
    may not be suitable as a boot device.  If you plan to
    store '/boot' on this device please ensure that
    your boot-loader understands md/v1.x metadata, or use
    --metadata=0.90
mdadm: size set to 2094080K
Continue creating array? y
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md1 started.
```
7. Соберите mdadm RAID0 на второй паре маленьких разделов.
```
vagrant@sysadm-fs:~$ sudo mdadm --create --verbose /dev/md2 -l 0 -n 2 /dev/sd{b2,c2}
mdadm: chunk size defaults to 512K
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md2 started.
```
```
vagrant@sysadm-fs:~$ cat /proc/mdstat
Personalities : [linear] [multipath] [raid0] [raid1] [raid6] [raid5] [raid4] [raid10] 
md2 : active raid0 sdc2[1] sdb2[0]
      1042432 blocks super 1.2 512k chunks
      
md1 : active raid1 sdc1[1] sdb1[0]
      2094080 blocks super 1.2 [2/2] [UU]
      
unused devices: <none>
```

8. Создайте 2 независимых PV на получившихся md-устройствах.

