# Лабораторная работа - Настройка NAT для IPv4

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab12-1.PNG)

Цели:

  * Создание сети и настройка основных параметров устройства
  * Настройка и проверка NAT для IPv4
  * Настройка и проверка PAT для IPv4
  * Настройка и проверка статического NAT для IPv4

# Выполнение: 

  * Создание сети и настройка основных параметров устройства

S1/S2
```
enable

clock set 21:50:00 15 dec 2025

configure

no ip domain-lookup

hostname S1(S2)

banner motd #!!!!STOP!!!!!#

service password-encryption

enable secret class

line vty 0 4

password cisco

login

login local

int range f0/2-4,f0/7-24, g0/1-2

shut

int vlan 1

ip add 192.168.1.11 255.255.255.0

no shu  

end

write
```


R1

```
enable

clock set 21:50:00 15 dec 2025

configure

no ip domain-lookup

hostname R1

banner motd #!!!!STOP!!!!!#

service password-encryption

enable secret class

line vty 0 4

password cisco

login

login local

int g0/0/0

ip add 209.165.200.230 255.255.255.248

no shut

int g0/0/1

ip add 192.168.1.1 255.255.255.0 

no shut

exit 

ip route 0.0.0.0 0.0.0.0 209.165.200.225

end

wr

```


R2

``` 
enable

clock set 21:50:00 15 dec 2025

configure

no ip domain-lookup

hostname R2

banner motd #!!!!STOP!!!!!#

service password-encryption

enable secret class

line vty 0 4

password cisco

login

login local

int g0/0/0

ip add 209.165.200.225 255.255.255.248

no shut

int loopback 1

ip add 209.165.200.1 255.255.255.224  

end 

wr
```

  * Настройка и проверка NAT для IPv4

R1 

```
access-list 1 permit 192.168.1.0 0.0.0.255

ip nat pool PUBLIC_ACCESS 209.165.200.226 209.165.200.228 netmask 255.255.255.248

ip nat inside source list 1 pool PUBLIC_ACCESS

int g0/0/1

ip nat inside

int g0/0/0

ip nat outside

end

wr

```
Ping с PC-B до 209.165.200.1 не проходит. Укажем шлюз по умолчанию  192.168.1.1 и проверим ещё раз. 

`show ip nat translations`

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab12-3.PNG)

Во что был транслирован внутренний локальный адрес PC-B?

209.165.200.226


Какой тип адреса NAT является переведенным адресом?

Внутренный глобальный адрес 


![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab12-4.PNG)

Ping с S1 до 209.165.200.1 не проходит. Укажем шлюз по умолчанию  192.168.1.1 и проверим ещё раз. 

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab12-5.PNG)

Команды `show ip nat translations verbose ` нет в CPT

R1

```
clear ip nat translations * 
```

  * Настройка и проверка PAT для IPv4

R1

```
no ip nat inside source list 1 pool PUBLIC_ACCESS

ip nat inside source list 1 pool PUBLIC_ACCESS overload

```

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab12-6.PNG)

Во что был транслирован внутренний локальный адрес PC-B?

209.165.200.226

Какой тип адреса NAT является переведенным адресом?

Внутренний глобальный

Чем отличаются выходные данные команды show ip nat translations из упражнения NAT?

Ничем


С PC-A пинг до 209.165.200.1 проходит

`ping -t 209.165.200.1`


![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab12-7.PNG)

Как маршрутизатор отслеживает, куда идут ответы? 

Благодаря уникальным номерам портов для каждого устройства

R1

```

no ip nat inside source list 1 pool PUBLIC_ACCESS overload

no ip nat pool PUBLIC_ACCESS

ip nat inside source list 1 interface g0/0/0 overload

```

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab12-8.PNG)

  * Настройка и проверка статического NAT для IPv4

R1

```
ip nat inside source static 192.168.1.2 209.165.200.229

```

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab12-9.PNG)







