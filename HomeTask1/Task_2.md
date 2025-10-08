# Задание 2: Наблюдение за VFS

### Трассировка команды `cat /etc/os-release > /dev/null`

```
root@cqeunxhpza:~# strace -e trace=openat,read,write,close cat /etc/os-release > /dev/null
openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
close(3)                                = 0
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\220\243\2\0\0\0\0\0"..., 832) = 832
close(3)                                = 0
openat(AT_FDCWD, "/usr/lib/locale/locale-archive", O_RDONLY|O_CLOEXEC) = 3
close(3)                                = 0
openat(AT_FDCWD, "/etc/os-release", O_RDONLY) = 3
read(3, "PRETTY_NAME=\"Ubuntu 24.04.3 LTS\""..., 131072) = 400
write(1, "PRETTY_NAME=\"Ubuntu 24.04.3 LTS\""..., 400) = 400
read(3, "", 131072)                     = 0
close(3)                                = 0
close(1)                                = 0
close(2)                                = 0
+++ exited with 0 +++
```
Открываемые файлы:

/etc/ld.so.cache - кеш библиотек для быстрой загрузки

/lib/x86_64-linux-gnu/libc.so.6 - стандартная библиотека C

/usr/lib/locale/locale-archive - данные локализации

/etc/os-release - целевой файл (читает 400 байт)

Про вывод:
Вызов write есть в трассировке:

bash
write(1, "PRETTY_NAME=\"Ubuntu 24.04.3 LTS\""..., 400) = 400
Но вывод не виден в терминале, потому что оболочка перенаправила stdout (дескриптор 1) в /dev/null до запуска cat. Данные уходят "в никуда".