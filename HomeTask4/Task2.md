# Задание 2. DNS Load Balancing с помощью dnsmasq

### Подготовка конфига 

```
root@btbcdwrobg:~# sudo nano /etc/dnsmasq.d/my-app.conf
root@btbcdwrobg:~# sudo systemctl stop systemd-resolved
root@btbcdwrobg:~# sudo systemctl start dnsmasq


root@btbcdwrobg:~# sudo mv /etc/resolv.conf /etc/resolv.conf.backup
root@btbcdwrobg:~# echo "nameserver 127.0.0.1" | sudo tee /etc/resolv.conf
nameserver 127.0.0.1
root@btbcdwrobg:~# dig my-awesome-highload-app.local

; <<>> DiG 9.18.39-0ubuntu0.24.04.2-Ubuntu <<>> my-awesome-highload-app.local
;; global options: +cmd
;; Got answer:
;; WARNING: .local is reserved for Multicast DNS
;; You are currently testing what happens when an mDNS query is leaked to DNS
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 45674
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;my-awesome-highload-app.local.	IN	A

;; ANSWER SECTION:
my-awesome-highload-app.local. 0 IN	A	127.0.0.2
my-awesome-highload-app.local. 0 IN	A	127.0.0.1

;; Query time: 0 msec
;; SERVER: 127.0.0.1#53(127.0.0.1) (UDP)
;; WHEN: Tue Oct 28 19:10:03 UTC 2025
;; MSG SIZE  rcvd: 90
```

### Видим что для my-awesome-highload-app.local есть две А записи с IP 127.0.0.1, 127.0.0.2

### Если один из backend-серверов перестанет отвечать, DNS так же будет возвращать 2 записи, но половина запросов будет падать