# Задание 3: LVM Management

### Создание 2gb диска и раздела `/dev/sdb`
Не было возможности подключить физический диск, поэтому создал файл-образ на 2gb 
```
root@cqeunxhpza:~# sudo fdisk /dev/loop0

Welcome to fdisk (util-linux 2.39.3).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS (MBR) disklabel with disk identifier 0x8720dd81.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 
First sector (2048-4194303, default 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-4194303, default 4194303): 

Created a new partition 1 of type 'Linux' and of size 2 GiB.

Command (m for help): t
Selected partition 1
Hex code or alias (type L to list all): 8e
Changed type of partition 'Linux' to 'Linux LVM'.

Command (m for help): p
Disk /dev/loop0: 2 GiB, 2147483648 bytes, 4194304 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x8720dd81

Device       Boot Start     End Sectors Size Id Type
/dev/loop0p1       2048 4194303 4192256   2G 8e Linux LVM

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

### Создание PV на этом разделе

```
root@cqeunxhpza:~# sudo pvcreate /dev/loop0p1
  Physical volume "/dev/loop0p1" successfully created.

root@cqeunxhpza:~# sudo pvs
  PV           VG Fmt  Attr PSize  PFree 
  /dev/loop0p1    lvm2 ---  <2.00g <2.00g
```

### Создание VG `vg_highload`

```
root@cqeunxhpza:~# sudo vgs
  VG          #PV #LV #SN Attr   VSize  VFree 
  vg_highload   1   0   0 wz--n- <2.00g <2.00g
root@cqeunxhpza:~# sudo vgdisplay
  --- Volume group ---
  VG Name               vg_highload
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
  VG Size               <2.00 GiB
  PE Size               4.00 MiB
  Total PE              511
  Alloc PE / Size       0 / 0   
  Free  PE / Size       511 / <2.00 GiB
  VG UUID               9dm0Hu-Vgq1-vq6g-fPI6-GPUz-tu05-aJukKd
```

### Создание LV `data_lv` (1200 MiB) и `logs_lv` (оставшееся место)

```
root@cqeunxhpza:~# sudo lvcreate -L 1200M -n data_lv vg_highload
  Logical volume "data_lv" created.
root@cqeunxhpza:~# sudo lvcreate -l 100%FREE -n logs_lv vg_highload
  Logical volume "logs_lv" created.
root@cqeunxhpza:~# sudo lvdisplay
  --- Logical volume ---
  LV Path                /dev/vg_highload/data_lv
  LV Name                data_lv
  VG Name                vg_highload
  LV UUID                J4c0Tb-9aoa-2FFr-qu2v-g9s4-iFDW-8rXi6X
  LV Write Access        read/write
  LV Creation host, time cqeunxhpza, 2025-10-02 09:39:55 +0000
  LV Status              available
  # open                 0
  LV Size                1.17 GiB
  Current LE             300
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           252:0
   
  --- Logical volume ---
  LV Path                /dev/vg_highload/logs_lv
  LV Name                logs_lv
  VG Name                vg_highload
  LV UUID                OfphjF-6QAZ-dde3-agcw-nqPY-h4E5-0ExPan
  LV Write Access        read/write
  LV Creation host, time cqeunxhpza, 2025-10-02 09:39:59 +0000
  LV Status              available
  # open                 0
  LV Size                844.00 MiB
  Current LE             211
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           252:1
```

### Форматирование `data_lv` как ext4 и примонтировать в `/mnt/app_data`

```
root@cqeunxhpza:~# sudo mkfs.ext4 /dev/vg_highload/data_lv
mke2fs 1.47.0 (5-Feb-2023)
Discarding device blocks: done                            
Creating filesystem with 307200 4k blocks and 76800 inodes
Filesystem UUID: f52f55a0-2d82-48f9-973e-75fd9233c404
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done 

root@cqeunxhpza:~# sudo mkdir -p /mnt/app_data
root@cqeunxhpza:~# sudo mount /dev/vg_highload/data_lv /mnt/app_data

```

### Форматирование `logs_lv` как xfs и примонтировать в `/mnt/app_logs`

```
root@cqeunxhpza:~# sudo mkfs.xfs /dev/vg_highload/logs_lv
meta-data=/dev/vg_highload/logs_lv isize=512    agcount=4, agsize=54016 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=1
         =                       reflink=1    bigtime=1 inobtcount=1 nrext64=0
data     =                       bsize=4096   blocks=216064, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=16384, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
Discarding blocks...Done.
root@cqeunxhpza:~# sudo mkdir -p /mnt/app_logs
root@cqeunxhpza:~# sudo mount /dev/vg_highload/logs_lv /mnt/app_logs
```

### Проверка

```
root@cqeunxhpza:~# df -hT | grep "vg_highload"
/dev/mapper/vg_highload-data_lv ext4   1.2G   24K  1.1G   1% /mnt/app_data
/dev/mapper/vg_highload-logs_lv xfs    780M   48M  733M   7% /mnt/app_logs

```