# Лабораторная работа - Конфигурация безопасности коммутатора 

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab9-1.PNG)

# Цели:
  * Настройка основного сетевого устройстваэ
  * Настройка сетей VLAN
  * Настройки безопасности коммутатора

# Выполнение

  * Настройка маршрутизатора R1

```
enable
configure terminal
hostname R1
no ip domain lookup
ip dhcp excluded-address 192.168.10.1 192.168.10.9
ip dhcp excluded-address 192.168.10.201 192.168.10.202
!
ip dhcp pool Students
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 domain-name CCNA2.Lab-11.6.1
!
interface Loopback0
 ip address 10.10.1.1 255.255.255.0
!
interface GigabitEthernet0/0/1
 description Link to S1
 ip dhcp relay information trusted
 ip address 192.168.10.1 255.255.255.0
 no shutdown
!
line con 0
 logging synchronous
 exec-timeout 0 0
```
СPT не поддерживает команду ip dhcp relay information trusted, поэтому меняем её на команду ip dhcp relay information trust-all в режиме глобальной конфигурации.

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab9-2.PNG)

```
show ip interface brief
```

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab9-3.PNG)

  * Настройка и проверка основных параметров коммутатора

S1

```
enable

conf

hostname S1

no ip domain lookup

int fa0/1

description link to S2

int fa0/5

description link to R1

int fa0/6

description link to PC-A

exit

ip default-gateway 192.168.10.1

end

wr

```

По аналогии настраиваем S2

  * Настройка сетей VLAN на коммутаторах

S1

```
vlan 10

name Management

exit

int vlan 10

ip add 192.168.10.201 255.255.255.0

description S1 Management

exit

vlan 333

name Native

exit

vlan 999

name ParkingLot

end

wr
```

По аналогии настраиваем S2

  * Настройки безопасности коммутатора.

```

int fa0/1

swi mode tru 

swi tru native vlan 333

end

show interface trunk

```

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab9-4.PNG)

```

int fa0/1

switchport nonegotiate

end

show interfaces f0/1 switchport | include Negotiation

```

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab9-5.PNG)

```

int ra fa0/5-6

swi mode access

swi  mode access vlan 10


int ra fa0/2-4, fa0/7-24, g0/1-2

swi  mode access

swi   access vlan 999

shutdown

end

wr

show interfaces status

```

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab9-6.PNG) 


  * Документирование и реализация функций безопасности порта
```
show port-security interface f0/6  
```

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab9-7.PNG)

 * Включим защиту порта на fa0/6 S1

```
int fa0/6

swi  port-security

swi  port-security maximum 3

swi  port-security violation restrict

swi  port-security aging time 60

end

show port-security interface f0/6

```

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab9-8.PNG)

```
show port-security address
```

В выводе команды show port-security address нет MAC адреса, сделаем пинг с PC-A на PC-B и попробуем ещё раз.

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab9-9.PNG)

  * Включим безопасность порта для F0 / 18 на S2

```
int f0/18

swi  port-security

swi  port-security  mac-add sticky

swi  port-security maximum 2

swi  port-security violation protect

swi  port-security aging time 60

end

wr

show port-security interface f0/18


show port-security address

```

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab9-10.PNG)

  * Реализовать безопасность DHCP snooping.
S2
```

ip dhcp snooping

ip dhcp snooping vlan 10

int fa0/1

ip dhcp snooping trust 

int fa0/18

ip dhcp snooping limit rate 5

end

wr

show ip dhcp snooping

```


![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab9-11.PNG)


  * В командной строке на PC-B освободим, а затем обновим IP-адрес:

```

ipconfig /release

ipconfig /renew

```

Проверbv привязку отслеживания DHCP с помощью команды show ip dhcp snooping binding:

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab9-12.PNG)


  * Реализация PortFast и BPDU Guard

S1

```
int ra fa0/5-6

spanning-tree portfast

int fa0/6

spanning-tree bpduguard enable

end

wr

```

S2

```
int fa0/18

spanning-tree portfast

spanning-tree bpduguard enable


end

wr
```

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab9-13.PNG)

  *  Проверьте наличие сквозного ⁪подключения

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab9-14.PNG)















































