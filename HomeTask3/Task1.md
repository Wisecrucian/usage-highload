## Задание 1. Анализ состояний TCP-соединений

### Задание 1. Анализ состояний TCP-соединений
```
python3 -m http.server 8080
Serving HTTP on 0.0.0.0 port 8080 (http://0.0.0.0:8080/) ...
```

## Проверяйте слушающие TCP-сокеты с помощью утилиты ss. найдите сокет с вашим http сервером
```
root@cqeunxhpza:~# ss -tlnp | grep 8080
LISTEN 0      5            0.0.0.0:8080      0.0.0.0:*    users:(("python3",pid=1743,fd=3))     
```          

### Подключитесь к серверу через curl
```
root@cqeunxhpza:~# curl http://localhost:8080
<!DOCTYPE HTML>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Directory listing for /</title>
</head>
<body>
<h1>Directory listing for /</h1>
<hr>
<ul>
<li><a href=".bash_history">.bash_history</a></li>
<li><a href=".bashrc">.bashrc</a></li>
<li><a href=".cache/">.cache/</a></li>
<li><a href=".gnupg/">.gnupg/</a></li>
<li><a href=".lesshst">.lesshst</a></li>
<li><a href=".local/">.local/</a></li>
<li><a href=".profile">.profile</a></li>
<li><a href=".ssh/">.ssh/</a></li>
<li><a href="disk1.img">disk1.img</a></li>
<li><a href="shared-memory-c/">shared-memory-c/</a></li>
<li><a href="shared-memory-java/">shared-memory-java/</a></li>
<li><a href="virtual_disk.img">virtual_disk.img</a></li>
</ul>
<hr>
</body>
</html>
```

### Проанализируйте состояние TCP-сокетов для порта 8080, объясните, почему есть сокет в состоянии TIME-WAIT, его роль и почему его нельзя удалить.
```
root@cqeunxhpza:~# ss -tan | grep 8080
LISTEN    0      5            0.0.0.0:8080        0.0.0.0:*           
TIME-WAIT 0      0          127.0.0.1:8080      127.0.0.1:42020      
``` 


### Состояние TIME-WAIT появляется на стороне, которая инициировала закрытие соединения. Оно нужно для гарантии доставки последних пакетов.

Его нельзя удалить, потому что:
Это часть TCP-протокола, определенная в RFC
Преждевременное удаление может привести к потере данных и проблемам в новых соединениях

### Проблемы большого количества TIME-WAIT сокетов
- Исчерпание доступных портов для исходящих соединений
- Ограничение на максимальное количество файловых дескрипторов
