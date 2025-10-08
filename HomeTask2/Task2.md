# Задание 2: Межпроцессное взаимодействие (IPC) с разделяемой памятью

```
root@cqeunxhpza:~# ipcs -m

------ Shared Memory Segments --------
key        shmid      owner      perms      bytes      nattch     status      
0x41017f16 1          root       666        1024       1                       
```

Выделили сегмент 1кб шаренной памяти (shmid 1), nattch=1 говорит о том, что к ней подключена наша прогамма
