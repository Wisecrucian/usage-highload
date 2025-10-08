# Задание 1. Systemd

### bash-скрипт /usr/local/bin/homework_service.sh
```
root@cqeunxhpza:~# sudo systemctl start homework_service.service
root@cqeunxhpza:~# sudo systemctl status homework_service.service
● homework_service.service - Homework Custom Service
Loaded: loaded (/etc/systemd/system/homework_service.service; enabled; preset: enabled)
Active: active (running) since Mon 2025-10-06 19:11:22 UTC; 3s ago
Main PID: 2411 (homework_servic)
Tasks: 2 (limit: 7066)
Memory: 612.0K (peak: 1.0M)
CPU: 9ms
CGroup: /system.slice/homework_service.service
├─2411 /bin/bash /usr/local/bin/homework_service.sh
└─2413 sleep 15

Oct 06 19:11:22 cqeunxhpza systemd[1]: Started homework_service.service - Homework Custom Service.
Oct 06 19:11:22 cqeunxhpza homework_service.sh[2411]: My custom service has started.
```

### Логи
```
root@cqeunxhpza:~# tail -f /tmp/homework_service.log
Service heartbeat: Mon Oct  6 07:11:22 PM UTC 2025
Service heartbeat: Mon Oct  6 07:11:37 PM UTC 2025
Service heartbeat: Mon Oct  6 07:11:52 PM UTC 2025
```

```
root@cqeunxhpza:~# ps aux | grep homework
root         958  0.0  0.0   7340  3712 ?        Ss   21:36   0:00 /bin/bash /usr/local/bin/homework_service.sh
root        1881  0.0  0.0   6544  2304 pts/2    S+   22:14   0:00 grep --color=auto homework
```

```
root@cqeunxhpza:~# systemd-analyze blame | head -5
15.206s homework_service.service
890ms NetworkManager.service
888ms cloud-init-local.service
875ms cloud-config.service
809ms cloud-init.service
```