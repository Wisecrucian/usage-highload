# Задание 1. Создание Server Pool для backend

### Создание каталогов и индекс файлов
```
root@btbcdwrobg:~# mkdir -p ~/backend1 ~/backend2
root@btbcdwrobg:~# echo '<h1>Response from Backend Server 1</h1>' > ~/backend1/index.html
root@btbcdwrobg:~# echo '<h2>*** Response from Backend Server 2 ***</h2>' > ~/backend2/index.html
```

### Запуск и проверка
```
root@btbcdwrobg:~# cd ~/backend1
root@btbcdwrobg:~/backend1# python3 -m http.server 8081
Serving HTTP on 0.0.0.0 port 8081 (http://0.0.0.0:8081/) ...
^[[A127.0.0.1 - - [28/Oct/2025 19:03:57] "GET / HTTP/1.1" 200 -

root@btbcdwrobg:~/backend2# python3 -m http.server 8082
Serving HTTP on 0.0.0.0 port 8082 (http://0.0.0.0:8082/) ...
127.0.0.1 - - [28/Oct/2025 19:04:01] "GET / HTTP/1.1" 200 -

root@btbcdwrobg:~# curl http://localhost:8081
<h1>Response from Backend Server 1</h1>
root@btbcdwrobg:~# curl http://localhost:8082
<h2>*** Response from Backend Server 2 ***</h2>
```