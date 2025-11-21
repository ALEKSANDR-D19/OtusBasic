# Лабраторная работа - Настройка DHCPv6 

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab8-1.PNG)

# Задачи:
  * Создание сети и настройка основных параметров устройства
  * Проверка назначения адреса SLAAC от R1
  * Настройка и проверка сервера DHCPv6 без гражданства на R1
  * Настройка и проверка состояния DHCPv6 сервера на R1
  * Настройка и проверка DHCPv6 Relay на R2

# Выполнение:

  * Создание сети и настройка основных параментров устройства
    * Создаём сеть согласно топологии и настраиваем базовые параметры каждого маршкрутизатора

      ![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab8-2.PNG)

```
enable

clock set 17:30:00 21 nov 2025

configure

no ip domain-lookup

hostname R1

banner motd #!!!!STOP!!!!!#

int range g0/0/0, fa0/0/1
no shutdown

exit

service password-encryption

enable secret class

ip domain-name otus.ru

crypto key generate rsa

2048

ip ssh version 2

username admin  secret class

username superadmin privilege 15 secret cisco

line vty 0 4

password cisco

login

login local

transport input ssh

exit

line con 0

password cisco

login

exit

ipv6 unicast-routing

int g0/0/0

ipv6 add fe80::1 link-local

ipv6 add 2001:db8:acad:2::1/64

exit

int g0/0/1

ipv6 add fe80::1 link-local

ipv6 add 2001:db8:acad:1::1/64

end

write
```

По аналогии настраиваем R2

* Настраиваем маршрут по умолчанию

 R1

```

conf

ipv6 route ::/0 2001:db8:acad:2::2

end

wr

```
R2

```
conf

ipv6 route ::/0 2001:db8:acad:2::1

end

wr

```


![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab8-3.PNG)





![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab8-4.PNG)

# Откуда взялась часть адреса с идентификатором хоста?

Cгенерировалась на основе Mac-адреса

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab8-5.PNG)


  * Настройте R1 для предоставления DHCPv6 без состояния для PC

```
conf

ipv6 dhcp pool R1-STATELESS

dns-server 2001:db8:acad::254

domain-name STATELESS.com

int g0/0/1

ipv6 nd other-config-flag

ipv6 dhcp server R1-STATELESS

end

wr

```

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab8-7.PNG)


# Настройка сервера DHCPv6 с сохранением состояния на R1


```
conf

ipv6 dhcp pool R2-STATEFUL

address prefix 2001:db8:acad:3:aaa::/80

dns-server 2001:db8:acad::254

domain-name STATEFUL.com

int g0/0/0

ipv6 dhcp server R2-STATEFUL

end

wr

```
  
  * Настройка и проверка ретрансляции DHCPv6 на R2.

Проверка  SLAAC на PC-B

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab8-8.PNG)


  * Настройка R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1

```

conf

int g0/0/1

ipv6 nd managed-config-flag

ipv6 dhcp relay destination 2001:db8:acad:2::1 g0/0/0

end

wr

```

На сколько я понял команду ipv6 dhcp relay в CPT не поддерживает 




























