# Задание 3. Балансировка Layer 4 с  с помощью IPVS

### dummy интерфейс
```
root@btbcdwrobg:~# ip addr show dummy1
3: dummy1: <BROADCAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN group default qlen 1000
link/ether 7e:13:28:e6:89:60 brd ff:ff:ff:ff:ff:ff
inet 192.168.100.1/32 scope global dummy1
valid_lft forever preferred_lft forever
inet6 fe80::7c13:28ff:fee6:8960/64 scope link
valid_lft forever preferred_lft forever
```


```
root@btbcdwrobg:~# sudo ipvsadm -C
root@btbcdwrobg:~# sudo ipvsadm -A -t 192.168.100.1:80 -s rr
root@btbcdwrobg:~# sudo ipvsadm -a -t 192.168.100.1:80 -r 127.0.0.1:8081 -m
sudo ipvsadm -a -t 192.168.100.1:80 -r 127.0.0.1:8082 -m

root@btbcdwrobg:~# sudo ipvsadm -Ln --stats
IP Virtual Server version 1.2.1 (size=4096)
Prot LocalAddress:Port               Conns   InPkts  OutPkts  InBytes OutBytes
  -> RemoteAddress:Port
TCP  192.168.100.1:80                   15       49       49     3200     4083
  -> 127.0.0.1:8081                      7       41       41     2720     3763
  -> 127.0.0.1:8082                      8        8        8      480      320
```

### Делаем несколько запросов

```
root@btbcdwrobg:~# for i in {1..5}; do
    curl -s http://192.168.100.1/ > /dev/null
done
root@btbcdwrobg:~# sudo ipvsadm -Ln --stats

=== Статистика ПОСЛЕ тестирования ===
IP Virtual Server version 1.2.1 (size=4096)
Prot LocalAddress:Port               Conns   InPkts  OutPkts  InBytes OutBytes
-> RemoteAddress:Port
TCP  192.168.100.1:80                   20       79       79     5180     6824
-> 127.0.0.1:8081                     10       59       59     3908     5398
-> 127.0.0.1:8082                     10       20       20     1272     1426
```

### Видим, что наши 5 запросов распределились по бекендам (+3 и +2 запроса)
