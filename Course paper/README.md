# Создание сети интернет-провайдера в Cisco Packet Tracer

# Введение

В рамках курсовой работы разработана и описана модель сети городского интернет-провайдера, реализованная в среде Cisco Packet Tracer (CPT). Модель демонстрирует базовые принципы построения провайдерской инфраструктуры: сегментация абонентов и служб по VLAN, управление доступом при помощи ACL, защита отказоустойчивости на канальном уровне (STP) и на уровне шлюза по умолчанию (HSRP), выдача адресов по DHCP, синхронизация времени по NTP, удаленное администрирование по SSH и организация выхода в интернет через NAT.


# Требования к работе:

  * сеть городского интернет-провайдера (модель в CPT);
  * абонентам предоставляется доступ в интернет с получением IP по DHCP
  * обязательные технологии: VLAN, ACL, STP, SSH, DHCP, NTP, NAT
  * эмуляция выхода в интернет внутри CPT
  * наличие резервных линий связи для отказоустойчивости
  * два администратора с разными правами: admin (ограниченный), superadmin (полный)
  * ограничение количества MAC-адресов на access-портах

# Задачи работы:

  * Разработать топологию сети
  * Создать VLAN для абонентов и управления
  * Настроить STP и резервные связи между коммутаторами доступа и распределения
  * Настроить DHCP-сервер
  * Настроить NTP
  * Настроить NAT
  * Реализовать ACL
  * Настроить SSH-доступ и создать два администратора (superadmin - полный доступ, admin - ограниченный)
  * Включить Port-Security на access-портах
  * Провести тестирование и зафиксировать результаты

# Используемое оборудование в CPT

|Устройство|Модель|Роль в проекте|
|-------|------|------|
|R-EDGE|Маршрутизатор Cisco 2911|Граничный маршрутизатор провайдерва|
|R-ISP|Маршрутизатор Cisco 2911|Эмуляция провайдера|
|DSW1|Коммутатор L3 Cisco 3560 |Распределение|
|DSW2|Коммутатор L3 Cisco 3560 |Распределение|
|ASW1|Коммутатор L2 Cisco 2960 |Доступ|
|ASW2|Коммутатор L2 Cisco 2960 |Доступ|
|Server-SERV|PT Server |DHCP, NTP|
|Internet Server|PT Server |HTTP-сервер|
|PC-ADMIN/PC-SUPERADMIN|PC|Рабочие станции администраторов|
|Клиенты|PC|Абоненты|

# План VLAN и IP-адресация

|VLAN|ИМЯ|Назначение|Подсеть|Шлюз по умолчанию|
|------|------|------|------|------|
|10|RESIDENTIAL|Абоненты домашний сектор|192.168.10.0/24|192.168.10.1 |
|20|BUSINESS|Абоненты бизнес|192.168.20.0/24|192.168.20.1 |
|30|SERVICES|DHCP/NTP|192.168.30.0/24|192.168.30.1 |
|99|MGMT|Управление|192.168.99.0/24|192.168.99.1 |
|999|NATIVE-BLACKHOLE|Native VLAN |-|- |

# IP-адреса соединений:

|Связь|Подсеть|Адреса|
|------|------|------|
|R-EDGE G0/0 - R-ISP G0/0 |203.0.113.0/30|R-ISP: 203.0.113.1, R-EDGE: 203.0.113.2|
|R-EDGE G0/1 - DSW1 Fa0/1 |10.0.0.0/30|R-EDGE: 10.0.0.1, DSW1: 10.0.0.2|
|R-EDGE G0/2 - DSW2 Fa0/1|10.0.0.4/30|R-EDGE: 10.0.0.5, DSW2: 10.0.0.6|
|R-ISP G0/1 - Internet Server|198.51.100.0/24|R-ISP: 198.51.100.1, Server: 198.51.100.10|


# Топология:
![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/kurs/Топология.PNG)

# Конфигурации устройств

  * R-ISP

```

hostname R-ISP
no ip domain-lookup

enable secret SuperEnable123

service password-encryption

ip domain-name isp.net
username superadmin privilege 15 secret SuperP@ss123
username admin privilege 1 secret AdminP@ss123

crypto key generate rsa 
2048
ip ssh version 2

interface GigabitEthernet0/0
 description WAN to R-EDGE
 ip address 203.0.113.1 255.255.255.252
 no shutdown
interface GigabitEthernet0/1
 description LAN to Internet Server
 ip address 198.51.100.1 255.255.255.0
 no shutdown

ip access-list standard MGMT-SSH
 permit 203.0.113.2

line vty 0 4
 transport input ssh
 login local
 exec-timeout 10 0
 access-class MGMT-SSH in

end
wr

```
включен SSH и локальные пользователи; доступ по VTY ограничен ACL MGMT-SSH (только из VLAN99);
настроены IP-адреса интерфейсов G0/0 и G0/1


  * R-EDGE

```

hostname R-EDGE
no ip domain-lookup

service password-encryption
enable secret SuperEnable123

ip domain-name isp.local
username superadmin privilege 15 secret SuperP@ss123
username admin privilege 1 secret AdminP@ss123

crypto key generate rsa
2048 
ip ssh version 2

interface GigabitEthernet0/0
 description WAN to R-ISP            
 ip address 203.0.113.2 255.255.255.252
 ip nat outside
 no shutdown

interface GigabitEthernet0/1
 description L3 to DSW1 
 ip address 10.0.0.1 255.255.255.252
 ip nat inside
 no shutdown

interface GigabitEthernet0/2
 description L3 to DSW2 
 ip address 10.0.0.5 255.255.255.252
 ip nat inside
 no shutdown

! Маршрут по умолчанию в интернет
ip route 0.0.0.0 0.0.0.0 203.0.113.1

! Маршруты во внутренние сети провайдера (основной и резервный)
ip route 192.168.0.0 255.255.0.0 10.0.0.2
ip route 192.168.0.0 255.255.0.0 10.0.0.6 10

! NAT/PAT (overload)
ip access-list standard NAT_INSIDE
 permit 192.168.0.0 0.0.255.255

ip nat inside source list NAT_INSIDE interface GigabitEthernet0/0 overload

ntp server 192.168.30.10
clock timezone MSK 3

ip access-list standard MGMT-SSH
 permit 192.168.99.0 0.0.0.255
!
line vty 0 4
 transport input ssh
 login local
 exec-timeout 10 0
 access-class MGMT-SSH in
!
end
wr

```

интерфейс G0/0 помечен как ip nat outside, внутренние G0/1 и G0/2 - ip nat inside;
NAT_INSIDE охватывает все внутренние VLAN 192.168.0.0/16;
настроены статические маршруты к внутренним сетям (основной через DSW1 и резервный через DSW2);
включен SSH и ограничение доступа по VTY только из VLAN99.


  * DSW1

```

hostname DSW1
no ip domain-lookup
service password-encryption

enable secret SuperEnable123

ip domain-name isp.local
username superadmin privilege 15 secret SuperP@ss123
username admin privilege 1 secret AdminP@ss123
crypto key generate rsa modulus 1024
ip ssh version 2

spanning-tree mode rapid-pvst
spanning-tree vlan 10,30,99 root primary
spanning-tree vlan 20 root secondary

vlan 10
 name RESIDENTIAL
vlan 20
 name BUSINESS
vlan 30
 name SERVICES
vlan 99
 name MGMT
vlan 999
 name NATIVE-BLACKHOLE

ip routing

! L3 линк к R-EDGE
interface FastEthernet0/1
 description L3 to R-EDGE G0/1
 no switchport
 ip address 10.0.0.2 255.255.255.252
 no shutdown

! EtherChannel  к DSW2
interface range GigabitEthernet0/1-2
 description Po1 to DSW2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,30,99,999
 channel-group 1 mode active

interface Port-channel1
 description LACP Po1 to DSW2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,30,99,999

! Trunk к ASW1
interface FastEthernet0/2
 description Trunk to ASW1 Gi0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,30,99,999

! Trunk к ASW2
interface FastEthernet0/3
 description Trunk to ASW2 Gi0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,30,99,999

interface Vlan10
 description RESIDENTIAL
 ip address 192.168.10.2 255.255.255.0
 standby 10 ip 192.168.10.1
 standby 10 priority 110
 standby 10 preempt
 ip helper-address 192.168.30.10
 ip access-group VLAN10_IN in
 no shutdown

interface Vlan20
 description BUSINESS
 ip address 192.168.20.2 255.255.255.0
 standby 20 ip 192.168.20.1
 standby 20 priority 100
 standby 20 preempt
 ip helper-address 192.168.30.10
 ip access-group VLAN20_IN in
 no shutdown

interface Vlan30
 description SERVICES
 ip address 192.168.30.2 255.255.255.0
 standby 30 ip 192.168.30.1
 standby 30 priority 110
 standby 30 preempt
 no shutdown

interface Vlan99
 description MGMT
 ip address 192.168.99.2 255.255.255.0
 standby 99 ip 192.168.99.1
 standby 99 priority 110
 standby 99 preempt
 no shutdown

! ACL: запрет доступа абонентов к VLAN управления и изоляция VLAN10/VLAN20
ip access-list extended VLAN10_IN
 remark Deny access to MGMT VLAN
 deny ip 192.168.10.0 0.0.0.255 192.168.99.0 0.0.0.255
 remark Deny access to BUSINESS VLAN
 deny ip 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255
 permit ip any any

ip access-list extended VLAN20_IN
 remark Deny access to MGMT VLAN
 deny ip 192.168.20.0 0.0.0.255 192.168.99.0 0.0.0.255
 remark Deny access to RESIDENTIAL VLAN
 deny ip 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255
 permit ip any any
!
! Маршрут по умолчанию к R-EDGE
ip route 0.0.0.0 0.0.0.0 10.0.0.1
!
! NTP клиент
ntp server 192.168.30.10
clock timezone MSK 3
!
! Ограничение SSH-доступа только из MGMT VLAN
ip access-list standard MGMT-SSH
 permit 192.168.99.0 0.0.0.255


line vty 0 4
 transport input ssh
 login local
 exec-timeout 10 0
 access-class MGMT-SSH in

end

wr
```
ip routing включает маршрутизацию на L3-коммутаторе
настроены VLAN и trunk 
Port-channel1 объединяет Gi0/1-2 в EtherChannel;
HSRP обеспечивает виртуальные шлюзы .1 в каждом VLAN;
ACL VLAN10_IN/VLAN20_IN блокирует доступ к VLAN99 и изолирует VLAN10 и VLAN20;
DHCP relay (ip helper-address) перенаправляет DHCP-запросы на Server-SERV (192.168.30.10).

  * DSW2

```

hostname DSW2
no ip domain-lookup
service password-encryption

enable secret SuperEnable123

ip domain-name isp.local
username superadmin privilege 15 secret SuperP@ss123
username admin privilege 1 secret AdminP@ss123
crypto key generate rsa 
2048
ip ssh version 2

spanning-tree mode rapid-pvst
spanning-tree vlan 20 root primary
spanning-tree vlan 10,30,99 root secondary

vlan 10
 name RESIDENTIAL
vlan 20
 name BUSINESS
vlan 30
 name SERVICES
vlan 99
 name MGMT
vlan 999
 name NATIVE-BLACKHOLE

ip routing

interface FastEthernet0/1
 description L3 to R-EDGE G0/2
 no switchport
 ip address 10.0.0.6 255.255.255.252
 no shutdown

! EtherChannel (LACP) к DSW1
interface range GigabitEthernet0/1-2
 description Po1 to DSW1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,30,99,999
 channel-group 1 mode active

interface Port-channel1
 description LACP Po1 to DSW1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,30,99,999

! Trunk к ASW1
interface FastEthernet0/2
 description Trunk to ASW1 Gi0/2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,30,99,999

! Trunk к ASW2
interface FastEthernet0/3
 description Trunk to ASW2 Gi0/2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,30,99,999

interface Vlan10
 description RESIDENTIAL
 ip address 192.168.10.3 255.255.255.0
 standby 10 ip 192.168.10.1
 standby 10 priority 100
 standby 10 preempt
 ip helper-address 192.168.30.10
 ip access-group VLAN10_IN in
 no shutdown

interface Vlan20
 description BUSINESS
 ip address 192.168.20.3 255.255.255.0
 standby 20 ip 192.168.20.1
 standby 20 priority 110
 standby 20 preempt
 ip helper-address 192.168.30.10
 ip access-group VLAN20_IN in
 no shutdown

interface Vlan30
 description SERVICES
 ip address 192.168.30.3 255.255.255.0
 standby 30 ip 192.168.30.1
 standby 30 priority 100
 standby 30 preempt
 no shutdown

interface Vlan99
 description MGMT
 ip address 192.168.99.3 255.255.255.0
 standby 99 ip 192.168.99.1
 standby 99 priority 100
 standby 99 preempt
 no shutdown

! ACL (аналогично DSW1)
ip access-list extended VLAN10_IN
 remark Deny access to MGMT VLAN
 deny ip 192.168.10.0 0.0.0.255 192.168.99.0 0.0.0.255
 remark Deny access to BUSINESS VLAN
 deny ip 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255
 permit ip any any

ip access-list extended VLAN20_IN
 remark Deny access to MGMT VLAN
 deny ip 192.168.20.0 0.0.0.255 192.168.99.0 0.0.0.255
 remark Deny access to RESIDENTIAL VLAN
 deny ip 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255
 permit ip any any

! Маршрут по умолчанию к R-EDGE (через резервный линк)
ip route 0.0.0.0 0.0.0.0 10.0.0.5

! NTP клиент
ntp server 192.168.30.10
clock timezone MSK 3

ip access-list standard MGMT-SSH
 permit 192.168.99.0 0.0.0.255

line vty 0 4
 transport input ssh
 login local
 exec-timeout 10 0
 access-class MGMT-SSH in

end

wr

```

DSW2 является корневым мостом STP для VLAN20 и активным HSRP для VLAN20;
настроен резервный линк к R-EDGE

  * ASW2

  ```

hostname ASW1
no ip domain-lookup
service password-encryption

enable secret SuperEnable123

ip domain-name isp.local
username superadmin privilege 15 secret SuperP@ss123
username admin privilege 1 secret AdminP@ss123
crypto key generate rsa modulus 1024
ip ssh version 2

spanning-tree mode rapid-pvst

vlan 10
 name RESIDENTIAL
vlan 20
 name BUSINESS
vlan 30
 name SERVICES
vlan 99
 name MGMT
vlan 999
 name NATIVE-BLACKHOLE

! Trunk uplinks
interface GigabitEthernet0/1
 description Trunk to DSW1 Fa0/2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,30,99,999

interface GigabitEthernet0/2
 description Trunk to DSW2 Fa0/2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,30,99,999

! Access ports - VLAN10 (клиенты)
interface range FastEthernet0/1-2
 description CLIENTS VLAN10
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 spanning-tree bpduguard enable
 switchport port-security
 switchport port-security maximum 3
 switchport port-security mac-address sticky
 switchport port-security violation restrict

! Access port - VLAN20 (клиенты)
interface FastEthernet0/11
 description CLIENT VLAN20
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 spanning-tree bpduguard enable
 switchport port-security
 switchport port-security maximum 3
 switchport port-security mac-address sticky
 switchport port-security violation restrict

! Access port - VLAN30 (Server-SERV)
interface FastEthernet0/21
 description Server-SERV VLAN30
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
 spanning-tree bpduguard enable
 switchport port-security
 switchport port-security maximum 3
 switchport port-security mac-address sticky
 switchport port-security violation restrict

! Access ports - VLAN99 (администраторы)
interface range FastEthernet0/22-23
 description ADMIN VLAN99
 switchport mode access
 switchport access vlan 99
 spanning-tree portfast
 spanning-tree bpduguard enable
 switchport port-security
 switchport port-security maximum 3
 switchport port-security mac-address sticky
 switchport port-security violation restrict

interface Vlan99
 ip address 192.168.99.11 255.255.255.0
 no shutdown

ip default-gateway 192.168.99.1

! NTP
ntp server 192.168.30.10
clock timezone MSK 3

ip access-list standard MGMT-SSH
 permit 192.168.99.0 0.0.0.255

line vty 0 4
 transport input ssh
 login local
 exec-timeout 10 0
 access-class MGMT-SSH in

end

wr
```
Uplink-порты Gi0/1 и Gi0/2 работают в trunk и несут VLAN 10/20/30/99;
на access-портах включен Port-Security с maximum 3 и sticky;
на портах включены portfast и bpduguard;
управление коммутатором выполняется через  VLAN99 

  * ASW2

  ```
hostname ASW2
no ip domain-lookup
service password-encryption

enable secret SuperEnable123

ip domain-name isp.local
username superadmin privilege 15 secret SuperP@ss123
username admin privilege 1 secret AdminP@ss123
crypto key generate rsa 
2048	
ip ssh version 2

spanning-tree mode rapid-pvst

vlan 10
 name RESIDENTIAL
vlan 20
 name BUSINESS
vlan 30
 name SERVICES
vlan 99
 name MGMT
vlan 999
 name NATIVE-BLACKHOLE

! Trunk uplinks
interface GigabitEthernet0/1
 description Trunk to DSW1 Fa0/3
 switchport mode trunk
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,30,99,999

interface GigabitEthernet0/2
 description Trunk to DSW2 Fa0/3
 switchport mode trunk
 switchport trunk native vlan 999
 switchport trunk allowed vlan 10,20,30,99,999

! Access ports - VLAN10
interface FastEthernet0/1
 description CLIENT VLAN10
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 spanning-tree bpduguard enable
 switchport port-security
 switchport port-security maximum 3
 switchport port-security mac-address sticky
 switchport port-security violation restrict

! Access ports - VLAN20
interface range FastEthernet0/11-12
 description CLIENTS VLAN20
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 spanning-tree bpduguard enable
 switchport port-security
 switchport port-security maximum 3
 switchport port-security mac-address sticky
 switchport port-security violation restrict

interface Vlan99
 ip address 192.168.99.12 255.255.255.0
 no shutdown

ip default-gateway 192.168.99.1

ntp server 192.168.30.10
clock timezone MSK 3

ip access-list standard MGMT-SSH
 permit 192.168.99.0 0.0.0.255

line vty 0 4
 transport input ssh
 login local
 exec-timeout 10 0
 access-class MGMT-SSH in

end

wr
```
Аналогично ASW1: trunk uplinks, Port-Security на access-портах, управление по SSH из VLAN99

  * Настройка Server-SERV

IP: 192.168.30.10
Mask: 255.255.255.0
Default Gateway: 192.168.30.1

DHCP on

POOL_VLAN10: Network 192.168.10.0/24, Default Gateway 192.168.10.1, DNS 192.168.30.10, Start IP 192.168.10.100, Max Users 100
POOL_VLAN20: Network 192.168.20.0/24, Default Gateway 192.168.20.1, DNS 192.168.30.10, Start IP 192.168.20.100, Max Users 100

DNS on

Запись: www.internet.test -> 198.51.100.10

NTP on


  * Настройка Internet Server
IP: 198.51.100.10/24
Gateway: 198.51.100.1

HTTP on

# Тестирование и проверка работоспособности:

  * Проверка VLAN и trunk

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/kurs/testvlantrunk.PNG)

  * Проверка STP и отказоустойчивости L2

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/kurs/Тест%20STP1.PNG)

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/kurs/Тест%20STP2.PNG)

  * Проверка DHCP

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/kurs/Тест%20DHCP1.PNG)

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/kurs/Тест%20DHCP2.PNG)

  * Проверка NAT

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/kurs/Тест%20NAT.PNG)

  * Проверка NTP

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/kurs/Тест%20NTP.PNG)

# Заключение

В ходе выполнения курсовой работы разработана модель сети интернет-провайдера в CPT, включающая сегментацию по VLAN, отказоустойчивые соединения и шлюз по умолчанию, централизованную выдачу IP по DHCP, синхронизацию времени по NTP, защиту доступа ACL, удаленное администрирование по SSH и выход в интернет через NAT.

Реализована базовая безопасность: ограничение управления только из VLAN99, два администратора с разными правами, а также Port-Security на access-портах (максимум 3 MAC-адреса). Проведенные тесты подтверждают работоспособность сети и устойчивость к отказу отдельных линий связи.

















   

