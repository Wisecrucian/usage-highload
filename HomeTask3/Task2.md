## Задание 2. Динамическая маршрутизация с BIRD

### Создайте dummy-интерфейс с адресом 192.168.14.88/32, назовите его service_0:
```
root@cqeunxhpza:~# sudo nano /etc/bird/bird.conf
root@cqeunxhpza:~# sudo ip link add service_0 type dummy
root@cqeunxhpza:~#  sudo ip addr add 192.168.14.88/32 dev service_0
root@cqeunxhpza:~# sudo ip link set service_0 up
root@cqeunxhpza:~# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:46:07:d0:36:97 brd ff:ff:ff:ff:ff:ff
    altname enp0s3
    altname ens3
    inet 45.80.70.225/32 brd 45.80.70.225 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::246:7ff:fed0:3697/64 scope link 
       valid_lft forever preferred_lft forever
3: service_0: <BROADCAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN group default qlen 1000
    link/ether 2a:2c:92:9d:d7:9d brd ff:ff:ff:ff:ff:ff
    inet 192.168.14.88/32 scope global service_0
       valid_lft forever preferred_lft forever
    inet6 fe80::282c:92ff:fe9d:d79d/64 scope link 
       valid_lft forever preferred_lft forever
```

### Добавляем в конфиг bird

```
protocol direct {
interface "service_*";

filter export_filter {
    if (net ~ 192.168.14.0/24 && net.len = 32 && ifname ~ "service_*") then {
        accept;
    }
    reject;
} 

protocol rip {
    ipv4 {
        import all;
        export filter export_filter;
    };
    interface "eth0" {
        mode multicast;
        update time 30;
    };
}
```

### Рестартим и смотрим конфиг
```
root@cqeunxhpza:~# sudo systemctl restart bird
root@cqeunxhpza:~# sudo birdc show route export rip1
BIRD 2.14 ready.
Table master4:
192.168.14.88/32     unicast [direct1 10:46:45.315] * (240)
dev service_0
```

### Создаем еще три интерфейса
```
root@cqeunxhpza:~# sudo ip link add service_1 type dummy
root@cqeunxhpza:~# sudo ip addr add 192.168.14.1/30 dev service_1
root@cqeunxhpza:~# sudo ip link set service_1 up
root@cqeunxhpza:~# sudo ip link add service_2 type dummy
root@cqeunxhpza:~# sudo ip addr add 192.168.10.4/32 dev service_2
root@cqeunxhpza:~# sudo ip link set service_2 up
root@cqeunxhpza:~# sudo ip link add srv_1 type dummy
root@cqeunxhpza:~# sudo ip addr add 192.168.14.4/32 dev srv_1
root@cqeunxhpza:~# sudo ip link set srv_1 up
```

```
root@cqeunxhpza:~# sudo tcpdump -i eth0 -n port 520 -v -X
tcpdump: listening on eth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
10:48:07.887665 IP (tos 0xc0, ttl 1, id 36779, offset 0, flags [none], proto UDP (17), length 52)
45.80.70.225.520 > 224.0.0.9.520:
RIPv2, Response, length: 24, routes: 1 or less
AFI IPv4,   192.168.14.88/32, tag 0x0000, metric: 1, next-hop: self
0x0000:  45c0 0034 8fab 0000 0111 d513 2d50 46e1  E..4........-PF.
0x0010:  e000 0009 0208 0208 0020 546c 0202 0000  ..........Tl....
0x0020:  0002 0000 c0a8 0e4e ffff ffff 0000 0000  .......N........
0x0030:  0000 0001                                ....
```

