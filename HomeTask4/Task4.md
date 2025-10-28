# Задание 4. Балансировка L7 с помощью NGINX

### Настройка nginx
```
http {
    upstream backend_servers {
        server 127.0.0.1:8081 max_fails=7 fail_timeout=10s;
        server 127.0.0.1:8082 backup;
    }
    server {
        listen 127.0.0.1:8888;
        server_name localhost;
        location / {
            proxy_pass http://backend_servers;
            proxy_set_header X-high-load-test 123;
        }
    }
}
```

### Запуск tshark
```
root@btbcdwrobg:~# sudo tshark -i lo -Y "tcp.port == 8088 and http.request" -V
root@btbcdwrobg:~# curl http://127.0.0.1:8888/
```

```
Hypertext Transfer Protocol
    GET / HTTP/1.0\r\n
        [Expert Info (Chat/Sequence): GET / HTTP/1.0\r\n]
            [GET / HTTP/1.0\r\n]
            [Severity level: Chat]
            [Group: Sequence]
        Request Method: GET
        Request URI: /
        Request Version: HTTP/1.0
    X-high-load-test: 123\r\n
    Host: 127.0.0.1\r\n
    X-Real-IP: 127.0.0.1\r\n
    X-Forwarded-For: 127.0.0.1\r\n
    X-Forwarded-Proto: http\r\n
    Connection: close\r\n
    User-Agent: curl/8.5.0\r\n
    Accept: */*\r\n
    \r\n
    [Full request URI: http://127.0.0.1/]
    [HTTP request 1/1]


root@btbcdwrobg:~# curl http://127.0.0.1:8888/
<h1>Response from Backend Server 1</h1>
```

### Хэдер успешно проставился в запрос

### После отключения бекенда2

```
root@btbcdwrobg:~# curl http://127.0.0.1:8888/
<h2>*** Response from Backend Server 2 ***</h2>

root@btbcdwrobg:~# curl -v 127.0.0.1:8888
*   Trying 127.0.0.1:8888...
* Connected to 127.0.0.1 (127.0.0.1) port 8888
> GET / HTTP/1.1
> Host: 127.0.0.1:8888
> User-Agent: curl/8.5.0
> Accept: */*
>
< HTTP/1.1 200 OK
< Server: nginx/1.24.0 (Ubuntu)
< Date: Tue, 28 Oct 2025 20:07:49 GMT
< Content-Type: text/html
< Content-Length: 48
< Connection: keep-alive
< Last-Modified: Tue, 28 Oct 2025 19:02:30 GMT
<
<h2>*** Response from Backend Server 2 ***</h2>
* Connection #0 to host 127.0.0.1 left intact
```

### Запрос перенаправился на бекенд2 и вернул ответ