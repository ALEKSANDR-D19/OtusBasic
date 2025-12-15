# Лабораторная работа. Настройка и проверка расширенных списков контроля доступа

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab11-1.PNG)

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab11-2.PNG)

  # Задачи:
  * Создание сети и настройка основных параметров устройства
  * Настройка и проверка списков расширенного контроля доступа

# Выполнение:
  * Базовая настройка маршлутизаторов и коммутаторов

```
enable

clock set 17:30:00 15 dec 2025

configure

no ip domain-lookup

hostname S1(S2)

banner motd #!!!!STOP!!!!!#

int range fa0/1, fa0/5, fa0/6

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

Настройки маршрутизаторов аналогичны




  * Настройка сетей VLAN на коммутаторах

S1

```
vlan 20

name Management

vlan 30

name Operations

vlan 40

name Sales

vlan 999

name Parking_Lot

vlan 1000

name Native

int range f0/2-4, f0/7-24, g0/1-2

switchport mode access

switchport acces vlan 999

shutdown

exit

int vlan 20

ip add 10.20.0.2 255.255.255.0

no shutdown

exit

ip default-gateway 10.20.0.1

int f0/6

switchport mode access

switchport acces vlan 30

int fa0/1

switchport mode trunk

switchport trunk native vlan 1000

switchport trunk allowed vlan 20, 30,40,1000

int fa0/5

switchport mode trunk

switchport trunk native vlan 1000

switchport  trunk allowed vlan 20,30,40,1000

end

write

```

S2

```

vlan 20

name Management

vlan 30

name Operations

vlan 40

name Sales

vlan 999

name Parking_Lot

vlan 1000

name Native

int range f0/2-4,f0/6-17,f0/19-24,g0/1-2

switchport mode access

switchport acces vlan 999

shutdown

exit

int vlan 20

no shutdown

exit

ip default-gateway 10.20.0.1

int f0/18

switchport mode access

switchport acces vlan 40

int fa0/1

switchport mode trunk

switchport trunk native vlan 1000

switchport trunk allowed vlan 20,30,40,1000

int fa0/5

switchport mode trunk

switchport trunk native vlan 1000

switchport trunk allowed vlan 20,30,40,1000

end

write

```


`show vlan brief`

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab11-3.PNG)

`show interfaces trunk`

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab11-4.PNG)


  * Настройка маршрутизации

R1

```
int g0/0/1.20

encapsulation  dot1q 20

ip add 10.20.0.1 255.255.255.0

descr Management Vlan


int g0/0/1.30

encapsulation  dot1q 30

ip add 10.30.0.1 255.255.255.0

descr Operations Vlan

int g0/0/1.40

encapsulation  dot1q 40

ip add 10.40.0.1 255.255.255.0

descr Sales Vlan

int g0/0/1.1000

encapsulation  dot1q 1000

descr Native Vlan

int loopback 1

ip add 172.16.1.1 255.255.255.0

end

wr
```

`show ip interface brief `

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab11-5.PNG)

R2

```

int g0/0/1

ip add 10.20.0.4 255.255.255.0

exit

ip route 0.0.0.0 0.0.0.0 10.20.0.1

end

wr

```

  * Настройка удаленного доступа

```
ip domain-name ccna-lab.com

crypto key generate rsa

1024

ip ssh version 2

username SSHadmin  secret $cisco123!

line vty 0 4

login local

transport input ssh

end

write
```

  * Включение защищенных веб-служб с проверкой подлинности на R1

Команды `ip http secure-server` и `ip http authentication local` не поддерживаются в CPT

  * Настроим PC-A и PC-B согласно таблици адресации

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab11-6.PNG)

  * Тестирование

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab11-7.PNG)


![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab11-8.PNG)


![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab11-9.PNG)


Протокол HTTPS мы проверить не сможем так как мы его не настроили из-за ограничений CPT

  *  Настройка и проверка списков контроля доступа (ACL)

R1

```
access-list 100 deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq 22

access-list 100 deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq 80

access-list 100 deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq 443

access-list 100 deny tcp 10.40.0.0 0.0.0.255 10.30.0.0 0.0.0.255 eq 80

access-list 100 deny tcp 10.40.0.0 0.0.0.255 10.30.0.0 0.0.0.255 eq 443

access-list 100 deny tcp 10.40.0.0 0.0.0.255 10.40.0.0 0.0.0.255 eq 80

access-list 100 deny tcp 10.40.0.0 0.0.0.255 10.40.0.0 0.0.0.255 eq 443

access-list 100 deny icmp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 echo

access-list 100 deny icmp 10.40.0.0 0.0.0.255 10.30.0.0 0.0.0.255 echo

access-list 100 permit ip any any

int g0/0/1.40

ip access-group 100 in

exit

access-list 101 deny icmp 10.30.0.0 0.0.0.255 10.40.0.0 0.0.0.255 echo

access-list 101 permit ip any any

int g0/0/1.30

ip access-group 101 in

end 

wr
```


  * Тестирование

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab11-10.PNG)

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab11-11.PNG)

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab11-12.PNG)







   
   
    


