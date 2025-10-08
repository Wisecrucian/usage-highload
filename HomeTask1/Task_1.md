# Задание 1: Kernel and Module Inspection

### Версия Ядра ОС
```
root@cqeunxhpza:~# uname -r
6.8.0-78-generic
```
### Список модулей
```
root@cqeunxhpza:~# lsmod
Module                  Size  Used by
tls                   155648  0
isofs                  61440  0
binfmt_misc            24576  1
nls_iso8859_1          12288  1
joydev                 32768  0
input_leds             12288  0
serio_raw              20480  0
sch_fq_codel           24576  2
dm_multipath           45056  0
msr                    12288  0
efi_pstore             12288  0
nfnetlink              20480  1
dmi_sysfs              24576  0
ip_tables              32768  0
x_tables               65536  1 ip_tables
autofs4                57344  2
btrfs                2043904  0
blake2b_generic        24576  0
raid10                 73728  0
raid456               196608  0
async_raid6_recov      20480  1 raid456
async_memcpy           16384  2 raid456,async_raid6_recov
async_pq               20480  2 raid456,async_raid6_recov
async_xor              16384  3 async_pq,raid456,async_raid6_recov
async_tx               16384  5 async_pq,async_memcpy,async_xor,raid456,async_raid6_recov
xor                    20480  2 async_xor,btrfs
raid6_pq              126976  4 async_pq,btrfs,raid456,async_raid6_recov
libcrc32c              12288  2 btrfs,raid456
raid1                  57344  0
raid0                  24576  0
crct10dif_pclmul       12288  1
crc32_pclmul           12288  0
polyval_clmulni        12288  0
polyval_generic        12288  1 polyval_clmulni
ghash_clmulni_intel    16384  0
sha256_ssse3           32768  0
sha1_ssse3             32768  0
psmouse               217088  0
floppy                131072  0
aesni_intel           356352  0
crypto_simd            16384  1 aesni_intel
cryptd                 24576  2 crypto_simd,ghash_clmulni_intel
```

### Отключение модуля cdrom через blacklist
```
root@cqeunxhpza:~# sudo nano /etc/modprobe.d/blacklist-cdrom.conf
blacklist cdrom
blacklist sr_mod
root@cqeunxhpza:~# sudo update-initramfs -u
update-initramfs: Generating /boot/initrd.img-6.8.0-78-generic
root@cqeunxhpza:~# sudo reboot
```

### онфигурация ядра, параметр CONFIG_XFS_FS
```
root@cqeunxhpza:~# ls -l /boot/initrd.img-6.8.0-78-generic
-rw-r--r-- 1 root root 28754798 Oct  2 08:28 /boot/initrd.img-6.8.0-78-generic
root@cqeunxhpza:~# grep "CONFIG_XFS_FS" /boot/initrd.img-6.8.0-78-generic
CONFIG_XFS_FS=m
m означает, что поддержка XFS загружается как модуль ядра, а не встроена в него.
```