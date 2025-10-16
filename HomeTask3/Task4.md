## Задание 4. Аппаратное ускорение сетевого трафика 

### С помощью ethtool исследуйте offload возможности вашего сетевого адаптера.
### Покажите включён ли TCP segmentation offload.
```
root@cqeunxhpza:~# sudo ethtool -k eth0 | grep -i tcp
tcp-segmentation-offload: on
tx-tcp-segmentation: on
tx-tcp-ecn-segmentation: on
tx-tcp-mangleid-segmentation: off
tx-tcp6-segmentation: on
```

#### Все основные параметры TSO активны

### Объясните, какую задачу решает TCP segmentation offload.
TCP segmentation offload решает проблему высокой нагрузки на CPU при передаче больших объемов данных по сети. 
Без него операционная система должна разбивать крупные пакеты данных на сегменты, соответствующие максимальному размеру передачи (MTU)
С включенным TCP segmentation offloa эти задачи переносятся на сетевой адаптер.