# Лабораторная работа - Внедрение маршрутизации между виртуальными локальными сетями 

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab6.1.PNG)

**Таблица Vlan**

| VLAN | Имя | Назначенный интерфейс |
| ----------- |-----------| :-----------: |
| 10   | Management | S1: VLAN 10    |
|      |            | S2: VLAN 10    |
|20    |Sales       |S1: F0/6        |
|30    |Operations  |S2: F0/18       |
|999   |Parking_Lot |C1:F 0/2-4, F0/7-24, G0/1-2  |
|      |            |C2: F0/2-17, F0/19-24, G0/1-2|
|1000  |Собственная |                             |



# Задачи
  * Создание сети и настройка основных параметров устройства
  * Создание сетей VLAN и назначение портов коммутатора
  * Настройка транка 802.1Q между коммутаторами.
  * Настройка маршрутизации между сетями VLAN
  *  Проверка, что маршрутизация между VLAN работает

 # Выполнение

   * Базовые настройки коммутаторов и маршрутизатора
      * Коммутаторы S1/S2:

```
enable

clock set 16:30:00 23 oct 2025

configure

no ip domain-lookup

hostname S1(S2)

banner motd #!!!!STOP!!!!!#

int range fa0/1, fa0/5, fa0/6 (int range fa0/1, fa0/18)

no shutdown

exit

service password-encryption

enable secret class

interface vlan 1

ip address 192.168.10.11 255.255.255.0 (ip address 192.168.10.12 255.255.255.0)

no shutdown

exit

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

end

write
```

***Настройка маршрутизатора аналогична, указывать не стал***

  * Настройка PC 1/2 
![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab6.2.PNG)

  * Создание сетей VLAN и назначение портов коммутатора

    S1

```
vlan 10

name Management

exit

vlan 20

name Salet

exit

vlan 30

name Operations

exit

vlan 999

name Parking_Lot

vlan 1000

name native

int range f0/2-4, f0/7-24, g0/1-2

switchport mode access

switchport acces vlan 999

shutdown

exit

int vlan 10

ip add 192.168.10.1 255.255.255.0

no shutdown

exit

int f0/6

switchport mode access

switchport acces vlan 20

end

write

```

По аналогиаи настраиваем S2 согласно таблицы Vlan 

  * Конфигурация магистрального канала стандарта 802.1Q между коммутаторами

На коммутаторах S1 и S2 настраиваем F0/1 как транк

```
int f0/1

switchport mode trunk

sw trunk native  vlan 1000

switchport trunk allowed vlan 10,20,30,1000

switchport nonegotiate 

no shutdown

end

write

```
Также настраиваем f0/5 на коммутаторе S1

На данный момент ping с PC-A до PC-B не проходит, так как они находятся в разных сетях, чтобы обеспечить связь междцу ними напм потребуется настроить маршрутизатор.

R1:

```
int g0/0/1.10

encapsulation  dot1q 10

ip add 192.168.10.1 255.255.255.0


exit

int g0/0/1.20

encapsulation  dot1q 20


ip add 192.168.20.1 255.255.255.0


exit

int g0/0/1.30

encapsulation  dot1q 30

ip add 192.168.30.1 255.255.255.0


exit

int g0/0/1.1000

encapsulation  dot1q 1000


exit

int g0/0/1

encapsulation  dot1q 1000 native

no shutdown

end

wr

```

Виртуальные интерфейсы создаются во включенном состоянии, поэтому проверяем работоспособность только физического интерфейса. Можно добавить коментарии к виртуальным интерфейсам командой description.

# Проверка

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab6.3.PNG)


    








