### Задание№1

Выполните базовую настройку всех устройств:

1.Соберите топологию согласно рисунку. Все устройства работают на OC Linux - Debian 

2.Присвоить имена в соответствии с топологией

3.Расчитайте IP-адресацию IPv4 и IPv6. Необходимо заполнить таблицу №1. При необходимости отредактируйте таблицу.

4.Пул адресов для сети офиса BRANCH - не более 16. Для IPv6 пропустите этот пункт.

5.Пул адресов для сети офиса HQ - не более 64. Для IPv6 пропустите этот пункт.

|Имя устройства| Интерфейс | IPv4        | Маска/ Префикс|  Шлюз       |
|--------------|-----------|-------------|---------------|-------------|
| ISP          | ens192    |10.12.16.1   | /24           |10.12.16.254 |
|              | ens256    |192.168.0.165| /30           |             |
|              | ens224    |192.168.0.161| /30           |             |
| HQ-R         | ens192    |192.168.0.1  | /25           |             |
|              | ens224    |192.168.0.166| /30           |192.168.0.165|
| BR-R         | ens192    |192.168.0.129| /26           |             |
|              | ens224    |192.168.0.162| /30           |192.168.0.161|
| HQ-SRV       | ens192    |192.168.0.20 | /25           |192.168.0.1  |
| BR-SRV       | ens192    |192.168.0.140| /26           |192.168.0.129|

## Топология

![image](https://github.com/Julia666666666666666666/demo2024/assets/148867585/c9672a0b-8f42-4796-b03a-9a32f251e2fa)


Выполнение задания 
--------------------------------------------------------------------------
Создала 4-vlana и 4-net (HQ-SRV-2, BR-SRV-4, HQ-R-2-1,  BR-R-4-3, ISP-1-3)

1-Сначала нужно прописать команду:

```
ip a (нужна чтобы знать интерфейсы );
```
2-Далее с помощью команды: 
```
nano /etc/network/interfaces (заходим на файл этих интерфейсов);
```
3-Изменяю адреса согласно топологии и табл.адресов:

![image](https://github.com/Julia666666666666666666/demo2024/assets/148867585/8ea5452f-ecae-4729-8019-e487786bae4a)


4-Сохраняю конфигурацию: 
```
Ctrl+s;
```
5-Выхожу из файла: 
```
Ctrl+x;
```
6-Также нужно перезагрузить сетевой интерфейс: 
```
systemctl restart networking;
```
7-Проверяем адреса с помощью команды: 
```
ip a.
```
### Настройка NAT

На устройстве  ISP:
```
apt install ip iptables
```
Далее заходим в конф.файл:
```
nano /etc/sysctl.conf
```
После убираем # из строки:
```
#net.ipv4.ip_forward=1
```
Проверяем командой:
```
sysctl -p
```
Далее пишем:
```
iptables -A POSTROUTING -t nat -j MASQUER
```
Создаем файл для автом. запуска NAT после перезагрузки устройства:
```
nano /etc/network/if-pre-up.d/nat
```
Пишем в файл: 
```
#!/bin/sh
/sbin/iptables -A POSTROUTING -t nat -j
MASQUERADE
```
Задаем права для файла:
```
chmod +x /etc/network/if-pre-up.d/nat
```
Повторяем эту работу на BR-R & HQ-R

## Задание 1.2

1.2 Настройте внутреннюю динамическую маршрутизацию по средствам FRR. Выберите и обоснуйте выбор протокола динамической маршрутизации из расчёта, что в дальнейшем сеть будет масштабироваться.

1.2.1 Составьте топологию сети L3.
## Выполнение задания 

На устройстве ISP устанавливаем ffr:  
```
apt update 

apt install frr
```
После отк файл: 
```
nano /etc/frr/daemons
```
Меняем значения: 
```
ospfd=yes 
```
Перезагружаем: 
```
systemctl restart frr
```
Переходим в среду роутера:
```
vtysh
```
Далее настраиваем frr для ближ. устр:
```
conf t

router ospf 

net  192.168.0.166/30 area 0

net 192.168.0.162/30 area 0

sh ip ospf neighbor
```
После настраиваем  FRR на HQ-R & BR-R

Проверяем настройки пингом:
```
HQ-SRV c BR-SRV

BR-SRV c HQ-SRV
```
### Задание 1.3
Настроить автом. распределение IP-адресов на HQ-R. У сервера должен быть зарезервирован адрес.
## Выполнение задания

Скачиваем dhcp:
```
apt install isc-dhcp-server
```
Далее переходим в режим конфигурации:
```
nano /etc/default/isc-dhcp-server
```
Нужно указать интерф. сети, куда будут идти IP-адреса
```
INTERFACESV4="ens224"
```
Теперь нужно настроить раздачу адресов:
```
nano /etc/dhcp/dhcpd.conf
```
Пишем:
```
subnet 192.168.0.0 netmask 255.255.255.128{
range 192.168.0.4 192.168.0.125;
option routers 192.168.0.2;}
```
Теперь нужно перезагрузить и вкл. dhcp:
```
systemctl restart isc-dhcp-server

system enable isc-dhcp-server
```
Настройка на HQ-SRV

