---
layout: post
category : tech
tagline: "Docker OpenVPN"
tags : [Docker]
excerpt: На днях дома отключился интернет. Оказалось, что к провайдеру пришли правохраниетльные органы и забрали сетевое оборудование. Перевел весь домашний трафик через Голландию. Чтоб неповадно было. Сейчас расскажу как.
---

Для понижения уровня паранои нам потребуются:
* Сервер за границей
* Docker

## Сервер
Нужна машина с 256мб ОЗУ. Ничего сверхестественного.
Порекомендовать могу DigitalOcean.
5$ в месяц. Все сервера на SSD. Быстрый хелпдеск.

## Docker
Docker - обертка над lxc контейнерами. Если просто - то это инструмент, позволяющий быстро создавать и управлять сверхлегкими виртуальными машинами.

Зачем именно Docker? Можно было развернуть OpenVpn сервер прямо на виртуальной машине.
Наверно, хотелось разобраться с данным софтом. Многим кажется, что Docker это прорыв 2013 года. Посмотрим. Кроме того, в итоге у меня получился контейнер в 80мб, который Я могу переносить с машины на машину и запускать одной командой. Никакой донастройки!

Докер мне попался на глаза, как только Я решил сделать Continues Delivery для нового проекта.
CD сделал. Скоро напишу об этом.
Очень понравилось то, что при создании виртуальной машины в DigitalOcean можно ткнуть галочку "Docker" и готово. Машина с установленным и настроенным Docker-ом готова.

## Code
Начинаем. Для базы выберем образ убунты
```shell
docker run -privileged -i -t ubuntu:precise /bin/bash # i - интерактивный режим. Попадаем в консоль
apt-get install -y iptables wget # iptables - для openVpn; wget - чтобы скачать openVPN AS

# Ставим OpenVPN
wget http://swupdate.openvpn.org/as/openvpn-as-1.8.5-Ubuntu12.amd_64.deb
dpkg -i openvpn-as-1.8.5-Ubuntu12.amd_64.deb

# tun утсройства в контейнере нет, но ведь оно прокинуто снаружи. Создадим его руками
mkdir -p /dev/net
mknod /dev/net/tun c 10 200

# ставим пароль админа и запускаем сервис
passwd openvpn
/etc/init.d/openvpnas start

# создаем пользователей. OpenVPN их подцепит
useradd borov
passwd borov

exit
```

Готово. Осталось сохранить контейнер.

```shell
# Самый верхний ID - это наш
docker ps -a

docker commit $ID -t borov/openvpn
```

Образ готов.
Запускаем

```shell
  docker run -d -privileged -p 1194:1194/udp -p 443:443/tcp -p 943:943/tcp -t borov/openvpn-server /bin/bash -c "service openvpnas start && tail -f /var/log/openvpnas.log"
```

Готово! Теперь по адресу `https://your-vm/` откроется консоль OpenVPN через которую можно подключиться к сети или зайти с административного аккаунта.
И больше никаких настроек!

Месяц назад Я узнал об интересном пр

apt-get install -y iptables wget
    2  wget http://swupdate.openvpn.org/as/openvpn-as-1.8.5-Ubuntu12.amd_64.deb
    3  dpkg -i openvpn-as-1.8.5-Ubuntu12.amd_64.deb
    4  passwd openvpn
    5  exit
    6  ls
    7  mkdir -p /dev/net
    8  mknod /dev/net/tun c 10 200
    9  /etc/init.d/openvpnas start
   10  aptitude search openvpn
   11  apt-get search openvpn
   12  ls
   13  dpkg -i openvpn-as-1.8.5-Ubuntu12.amd_64.deb
   14  apt-get install ifconfig
   15  apt-get search net-tools
   16  apt-get install net-tools
   17  ifconfig
   18  useradd borov
   19  passwd borov
   20  passwd borov
   21  /etc/init.d/openvpnas restart
   22  useradd ratsam
   23  passwd ratsam

   docker run -i -privileged -p 1194:1194/udp -p 443:443/tcp -p 943:943/tcp -t borov/openvpn-server /bin/bash

   docker run -d -privileged -p 1194:1194/udp -p 443:443/tcp -p 943:943/tcp -t borov/openvpn-server /bin/bash -c "service openvpnas start && tail -f /var/log/openvpnas.log"