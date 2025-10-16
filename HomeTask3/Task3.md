## Задание 3. Настройка фаервола/ Host Firewalling 

### С помощью iptables или nftables создайте правило, запрещающее подключения к порту 8080:
```
root@cqeunxhpza:~# sudo iptables -A INPUT -p tcp --dport 8080 -j DROP
root@cqeunxhpza:~# sudo iptables -L INPUT -n --line-numbers
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination         
1    REJECT     6    --  0.0.0.0/0            0.0.0.0/0            multiport dports 22 match-set f2b-sshd src reject-with icmp-port-unreachable
2    DROP       6    --  0.0.0.0/0            0.0.0.0/0            tcp dpt:8080
```

```
python3 -m http.server 8080

sudo tcpdump -i any port 8080 -n
tcpdump: data link type LINUX_SLL2
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on any, link-type LINUX_SLL2 (Linux cooked v2), snapshot length 262144 bytes
11:28:56.590471 lo    In  IP6 ::1.58034 > ::1.8080: Flags [S], seq 781585872, win 65476, options [mss 65476,sackOK,TS val 2499976413 ecr 0,nop,wscale 7], length 0
11:28:56.590499 lo    In  IP6 ::1.8080 > ::1.58034: Flags [R.], seq 0, ack 781585873, win 0, length 0

curl -v http://localhost:8080/
* Host localhost:8080 was resolved.
* IPv6: ::1
* IPv4: 127.0.0.1
*   Trying [::1]:8080...
* connect to ::1 port 8080 from ::1 port 58034 failed: Connection refused
*   Trying 127.0.0.1:8080...
```

Клиент отправляет SYN для установки соединения
In  IP6 ::1.58034 > ::1.8080: Flags [S]

Система отвечает RST, порт закрыт
In  IP6 ::1.8080 > ::1.58034: Flags [R.]