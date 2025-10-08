# Задание 4. NUMA и cgroups

### количество NUMA нод

```
root@cqeunxhpza:~# numactl --hardware
available: 1 nodes (0)
node 0 cpus: 0 1 2 3
node 0 size: 5925 MB
node 0 free: 5534 MB
node distances:
node   0
0:  10 
```

Сервер имеет 1 NUMA ноду с 4 CPU ядрами и 5925 MB памяти.

### Тест с превышением лимита памяти
```
sudo systemd-run --unit=test-oom-mem --slice=testing.slice \
--property="MemoryMax=150M" \
stress --cpu 1 --vm 1 --vm-bytes 300M --timeout 30s
```
### Результат
```
root@cqeunxhpza:~# sudo systemctl status test-oom-mem --no-pager
× test-oom-mem.service - /usr/bin/stress --cpu 1 --vm 1 --vm-bytes 300M --timeout 30s
Loaded: loaded (/run/systemd/transient/test-oom-mem.service; transient)
Transient: yes
Active: failed (Result: oom-kill) since Wed 2025-10-08 21:40:33 UTC; 4s ago
Duration: 212ms
Process: 1258 ExecStart=/usr/bin/stress --cpu 1 --vm 1 --vm-bytes 300M --timeout 30s (code=killed, signal=TERM)
Main PID: 1258 (code=killed, signal=TERM)
CPU: 410ms

Oct 08 21:40:32 cqeunxhpza systemd[1]: Started test-oom-mem.service - /usr/bin/stress --cpu 1 --vm 1 --vm-bytes 300M --timeout 30s.
Oct 08 21:40:32 cqeunxhpza stress[1258]: stress: info: [1258] dispatching hogs: 1 cpu, 0 io, 1 vm, 0 hdd
Oct 08 21:40:33 cqeunxhpza systemd[1]: test-oom-mem.service: A process of this unit has been killed by the OOM killer.
Oct 08 21:40:33 cqeunxhpza systemd[1]: test-oom-mem.service: Failed with result 'oom-kill'.
```

### Тест в пределах лимита
```
sudo systemd-run --unit=test-oom-mem --slice=testing.slice \
--property="MemoryMax=150M" \
stress --cpu 1 --vm 1 --vm-bytes 300M --timeout 30s
```

### Результат
```
root@cqeunxhpza:~# systemd-cgls /testing.slice
CGroup /testing.slice:
└─test-normal-mem.service
├─1288 /usr/bin/stress --cpu 1 --vm 1 --vm-bytes 150M --timeout 20s
├─1289 /usr/bin/stress --cpu 1 --vm 1 --vm-bytes 150M --timeout 20s
└─1290 /usr/bin/stress --cpu 1 --vm 1 --vm-bytes 150M --timeout 20s
```

1) Будет ли работать тест если мы запрашиваем 300М оперативной памяти, а ограничивыем 150М?
    - Не будет, процесс немедленно убивается OOM killer при попытке превысить установленный лимит памяти.
2) Что происходит с процессом при превышении
    - Система не позволяет процессу фактически использовать больше памяти, чем установленный лимит, 

Тест запускается после установки `-vm-bytes` в 150M
MemoryMax устанавливает максимальный лимит использования памяти для службы.
CPUWeight определяет приоритет при распределении времени CPU между процессами.