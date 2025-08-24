# Просмотр таблицы MAC-адресов коммутатора

![alt text](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab2-1.PNG)

# Цели
   * Часть 1. Создание и настройка сети
   * Часть 2. Изучение таблицы МАС-адресов коммутатора

# Выполнение
   * # Часть 1. Создание и настройка сети

<img width="1357" height="573" alt="image" src="https://github.com/user-attachments/assets/5ed74e0c-0f20-4a8a-b70b-80c569e96756" />

enable

configure

no ip domain-lookup

hostname S1

service password-encryption

enable secret class


interface vlan 1

ip address 192.168.1.11 255.255.255.0

no shutdown

exit

line vty 0 4

password cisco

login

end

copy running-config startup-config 

По аналогии настраиваем второй коммутатор

![alt text](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab2S1.PNG) ![alt text](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab2S2.PNG)

Настраиваем пк и подключаем кабели как указано в топологии

![alt](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab2PC-A.PNG)

![alt](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lb2PC-B.PNG)

![alt](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab2-3.PNG)


   * # Часть 2. Часть 2. Изучение таблицы МАС-адресов коммутатора
   

![alt text](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab2-4.PNG)

![alt text](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab2-5.PNG)

Назовите физические адреса адаптера Ethernet.

MAC-адрес компьютера PC-A:0010.1174.D970

MAC-адрес компьютера PC-B: 000A.F357.7758

![alt text](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab2-8.PNG)

![alt text](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab2-9.PNG)

MAC-адрес коммутатора S1: 00e0.8f0d.4b01

MAC-адрес коммутатора S2: 0060.3e35.c101




# Просмотр таблицы MАС-адресов коммутатора:
   * До тестирования сетевой связи с помощью эхо-запросов:    


![alt text](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab2-6.PNG)


MAC-адрес 00e0.8f0d.4b01 принадлежит коммутатору S1

   * После тестирования сетевой связи с помощью эхо-запросов:

![alt text](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab2-7.PNG)

Мы видим MAС-адреса PC-A, PC-B, и S1

# Если вы не записали МАС-адреса сетевых устройств в шаге 1, как можно определить, каким устройствам принадлежат МАС-адреса, используя только выходные данные команды show mac address-table? Работает ли это решение в любой ситуации?

При выполнении команды show mac address-table мы видим какому порту соответствует мак адрес, таким образом мы можем определить какой MAC-адрес какому устройству принадлежит.

# Очистите таблицу МАС-адресов коммутатора S2 и снова отобразите таблицу МАС-адресов.

	В привилегированном режиме EXEC введите команду clear mac address-table dynamic и нажмите клавишу Enter.
S2# clear mac address-table dynamic

	Снова быстро введите команду show mac address-table.
 
Вопросы:
   * Указаны ли в таблице МАС-адресов адреса для VLAN 1? Указаны ли другие МАС-адреса?

![alt text](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab2-10.PNG)

После выполнения команды clear mac address-table dynamic таблица Mac адресов полностью очищена

  * Через 10 секунд введите команду show mac address-table и нажмите клавишу ввода. Появились ли в таблице МАС-адресов новые адреса?



![alt text](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab2-11.PNG)

Через 10 секунд при вводе команды show mac address-table появляется Mac адрес S1

# С компьютера PC-B отправьте эхо-запросы устройствам в сети и просмотрите таблицу МАС-адресов коммутатора.

![alt text](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab2-12.PNG)

Видим что от всех устройств у нас есть ответ



На компьютере PC-B откройте командную строку и еще раз введите команду arp -a.

![alt text](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab2-14.PNG)


Не считая адресов многоадресной и широковещательной рассылки, сколько пар IP- и МАС-адресов устройств было получено через протокол ARP?

Мы получили 3 пары IP И MAC адресов через протокол ARP

# 	Подключившись через консоль к коммутатору S2, введите команду show mac address-table. 

![alt text](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab2-13.PNG)

# Добавил ли коммутатор в таблицу МАС-адресов дополнительные МАС-адреса? Если да, то какие адреса и устройства?

Да добавил MACадрес 000b.be6b.b7da





























