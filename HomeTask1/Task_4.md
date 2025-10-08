# Задание 4: Использование pseudo filesystem

### Извлечение из `/proc` модели CPU и объёма памяти в KB

```
root@cqeunxhpza:~# grep -m1 "model name" /proc/cpuinfo | cut -d: -f2 | sed 's/^[ \t]*//'
AMD EPYC 7763 64-Core Processor
grep "MemTotal" /proc/meminfo
MemTotal:        6067516 kB
```

### Нахождение Parent Process ID (PPid) вашего текущего shell с `/proc/$$/status`
```
root@cqeunxhpza:~# grep "PPid" /proc/$$/status
PPid:	1027
```
`$$` — это переменная в bash, которая содержит PID текущего shell процесса.

### Определение настройки I/O scheduler для основного диска `/dev/sda`

```
root@cqeunxhpza:~# cat /sys/block/vda/queue/scheduler
[none] mq-deadline 
```

### Определение размер MTU для основного сетевого интерфейса

```
root@cqeunxhpza:~# ip route | grep default | awk '{print $5}' | head -1
eth0
root@cqeunxhpza:~# ip addr show eth0 | grep mtu
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
```
Основной сетевой интерфейс - eth0