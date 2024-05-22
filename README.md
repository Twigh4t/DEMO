# DEMO
https://docs.google.com/document/d/1Mf6mRFieZmf3cWS8VsheKH3B7QdgvI89jPS-w5HHtHA/edit
# МАСКИ ВОЗМОЖНО БУДУТ ДРУГИЕ

# https://www.youtube.com/watch?v=D9nF4YB5NRE п 1.1

# https://www.youtube.com/watch?v=56o8LY2fl5I п 1.2

# https://www.youtube.com/watch?v=6HOqEDV4fXM п 1.3

# https://www.youtube.com/watch?v=QEVgk0ZHtTA п 1.4

# https://www.youtube.com/watch?v=MVCkT8nM9Bc п 1.5

# https://www.youtube.com/watch?v=dMWsAg8fI90 п 1.6

# https://www.youtube.com/watch?v=_9wCHH9U070 п 2.1

# https://youtu.be/jhVxV-VJwVE?si=vajg-yfW0ZnMsb7i п 2.2

# CLI (AltLinux) КЛИЕНТ

# HQ-R и BR-R (vESR) ЭТО РОУТЕРЫ

# HQ-SRV и BR-SRV (AltServer) ЭТО СЕРВЕРЫ

# ЗАДАНИЕ 1 МОДУЛЬ 1

# Пункт 1

# А)

# BR-R (HQ-R)

configure - вход в режим конфигурации

hostname BR-R(HQ-R) - в режиме конфигурации меняет имя устройства

exit

comm - всегда после выхода писать чтоб сохранить

confirm - подтвердить после comm

# ——

# CLI (/BR-SRV/HQ-SRV)

Запускаем консоль

su - зайти от имени суперпользователя

hostnamectl set-hostname CLI (/BR-SRV/HQ-SRV)
exec bash
(ISP не меняем)

# Б)

# CLI (Графическая оболочка)

Параметр соединение

Параметры IPv4 - вручную

адрес 3.3.3.2 маска сети 30 шлюз 3.3.3.1

Серверы DNS 8.8.8.8

# терминал:

ip a - показывает все ip на устройстве 

ping 3.3.3.1

ping ya.ru

# ——

# HQ-R

configure

interface gigabitethernet 1/0/1 - вход в интерфейс который смотрит в сторону ISP

ip address 1.1.1.2/30  - (в интерфейсе) меняет ip

ip firewall disable - (в интерфейсе) отключает фаерволл

ex

comm

confirm

ping 1.1.1.1

# —Второй интерфейс HQ-R—

configure

interface gigabitethernet 1/0/2

ip address 192.168.100.1/26

ip firewall disable

ex

comm

confirm

# ——

# BR-R

configure

interface gigabitethernet 1/0/1

ip address 2.2.2.2/30

ip firewall disable

exit

# —Второй интерфейс BR-R—

configure

interface gigabitethernet 1/0/2

ip address 172.16.100.1/28

ip firewall disable

ex

comm

confirm - подтвердить после comm

ping 2.2.2.1

# ——

# BR-SRV

(IP) echo 172.16.100.2/28 > /etc/net/ifaces/ens192/ipv4address

(шлюз) echo via default 172.16.100.1 > /etc/net/ifaces/ens192/ipv4route

(днс сервер) echo nameserver 192.168.100.2 > /etc/net/ifaces/ens192/resolv.conf

ip a - показывает все ip на устройстве 

systemctl restart network

ip a

ping 172.16.100.1


ЗАДАНИЕ 2 МОДУЛЬ 1

HQ-R BR-R

NAT

configure

object-group network LOCAL_NET (локалка SRV)

ip address-range 192.168.100.1-192.168.100.62

exit



object-group network PUBLIC_POOL

ip address 1.1.1.2 (Внешний ip адрес роутера с ISP)

exit



nat source

pool TRANSLATE_ADDRESS

ip address 1.1.1.2

exit



ruleset SNAT

to interface gigabitethernet 1/0/1

rule 1

match source-address LOCAL_NET

action source-nat pool TRANSLATE_ADDRESS

enable

exit



ip route 0.0.0.0/0 1.1.1.1

exit

comm

confirm



ping 8.8.8.8

ping 8.8.8.8 source ip 192.168.100.1

show ip nat translations



Динамическая маршрутизация OSPF

HQ-R/BR-R

configure



router ospf

router-id 1.1.1.1

area 1.1.1.1




network 192.168.1.0/26

enable


exit
enable

exit



Настройка туннеля

configure

tunnel gre 10

ip firewall disable



local address 1.1.1.2

remote address 2.2.2.2

ip address 10.10.10.1/30

mtu 1426

ttl 18
enable

exit

comm

confirm

ЗАДАНИЕ 3 МОДУЛЬ 1

Настройка DHCP


configure

ip dhcp-server - активация dhcp сервера

ip dhcp-server pool LAN_HQ - создание пула сети

network <указываем сеть HQ>

address-range <пишем начало пула> - <пишем конец пула>

excluded-address-range <вписываем ip-шник шлюза>

excluded-address-range <вписываем ip-шник который хотим зарезервировать>

address <прописываем IP-шник для присваивания HQ-SRV> mac-address <Прописываем mac адрес HQ-SRV>

default-router <прописываем IP шлюз HQ-R>

dns-server <IP-шник dns сервера HQ-SRV>

exit

exit

comm

confirm

ЗАДАНИЕ 4 МОДУЛЬ 1

CLI

Меню→центр управления→центр управления системой→вводим пароль→Локальные учетные записи→Вбиваем имя учётной записи→Создать→в комментарии написать имя учётки→дальше ниже вводим пароль(как в задании)→Применить.

HQ-SRV(BR-SRV-2 учетки)

useradd (имя) -с “(имя)”

passwd (имя которое вводили при создании)

(Enter после вводим пароль)

usermod -aG wheel (имя которое вводили при создании) - Даём админку пользователю

cat /etc/paswd - проверка создания пользователя в самом низу ваш пользователь

——

HQ-R

configure

username admin

password (пароль по заданию)

exit

exit

comm

confirm

show users accounts - проверка 

при 

——

BR-R

configure

username (имя)

password (пароль)

privilege 15

exit

exit

commit

confirm







ЗАДАНИЕ 5 МОДУЛЬ 1

CLI

su

apt-get update

apt-get install iperf3

iperf3 -s (на серваке(HQ-SRV/BR-SRV))

iperf3 -c <вписываем IP сервака>

iperf3 -R -c обратная проверка <вписываем IP сервака>

ЗАДАНИЕ 6 МОДУЛЬ 1

HQ-R

configure

archive

type local

count-backup 30

time-period 1440

by-commitexit

comm

confirm

dir flash:backup/



BR-R

configure

archive

type local

count-backup 30

time-period 1440

by-commitexit

comm

confirm

dir flash:backup/



ЗАДАНИЕ 7 МОДУЛЬ 1

apt-get install openssh-server

cd /etc/openssh (раскомментировать #Port 22 и заменить порт на 2222)

systemctl restart sshd

Запускаем PUTTY вводим ip и порт и пытаемся подключится для проверки



ЗАДАНИЕ 8 МОДУЛЬ 1

cd /etc/openssh

пишем в свободной строке AllowUsers login@ip …(Перечисляем пользователей которые должны подключаться(Все кроме CLI))

systemctl restart sshd

ЗАДАНИЕ 1 МОДУЛЬ 2

HQ-SRV

vim /etc/bind/options.conf - заходим в файл 

нажимаем insert на клавиатуре,в строчке listen-on и в listen-on-v6 меняем айпи который там на any. далее  раскомменчиваем и вместо only пишем first в скобки forwarders пишем 77.88.8.8;. Эту строку  комментируем(перед текстом  //).

далее  раскомменчиваем и вместо localnets;

пишем any; . сохраняем конфигурацию и выходим.

systemctl restart bind - перезапускаем bind после выхода





ЗАДАНИЕ 2 МОДУЛЬ 2

configure 

interface loopback 1 

ip address 1.2.3.4./32

exit 

router ospf 10 

area 1.1.1.1

network 1.2.3.4/32

exit 

exit

exit

comm

confirm 

configure 

clock timezone gmt +3












