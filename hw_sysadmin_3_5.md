1. Изучить sparse (разряженные) файлы.  
Данный тип файлов позволяет с большей эффективностью использовать дисксовое пространство, так как занимают меньший объем дисквого пространства, чем их реальный объем.  

2. Могут ли файлы, являющиеся жесткой ссылкой на один объект, иметь разные права доступа и владельца? Почему?  
Нет, так как имеет тот же inode, то права будут одни и теже  
- для примера создам файл test_link и жесткую ссылку test_link_hard 
```
vagrant@vagrant:~$ touch test_link
vagrant@vagrant:~$ ln test_link test_link_hard
vagrant@vagrant:~$ stat test_link test_link_hard
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
- изменим права для tect_link с 0664 на 0665
```
vagrant@vagrant:~$ chmod 0665 test_link
vagrant@vagrant:~$ stat test_link test_link_hard
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
- для 0665 права поменялись в обоих случаях

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

5. Используя sfdisk, перенесите данную таблицу разделов на второй диск.  

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
```
vagrant@sysadm-fs:~$ sudo pvcreate /dev/md1 /dev/md2
  Physical volume "/dev/md1" successfully created.
  Physical volume "/dev/md2" successfully created.
```
```
vagrant@sysadm-fs:~$ sudo pvscan
  PV /dev/sda3   VG ubuntu-vg       lvm2 [<62.00 GiB / 31.00 GiB free]
  PV /dev/md1                       lvm2 [<2.00 GiB]
  PV /dev/md2                       lvm2 [1018.00 MiB]
  Total: 3 [<64.99 GiB] / in use: 1 [<62.00 GiB] / in no VG: 2 [2.99 GiB]
```
9. Создайте общую volume-group на этих двух PV.  
```
vagrant@sysadm-fs:~$ sudo vgcreate volume_group_1 /dev/md1 /dev/md2
  Volume group "volume_group_1" successfully created
```
```
vagrant@sysadm-fs:~$ sudo vgdisplay
  --- Volume group ---
  VG Name               ubuntu-vg
  System ID             
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  2
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                1
  Open LV               1
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               <62.00 GiB
  PE Size               4.00 MiB
  Total PE              15871
  Alloc PE / Size       7935 / <31.00 GiB
  Free  PE / Size       7936 / 31.00 GiB
  VG UUID               UZfqXZ-997r-hT24-kqSU-tjd6-fB2t-Up7O0f
   
  --- Volume group ---
  VG Name               volume_group_1
  System ID             
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               <2.99 GiB
  PE Size               4.00 MiB
  Total PE              765
  Alloc PE / Size       0 / 0   
  Free  PE / Size       765 / <2.99 GiB
  VG UUID               0lS2c2-Kr2f-V7Bl-ygG7-1Ec6-Z4nn-EvrHuu
```
```
vagrant@sysadm-fs:~$ sudo pvdisplay
  --- Physical volume ---
  PV Name               /dev/sda3
  VG Name               ubuntu-vg
  PV Size               <62.00 GiB / not usable 0   
  Allocatable           yes 
  PE Size               4.00 MiB
  Total PE              15871
  Free PE               7936
  Allocated PE          7935
  PV UUID               zsjUIf-lkCP-mcQc-77AQ-EnRV-aROu-2kC8c0
   
  --- Physical volume ---
  PV Name               /dev/md1
  VG Name               volume_group_1
  PV Size               <2.00 GiB / not usable 0   
  Allocatable           yes 
  PE Size               4.00 MiB
  Total PE              511
  Free PE               511
  Allocated PE          0
  PV UUID               TQsYVD-fQF1-2UVe-ZSOl-cI41-QaPC-13j5iZ
   
  --- Physical volume ---
  PV Name               /dev/md2
  VG Name               volume_group_1
  PV Size               1018.00 MiB / not usable 2.00 MiB
  Allocatable           yes 
  PE Size               4.00 MiB
  Total PE              254
  Free PE               254
  Allocated PE          0
  PV UUID               cNW0pr-hbwS-6U3Y-CsAs-BIUv-rNH3-k0qa1U
```

10. Создайте LV размером 100 Мб, указав его расположение на PV с RAID0.  
```
vagrant@sysadm-fs:~$ sudo lvcreate -L 100M -n logical_volume1 volume_group_1 /dev/md2
  Logical volume "logical_volume1" created.
```
Проверим  
```
vagrant@sysadm-fs:~$ sudo lvdisplay
  --- Logical volume ---
  LV Path                /dev/ubuntu-vg/ubuntu-lv
  LV Name                ubuntu-lv
  VG Name                ubuntu-vg
  LV UUID                uVU4nO-Z2nN-pe9b-ehaW-yje4-GZwy-tEtd4j
  LV Write Access        read/write
  LV Creation host, time ubuntu-server, 2022-12-11 06:19:22 +0000
  LV Status              available
  # open                 1
  LV Size                <31.00 GiB
  Current LE             7935
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:0
   
  --- Logical volume ---
  LV Path                /dev/volume_group_1/logical_volume1
  LV Name                logical_volume1
  VG Name                volume_group_1
  LV UUID                1skzAu-2VS3-CRrq-Y3st-l0et-MCjv-qnfAhm
  LV Write Access        read/write
  LV Creation host, time sysadm-fs, 2023-01-15 18:06:36 +0000
  LV Status              available
  # open                 0
  LV Size                100.00 MiB
  Current LE             25
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     4096
  Block device           253:1
  ```
  ```
  vagrant@sysadm-fs:~$ lsblk
NAME                                 MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                                  7:0    0 67.8M  1 loop  /snap/lxd/22753
loop1                                  7:1    0   62M  1 loop  /snap/core20/1611
loop3                                  7:3    0 49.8M  1 loop  /snap/snapd/17950
loop4                                  7:4    0 63.3M  1 loop  /snap/core20/1778
loop5                                  7:5    0 91.9M  1 loop  /snap/lxd/24061
sda                                    8:0    0   64G  0 disk  
├─sda1                                 8:1    0    1M  0 part  
├─sda2                                 8:2    0    2G  0 part  /boot
└─sda3                                 8:3    0   62G  0 part  
  └─ubuntu--vg-ubuntu--lv            253:0    0   31G  0 lvm   /
sdb                                    8:16   0  2.5G  0 disk  
├─sdb1                                 8:17   0    2G  0 part  
│ └─md1                                9:1    0    2G  0 raid1 
└─sdb2                                 8:18   0  511M  0 part  
  └─md2                                9:2    0 1018M  0 raid0 
    └─volume_group_1-logical_volume1 253:1    0  100M  0 lvm   
sdc                                    8:32   0  2.5G  0 disk  
├─sdc1                                 8:33   0    2G  0 part  
│ └─md1                                9:1    0    2G  0 raid1 
└─sdc2                                 8:34   0  511M  0 part  
  └─md2                                9:2    0 1018M  0 raid0 
    └─volume_group_1-logical_volume1 253:1    0  100M  0 lvm 
```

11. Создайте mkfs.ext4 ФС на получившемся LV.  
```
vagrant@sysadm-fs:~$ sudo mkfs.ext4 /dev/volume_group_1/logical_volume1
mke2fs 1.45.5 (07-Jan-2020)
Creating filesystem with 25600 4k blocks and 25600 inodes

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (1024 blocks): done
Writing superblocks and filesystem accounting information: done
```

12. Смонтируйте этот раздел в любую директорию, например, /tmp/new  
```
vagrant@sysadm-fs:~$ mkdir /tmp/lw
vagrant@sysadm-fs:~$ sudo mount /dev/volume_group_1/logical_volume1 /tmp/lw
```
```
vagrant@sysadm-fs:~$ df -Th | grep "^/dev"
/dev/mapper/ubuntu--vg-ubuntu--lv          ext4       31G  3.7G   26G  13% /
/dev/loop0                                 squashfs   68M   68M     0 100% /snap/lxd/22753
/dev/loop1                                 squashfs   62M   62M     0 100% /snap/core20/1611
/dev/sda2                                  ext4      2.0G  106M  1.7G   6% /boot
/dev/loop3                                 squashfs   50M   50M     0 100% /snap/snapd/17950
/dev/loop4                                 squashfs   64M   64M     0 100% /snap/core20/1778
/dev/loop5                                 squashfs   92M   92M     0 100% /snap/lxd/24061
/dev/mapper/volume_group_1-logical_volume1 ext4       93M   24K   86M   1% /tmp/lw
```

13. Поместите туда тестовый файл, например wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz.  
```
vagrant@sysadm-fs:~$ sudo wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz -O /tmp/lw/test.gz
--2023-01-15 18:27:21--  https://mirror.yandex.ru/ubuntu/ls-lR.gz
Resolving mirror.yandex.ru (mirror.yandex.ru)... 213.180.204.183, 2a02:6b8::183
Connecting to mirror.yandex.ru (mirror.yandex.ru)|213.180.204.183|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 24416140 (23M) [application/octet-stream]
Saving to: ‘/tmp/lw/test.gz’

/tmp/lw/test.gz           100%[====================================>]  23.28M   997KB/s    in 26s     

2023-01-15 18:27:47 (926 KB/s) - ‘/tmp/lw/test.gz’ saved [24416140/24416140]
```
```vagrant@sysadm-fs:~$ ls -l /tmp/lw
total 23860
drwx------ 2 root root    16384 Jan 15 18:15 lost+found
-rw-r--r-- 1 root root 24416140 Jan 15 15:38 test.gz
```

14. Прикрепите вывод ```lsblk```.  
```
vagrant@sysadm-fs:~$ lsblk
NAME                                 MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                                  7:0    0 67.8M  1 loop  /snap/lxd/22753
loop1                                  7:1    0   62M  1 loop  /snap/core20/1611
loop3                                  7:3    0 49.8M  1 loop  /snap/snapd/17950
loop4                                  7:4    0 63.3M  1 loop  /snap/core20/1778
loop5                                  7:5    0 91.9M  1 loop  /snap/lxd/24061
sda                                    8:0    0   64G  0 disk  
├─sda1                                 8:1    0    1M  0 part  
├─sda2                                 8:2    0    2G  0 part  /boot
└─sda3                                 8:3    0   62G  0 part  
  └─ubuntu--vg-ubuntu--lv            253:0    0   31G  0 lvm   /
sdb                                    8:16   0  2.5G  0 disk  
├─sdb1                                 8:17   0    2G  0 part  
│ └─md1                                9:1    0    2G  0 raid1 
└─sdb2                                 8:18   0  511M  0 part  
  └─md2                                9:2    0 1018M  0 raid0 
    └─volume_group_1-logical_volume1 253:1    0  100M  0 lvm   /tmp/lw
sdc                                    8:32   0  2.5G  0 disk  
├─sdc1                                 8:33   0    2G  0 part  
│ └─md1                                9:1    0    2G  0 raid1 
└─sdc2                                 8:34   0  511M  0 part  
  └─md2                                9:2    0 1018M  0 raid0 
    └─volume_group_1-logical_volume1 253:1    0  100M  0 lvm   /tmp/lw
```

15. Протестируйте целостность файла:  
```
vagrant@sysadm-fs:~$ gzip -t /tmp/lw/test.gz
vagrant@sysadm-fs:~$ echo $?
0
```

16. Используя pvmove, переместите содержимое PV с RAID0 на RAID1.  
```
vagrant@sysadm-fs:~$ sudo pvmove -n /dev/volume_group_1/logical_volume1 /dev/md2 /dev/md1
  /dev/md2: Moved: 20.00%
  /dev/md2: Moved: 100.00%
```
```
vagrant@sysadm-fs:~$ lsblk
NAME                                 MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                                  7:0    0 67.8M  1 loop  /snap/lxd/22753
loop1                                  7:1    0   62M  1 loop  /snap/core20/1611
loop3                                  7:3    0 49.8M  1 loop  /snap/snapd/17950
loop4                                  7:4    0 63.3M  1 loop  /snap/core20/1778
loop5                                  7:5    0 91.9M  1 loop  /snap/lxd/24061
sda                                    8:0    0   64G  0 disk  
├─sda1                                 8:1    0    1M  0 part  
├─sda2                                 8:2    0    2G  0 part  /boot
└─sda3                                 8:3    0   62G  0 part  
  └─ubuntu--vg-ubuntu--lv            253:0    0   31G  0 lvm   /
sdb                                    8:16   0  2.5G  0 disk  
├─sdb1                                 8:17   0    2G  0 part  
│ └─md1                                9:1    0    2G  0 raid1 
│   └─volume_group_1-logical_volume1 253:1    0  100M  0 lvm   /tmp/lw
└─sdb2                                 8:18   0  511M  0 part  
  └─md2                                9:2    0 1018M  0 raid0 
sdc                                    8:32   0  2.5G  0 disk  
├─sdc1                                 8:33   0    2G  0 part  
│ └─md1                                9:1    0    2G  0 raid1 
│   └─volume_group_1-logical_volume1 253:1    0  100M  0 lvm   /tmp/lw
└─sdc2                                 8:34   0  511M  0 part  
  └─md2                                9:2    0 1018M  0 raid0
```

17. Сделайте --fail на устройство в вашем RAID1 md.  
```
vagrant@sysadm-fs:~$ sudo mdadm /dev/md1 --fail /dev/sdb1
mdadm: set /dev/sdb1 faulty in /dev/md1
```

18. Подтвердите выводом dmesg, что RAID1 работает в деградированном состоянии.  
```
vagrant@sysadm-fs:~$ dmesg -T | grep md1
[Sun Jan 15 17:36:47 2023] md/raid1:md1: not clean -- starting background reconstruction
[Sun Jan 15 17:36:47 2023] md/raid1:md1: active with 2 out of 2 mirrors
[Sun Jan 15 17:36:47 2023] md1: detected capacity change from 0 to 2144337920
[Sun Jan 15 17:36:47 2023] md: resync of RAID array md1
[Sun Jan 15 17:36:57 2023] md: md1: resync done.
[Sun Jan 15 18:40:25 2023] md/raid1:md1: Disk failure on sdb1, disabling device.
                           md/raid1:md1: Operation continuing on 1 devices.
```

19. Протестируйте целостность файла, несмотря на "сбойный" диск он должен продолжать быть доступен:  
```
vagrant@sysadm-fs:~$ gzip -t /tmp/lw/test.gz
vagrant@sysadm-fs:~$ echo $?
0
```

20. Погасите тестовый хост, ```vagrant destroy```.  
```vagrant destroy```




