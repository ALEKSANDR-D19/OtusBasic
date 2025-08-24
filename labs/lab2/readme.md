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


# Просмотр таблицы MАС-адресов коммутатора:
   * До тестирования сетевой связи с помощью эхо-запросов:    00e0.8f0d.4b01













