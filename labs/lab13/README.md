# Лабораторная работа - Настройка протоколов CDP, LLDP и NTP

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab13-1.PNG)

# Задачи:

  * Создание сети и настройка основных параметров устройства
  * Обнаружение сетевых ресурсов с помощью протокола CDP
  * Обнаружение сетевых ресурсов с помощью протокола LLDP
  * Настройка и проверка NTP

# Выполнение:

  * Создание сети и настройка основных параметров устройства


S1/2

```
enable

clock set 17:30:00 27 dec 2025

configure

no ip domain-lookup

hostname S1

banner motd #!!!!STOP!!!!!#

service password-encryption

enable secret class

int ra fa0/2-4, fa0/6-24, g0/1-2

shut

exit

line vty 0 4

password cisco

login

login local

transport input ssh

end

write

```

R1

```
enable

clock set 17:30:00 27 dec 2025

conf

no ip domain-lookup

hostname R1

banner motd #!!!!STOP!!!!!#


service password-encryption

enable secret class

interface g0/0/1

ip address 10.22.0.1 255.255.255.0 

no shutdown

int loopback1

ip address 172.16.1.1 255.255.255.0 

exit

line vty 0 4

password cisco

login

login local

transport input ssh

end

write

```

  * Обнаружение сетевых ресурсов с помощью протокола CDP

R1 `show cdp int`

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab13-2.PNG)

Сколько интерфейсов участвует в объявлениях CDP? - 2

Какие из них активны? - int g0/0/1

R1 `show cdp entry  S1`

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab13-3.PNG)

Какая версия IOS используется на  S1? - Version 15.0

Команда `show cdp traffic` не поддерживается CPT

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab13-4.PNG)

Сколько пакетов имеет выход CDP с момента последнего сброса счетчика? - Согласно выводу выше - 179

  * Настройка SVI для VLAN 1 на S1 и S2

S1/2

```
int vlan 1

ip add 10.22.0.2 255.255.255.0 (10.22.0.3 255.255.255.0)

no shut

exit

ip def  10.22.0.1

end

wr

```

R1 `show cdp entry S1 `

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab13-5.PNG)

Какие дополнительные сведения доступны теперь? - ip адрес

  * Отключение CDP

R1/S1/S2 `no cdp run`

  * Обнаружение сетевых ресурсов с помощью протокола LLDP

R1/S1/S2 `lldp run`

Команда `show lldp entry ` не поддерживается CPT, заменим её на `show lldp neighbors detail`
 
![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab13-6.PNG)

Что такое chassis ID  для коммутатора S2? - Индетификатор коммутатора

  * Настройка NTP
R1 `ntp master 4`
S1/2 `ntp server 10.22.0.1`

Команда  `ntp update-calendar` не поддерживается Cpt

S1/2 `show ntp status`

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab13-7.PNG)

Для каких интерфейсов в пределах сети не следует использовать протоколы обнаружения сетевых ресурсов? Поясните ответ. 

На интерфейсах которые подключены к внешним сетям. Злоумышленники могут получить информацию об устройствах.















