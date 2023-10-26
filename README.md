### Задание№1

Выполните базовую настройку всех устройств:

1.Соберите топологию согласно рисунку. Все устройства работают на OC Linux - Debian 

2.Присвоить имена в соответствии с топологией

3.Расчитайте IP-адресацию IPv4 и IPv6. Необходимо заполнить таблицу №1. При необходимости отредактируйте таблицу.

4.Пул адресов для сети офиса BRANCH - не более 16. Для IPv6 пропустите этот пункт.

5.Пул адресов для сети офиса HQ - не более 64. Для IPv6 пропустите этот пункт.

|Имя устройства| Интерфейс | IPv4        | Маска/ Префикс|  Шлюз       |
|--------------|-----------|-------------|---------------|-------------|
| ISP          | ens192    |10.12.201.100| /24           |10.10.201.254|
|              | ens224    |192.168.0.165| /30           |             |
|              | ens225    |192.168.0.161| /30           |             |
| HQ-R         | ens192    |192.168.0.1  | /25           |             |
|              | ens224    |192.168.0.166| /30           |192.168.0.165|
| BR-R         | ens192    |192.168.0.129| /27           |             |
|              | ens224    |192.168.0.162| /30           |192.168.0.161|
| HQ-SRV       | ens192    |192.168.0.2  | /25           |192.168.0.1  |
| BR-SRV       | ens192    |192.168.0.130| /27           |192.168.0.129|








Выполнение задания 
--------------------------------------------------------------------------
1-Сначала нужно прописать команду: ip a (нужна чтобы знать интерфейсы );

2-Далее с помощью команды: nano /etc/network/interfaces (заходим на файл этих интерфейсов);

3-Изменяю адреса согласно топологии и табл.адресов:

4-Сохраняю конфигурацию: Ctrl+s;

5-Выхожу из файла: Ctrl+x;

6-Также нужно перезагрузить сетевой интерфейс: systemctl restart networking;

7-Проверяем адреса с помощью команды: ip a.




