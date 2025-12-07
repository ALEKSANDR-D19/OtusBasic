# Лабораторная работа - Реализация DHCPv4 

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab8v4-1.PNG)

# Задачи:

  * Создание сети и настройка основных параметров устройства
  * Настройка и проверка двух серверов DHCPv4 на R1
  * Настройка и проверка DHCP-ретрансляции на R2

# Выполнение:

  * Создание сети и настройка основных параметров устройства

Создаём схемы адресации

192.168.1.0/24

| Подсеть | Количество хостов | Сетевой адрес | 1-й адрес | Последний адрес | Маска | Широковещательный адрес |
| ----------- |-----------| :-----------: | ----------- | ----------- | ----------- | ----------- |
| A           | 58            | 192.168.1.0 | 192.168.1.1 | 192.168.1.62 | 192.168.1.62 | 255.255.255.192 |
| B           | 28            | 192.168.1.64 | 192.168.1.65 | 192.168.1.94 | 192.168.1.95 | 255.255.255.224 |
| C           | 12            | 192.168.1.96 | 192.168.1.97 | 192.168.1.110 | 192.168.1.111 | 255.255.255.240 |

Расчёт произведён при помощи Ip калькулятора

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab8v4-2.PNG)

  * Базовая настройка маршрутизаторов

```
enable

clock set 16:30:00 07 dec 2025

configure

no ip domain-lookup

hostname R1(R2)

banner motd #!!!!STOP!!!!!#

int range g0/0/1, g0/0/0

no shutdown

exit

service password-encryption

enable secret class

line vty 0 4

password cisco

login

login local

end

write

``` 

  * Настройка маршрутизации между сетями VLAN на маршрутизаторе R1

```

int g0/0/1.100

encapsulation dot1q 100

ip add 192.168.1.1 255.255.255.192

des users

int g0/0/1.200

encapsulation dot1q 200

ip add 192.168.1.65 255.255.255.224

des management

int g0/0/1.1000

des native

encapsulation dot1q 1000 native

end

wr

```

  * Проверка работоспособности  подинтерфейсов:

```

show ip int br

```

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab8v4-3.PNG)


  * Настройка G0/1 на R2 и G0/0/0 для обоих маршрутизаторов

R2

```
int g0/0/1

ip add 192.168.1.97 255.255.255.240

int g0/0/0

ip add 10.0.0.2 255.255.255.252

exit

ip route 0.0.0.0 0.0.0.0 10.0.0.1


wr

```

R1

```

int g0/0/0

ip add 10.0.0.1 255.255.255.252

exit

ip route 0.0.0.0 0.0.0.0 10.0.0.2

wr

```

  * Проверка работоспособностти статической маршрутизации

R1  

```

ping 192.168.1.97

```

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab8v4-4.PNG)


  * Настройка коммутаторов

 Базовые настройки аналогичны настройкам маршрутизатора, их указывать не буду, сразу переходим к настройке VLAN 

S1

 ```

vlan 100

name clients

vlan 200

name management

vlan 999

name Parking_Lot

vlan 1000

name native

exit

int vlan 200

ip add 192.168.1.66 255.255.255.224

exit

ip default-gateway 192.168.1.65

int ra fa0/1-4, fa0/7-24, g0/1-2

swi mode access

swi access vlan 999

shut

exit

int fa0/6

swi mod access

swi access vlan 100

end

wr

```

S2

```

int vlan 1

ip add 192.168.1.98 255.255.255.240

no shut

exit

ip default-gateway 192.168.1.97

int ra fa0/1-4, fa0/6-17, fa0/19-24, g0/1-2

shut

end

wr

```

  * Проверим что Vlan назначины на нправильные интерфейсы

```
show vlan br
```

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab8v4-5.PNG)

Почему интерфейс F0/5 указан в VLAN 1?

Потому что он не назначен не на один Vlan


  * Настройка интерфейса S1 F0/5 в качестве транка 802.1Q.

```

int fa0/5

swi mode trunk

swi trunk native vlan 1000

swi trunk allowed vlan 100,200,1000

end

wr

show int trunk

```

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab8v4-6.PNG)

Какой IP-адрес был бы у ПК, если бы он был подключен к сети с помощью DHCP?

В этом случае пк получит адрес из подсети 192.168.1.1 255.255.255.192

  * # Настройка и проверка двух серверов DHCPv4 на R1

R1

```
ip dhcp ex 192.168.1.1 192.168.1.5

ip dhcp ex 192.168.1.97 192.168.1.101

ip dhcp pool vlan100

network 192.168.1.0 255.255.255.192

default-router 192.168.1.1

domain-name CCNA-lab.com

exit

ip dhcp pool R2_Client_LAN

network 192.168.1.96 255.255.255.240

default-router 192.168.1.97

domain-name CCNA-lab.com

end

wr

```


cpt не поддерживает команду `lease`, поэтому время аренды установить не получится


  * Проверка конфигурации сервера DHCPv4

```
show ip dhcp pool

show ip dhcp binding

show ip dhcp server statistics 
```

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab8v4-7.PNG)

`show ip dhcp server statistics` отсутствует.

  * Попытка получить IP-адрес от DHCP на PC-A

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab8v4-8.PNG)

  * # Настройка и проверка DHCP-ретрансляции на R2

R2

```

int g0/0/1

ip helper-add 10.0.0.1

end

wr

```

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab8v4-9.PNG)






































 




































