# Лабораторная работа. Настройка протокола OSPFv2 для одной области

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab10-1.PNG)

# Цели:
 
  * Создание сети и настройка основных параметров устройства
  
  * Настройка и проверка базовой работы протокола  OSPFv2 для одной области
  
  * Оптимизация и проверка конфигурации OSPFv2 для одной области

# Выполнение

  *  Настройка основных параметров   коммутаторов и маршрутизаторов

S1/S2

```
enable

clock set 18:30:00 03 dec 2025

configure

no ip domain-lookup

hostname S1(S2)

banner motd #!!!!STOP!!!!!#

int range fa0/1, fa0/5

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

Настройка маршрутизаторов аналогична

  * Настройка и проверка базовой работы протокола OSPFv2 для одной области

R1

```
int g0/0/1

ip add 10.53.0.1 255.255.255.0

no shut

int loopback 1

ip add 172.16.1.1 255.255.255.0

no shut

exit


router ospf 56

router-id 1.1.1.1

int g0/0/1

ip ospf 56 area 0

end

wr

```

R2

```

int g0/0/1

ip add 10.53.0.2 255.255.255.0

no shut

int loopback 1

ip add 192.168.1.1 255.255.255.0

no shut

exit


router ospf 56

router-id 1.1.1.1

int g0/0/1

ip ospf 56 area 0

int loopback 1

ip ospf 56 area 0

end

wr

```

R1/R2

```
show ip ospf neighbor
```


![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab10-2.PNG)


Какой маршрутизатор является DR? Какой маршрутизатор является BDR? Каковы критерии отбора?

R2 является BDR, R1 - DR, так произошло потому что R1 был настроен первым

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab10-3.PNG)

Выполним команду `clear ip ospf process` на обоих коммутаторах и повторим команду `show ip ospf int g0/0/1` на R2

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab10-4.PNG)

Мы видим что R1 стал BDR, R2-D, так как у R1 индитефикатор ниже.

Вывод команды `show ip route ospf` на R1

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab10-5.PNG)

Cеть R2 Loopback1 присутствует в таблице маршрутизации


![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab10-6.PNG)


# Оптимизация и проверка конфигурации OSPFv2 для одной области

R1

```
int g0/0/1

ip ospf priority 50

ip ospf hello-interval 30

exit

ip route 0.0.0.0 0.0.0.0 loopback 1

router ospf 56

default-information originate

auto-cost reference-bandwidth 1000

end

wr

clear ip ospf process

```
R2

```
int g0/0/1

ip ospf hello-interval 30

int loopback 1

ip ospf network point-to-point

exit

router ospf 56

passive-interface loopback 1

auto-cost reference-bandwidth 1000

end

wr

clear ip ospf process

```


 # Проверка 

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab10-7.PNG)

Приоритет интерфейса g0/0/1 на R1  установлен равным 50, временные интервалы — Hello 30, Dead 40, а тип сети по умолчанию — Broadcast.
Временной интервал dead можно поменять при помощи команды `ip ospf dead-interval 120`

Сеть R2 Loopback1 присутствует в таблице маршрутизации R1

Ping до адреса интерфейса R1 Loopback 1 из R2 успешен

Почему стоимость OSPF для маршрута по умолчанию отличается от стоимости OSPF в R1 для сети 192.168.1.0/24?

Потому что маршруты по умолчанию не меняют метрику.






