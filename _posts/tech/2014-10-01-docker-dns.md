---
layout: post
title: "DNS внутри контейнера"
categories: blog
excerpt: "Поднимаем DNS сервер в контейнере"
author: wonderbeat
tags: [Docker]
image:
  feature: backgrounds/wg_blurred_backgrounds_13.jpg
  credit: WeGraphics
  creditlink: http://wegraphics.net/downloads/free-ultimate-blurred-background-pack/
comments: true
share: true
---

Сегодня расскажу как поднять свой DNS сервер в Docker контейнере.  
Уже почти год как сам пользуюсь таким в [Digital Ocean](https://www.digitalocean.com/?refcode=06c4ef09e5cc). Приватной сети у данного провайдера все еще нет. Поэтому все мои машинки соединены через OpenVPN и в этот же VPN прокинут DNS сервер, через который осуществляется service location. О такой конфигурации и пойдет речь.

## Собираем образ
Cоберем образ сервера с помощью Docker.   

~~~ bash
## Dockerfile
FROM ubuntu
MAINTAINER Denis Golovachev

RUN apt-get update
RUN apt-get install -y bind9 

CMD usr/sbin/named -c /etc/bind/named.conf -f
~~~

~~~ bash
docker build -t server/bind .
~~~
И заберем из контейнера папку с конфигурацией DNS сервера.

~~~ bash
bindId=$(docker run -d serer/bind)
docker cp $bindID:/etc/bind ./bind
docker stop $bindID
~~~ 
Теперь мы можем отредактировать конфигурацию DNS сервера

## DNS Server
Добавим своих DNS записей. Для этого создадим свою доменную зону `.tapcat`

~~~ bash
# bind/named.conf.local - Добавим запись вида
include "/etc/bind/named.conf.tapcat";
# bind/named.conf.tapcat - Создадим файл
zone "tapcat" {
        type master;
        file "/etc/bind/db.tapcat";
};
# bind/db.tapcat - Создадим файл. За доп опциями можно обратиться в man bind
$TTL 10
$ORIGIN tapcat.

@ IN SOA        ns1.tapcat hostmaster.tapcat. (
                    2014040901 ; Serial
                    2h ; Refresh
                    1h ; Retry
                    1w ; Expire
                    2h ; Negative Cache TTL
)

@               IN      NS      ns1.    ; Master

ns1.tapcat.         IN       A       10.8.0.19
master.tapcat.      IN       A       10.8.0.19

cname.tapcat.       IN      CNAME   master.tapcat.
cname2.tapcat.      IN      CNAME   master.tapcat.
~~~
Запускаем с нашей конфигурацией

~~~ bash
docker run -d -p 53:53/udp -v /home/postengineering/bind9/conf:/etc/bind -c 25 -m 30m server/bind
~~~
Готово. У нас теперь свой ДНС сервер.  Давайте прокинем его в OpenVPN. 

## OpenVPN
Машина, на которой установлен bind должна находиться в VPN сети. После этого мы редактируем конфиг VPN сервера

~~~ bash 
# /etc/openvpn/server.conf - добавим строчку
push "dhcp-option DNS 10.8.0.3"
# где 10.8.0.3 - адрес машины с VPN сервером
~~~
Изменяем конфигурацию наших VPN клиентов

~~~ bash
# etc/openvpn/client.conf - добавляем
up /etc/openvpn/update-resolv-conf
down /etc/openvpn/update-resolv-conf
~~~
И кладем данный update-resolv-conf рядышком. Сам скрипт лежит у вас в репозитории пакетов или гуглится.  
Перезапускаем bind:

~~~ bash
docker run -d -p $VPN_IP:53:53/udp -v /home/postengineering/bind9/conf:/etc/bind -c 25 -m 30m server/bind
~~~

Готово. Теперь в нашей внутренней сети есть свой маленький DNS сервер.

