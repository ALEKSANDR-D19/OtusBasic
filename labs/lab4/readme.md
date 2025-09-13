# Настройка IPv6-адресов на сетевых устройствах


![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/labs/lab4/jpeg/1.PNG)

# Задачи
  * Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора
  * Часть 2. Ручная настройка IPv6-адресов
  * Часть 3. Проверка сквозного соединения

# Выполнение:

  * Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора

    * Шаг 1. Базовые настройки маршрутизатора


    ![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/labs/lab4/jpeg/2.PNG)


      * Шаг 2. Базовае настройки коммутатора
   
![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/labs/lab4/jpeg/3.PNG)

  * Часть 2. Ручная настройка IPv6-адресов

    * Шаг 1.  Назначение IPv6-адреса интерфейсам Ethernet на R1.
    
    
   

    ![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/labs/lab4/jpeg/4.PNG)


Какие группы многоадресной рассылки назначены интерфейсу G0/0?

show ipv6 interface g 0/0/0

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/labs/lab4/jpeg/5.PNG)
 
  *  Шаг 2.  Активировать IPv6-маршрутизацию на R1.
    
    До выполнения команды ipv6 unicast-routing на интерфейсе 0/0/0 R1  PC-B не получал ipv6

    После выполнения команды PC-B получил ipv6 2001:DB8:ACAD:A:201:C7FF:FE99:DD0C

    Почему PC-B получил глобальный префикс маршрутизации и идентификатор подсети, которые вы настроили на R1?

    Потому что R1 выдаёт префикс сети, PC-B при помощи технологии SLACC генирирует оставшиеся 64 бита.

  
  * Шаг 3. Назначить IPv6-адреса интерфейсу управления (SVI) на S1

configure terminal

sdm prefer dual-ipv4-and-ipv6 default

end

wr mem

reload

show ipv6 interface vlan1


![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/labs/lab4/jpeg/6.PNG)


  * Шаг 4. Назначить компьютерам статические IPv6-адреса.

PC-A:

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/labs/lab4/jpeg/100.PNG)

PC-B:

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/labs/lab4/jpeg/101.PNG)


# Часть 3. Проверка сквозного подключения

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/labs/lab4/jpeg/9.PNG)

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/labs/lab4/jpeg/10.PNG)


# Вопросы для повторения
1.	Почему обоим интерфейсам Ethernet на R1 можно назначить один и тот же локальный адрес канала — FE80::1?
2.	Какой идентификатор подсети в индивидуальном IPv6-адресе 2001:db8:acad::aaaa:1234/64?


# Ответы:

1. Потому что каждый интерфейс R1 это отдельная сеть.

2. 2001:db8:acad::aaaa:1234 = 2001:db8:acad:0000:0000:0000:aaaa:1234, индетефикатор подсети 2001:db8:acad:0000
   










      

    
        
    
   








