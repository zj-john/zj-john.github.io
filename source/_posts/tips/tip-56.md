---
title: 一次Centos虚机硬盘扩容的操作
categories:
  - tips
tags:
  - 虚机
  - 硬盘扩容
  - centos7
toc: false
date: 2018-08-27 11:05:28
---
硬盘当前不断有写入，磁盘空间不足，不能重启。  
给磁盘加了容量后，fdisk中已经看到，df中还没有。

下面是识别过程：

<!-- more -->

```shell
[root@myVM ~]# parted /dev/sda
GNU Parted 2.1
Using /dev/sda
Welcome to GNU Parted! Type 'help' to view a list of commands.

// 目前已有3块主分区，最多再加一块。
(parted) print                                                            
Model: VMware Virtual disk (scsi)
Disk /dev/sda: 290GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos

Number  Start   End    Size    Type     File system  Flags
 1      1049kB  525MB  524MB   primary  ext4         boot
 2      525MB   193GB  193GB   primary               lvm
 3      193GB   225GB  32.2GB  primary

(parted) unit s                                                           
(parted) print                                                            
Model: VMware Virtual disk (scsi)
Disk /dev/sda: 566231040s
Sector size (logical/physical): 512B/512B
Partition Table: msdos

Number  Start       End         Size        Type     File system  Flags
 1      2048s       1026047s    1024000s    primary  ext4         boot
 2      1026048s    377479304s  376453257s  primary               lvm
 3      377479305s  440401919s  62922615s   primary

// 在最后一块分区的最后一个位置（440401919s）加1处添加一块主分区
(parted) mkpart primary 440401920s 100%
Warning: WARNING: the kernel failed to re-read the partition table on /dev/sda (Device or resource busy).  As a result, it may not reflect all of your changes until after reboot.

(parted) print                                                            
Model: VMware Virtual disk (scsi)
Disk /dev/sda: 566231040s
Sector size (logical/physical): 512B/512B
Partition Table: msdos

Number  Start       End         Size        Type     File system  Flags
 1      2048s       1026047s    1024000s    primary  ext4         boot
 2      1026048s    377479304s  376453257s  primary               lvm
 3      377479305s  440401919s  62922615s   primary
 4      440401920s  566231039s  125829120s  primary

(parted) unit gb                                                          
(parted) print                                                            
Model: VMware Virtual disk (scsi)
Disk /dev/sda: 290GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos

Number  Start   End     Size    Type     File system  Flags
 1      0.00GB  0.53GB  0.52GB  primary  ext4         boot
 2      0.53GB  193GB   193GB   primary               lvm
 3      193GB   225GB   32.2GB  primary
 4      225GB   290GB   64.4GB  primary

(parted) q                                                                
[root@myVM ~]# more /proc/partitions
major minor  #blocks  name

   8        0  283115520 sda
   8        1     512000 sda1
   8        2  156771358 sda2
   8        3   31461307 sda3
 253        0    8388608 dm-0
 253        1  179830784 dm-1

//加入分区表
[root@myVM ~]# addpart /dev/sda 4 440401920s 125829120s

[root@myVM ~]# more /proc/partitions
major minor  #blocks  name

   8        0  283115520 sda
   8        1     512000 sda1
   8        2  156771358 sda2
   8        3   31461307 sda3
   8        4   62914560 sda4
 253        0    8388608 dm-0
 253        1  179830784 dm-1

// 创建PV
[root@myVM ~]# pvcreate /dev/sda4
  Physical volume "/dev/sda4" successfully created

// 扩展VG
[root@myVM ~]# vgextend /dev/VolGroup00 /dev/sda4
  Volume group "VolGroup00" successfully extended

[root@myVM ~]# vgdisplay -v
    Finding all volume groups
    Finding volume group "VolGroup00"
  --- Volume group ---
  VG Name               VolGroup00
  System ID             
  Format                lvm2
  Metadata Areas        3
  Metadata Sequence No  15
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                3
  Act PV                3
  VG Size               239.47 GiB
  PE Size               32.00 MiB
  Total PE              7663
  Alloc PE / Size       5744 / 179.50 GiB
  Free  PE / Size       1919 / 59.97 GiB
  VG UUID               VmXybG-XlLj-89WX-xu7S-Wv2v-lcFm-RP2I80

  --- Logical volume ---
  LV Path                /dev/VolGroup00/lv_swap
  LV Name                lv_swap
  VG Name                VolGroup00
  LV UUID                agW1rd-InHh-00KJ-VtDq-qXzf-X20Y-2AAS8w
  LV Write Access        read/write
  LV Creation host, time localhost.localdomain, 2016-08-15 02:13:38 +0800
  LV Status              available
  # open                 1
  LV Size                8.00 GiB
  Current LE             256
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:0

  --- Logical volume ---
  LV Path                /dev/VolGroup00/lv_root
  LV Name                lv_root
  VG Name                VolGroup00
  LV UUID                ehJEGy-Ln22-Gv2u-vswq-MK2f-f4Q5-DsR5rs
  LV Write Access        read/write
  LV Creation host, time localhost.localdomain, 2016-08-15 02:13:38 +0800
  LV Status              available
  # open                 1
  LV Size                171.50 GiB
  Current LE             5488
  Segments               2
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:1

  --- Physical volumes ---
  PV Name               /dev/sda2     
  PV UUID               V4kpvd-4450-H6Ud-dLaH-0xyz-yebc-fxckbd
  PV Status             allocatable
  Total PE / Free PE    4784 / 0

  PV Name               /dev/sda3     
  PV UUID               ZNv0MZ-vLFv-5c0i-hxFA-8Xmq-TOW4-nuaVcp
  PV Status             allocatable
  Total PE / Free PE    960 / 0

  PV Name               /dev/sda4     
  PV UUID               oBbCrF-CzVU-8Xyq-3wei-wAL6-vkLu-T5b0EJ
  PV Status             allocatable
  Total PE / Free PE    1919 / 1919

// 扩展lv并加入文件系统
[root@myVM ~]# lvextend -l +100%free -r /dev/VolGroup00/lv_root
  Extending logical volume lv_root to 231.47 GiB
  Logical volume lv_root successfully resized
resize2fs 1.41.12 (17-May-2010)
Filesystem at /dev/mapper/VolGroup00-lv_root is mounted on /; on-line resizing required
old desc_blocks = 11, new_desc_blocks = 15
Performing an on-line resize of /dev/mapper/VolGroup00-lv_root to 60678144 (4k) blocks.
The filesystem on /dev/mapper/VolGroup00-lv_root is now 60678144 blocks long.

[root@myVM ~]# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/mapper/VolGroup00-lv_root
                      228G  149G   68G  69% /
tmpfs                  12G     0   12G   0% /dev/shm
/dev/sda1             485M   60M  400M  14% /boot
```
