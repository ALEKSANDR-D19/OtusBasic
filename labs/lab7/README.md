# Лабораторная работа. Развертывание коммутируемой сети с резервными каналами

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab7-1.PNG)

Цели

  * Создание сети и настройка основных параметров устройства
  * Выбор корневого моста
  * Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов
  * Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов

# Выполнение
  * Создание сети согласно топологии

    ![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab7-2.PNG)

  * Базовые настройки коммутаторов S1, S2, S3

```
enable

clock set 16:30:00 17 nov 2025

configure

no ip domain-lookup

hostname S1(S2, S3)

banner motd #!!!!STOP!!!!!#

int range fa0/1, fa0/2, fa0/3, fa0/4 

no shutdown

exit

service password-encryption

enable secret class

interface vlan 1

ip address 192.168.1.1 255.255.255.0 (ip address 192.168.1.2 255.255.255.0, ip address 192.168.1.3 255.255.255.0 )

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

  * Проверка связи

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab7-3.PNG)


  * Определение корневого моста

```
int range fa0/1-24

shutdown

exit

int range g0/1-2

shutdown 

exit

int range fa0/1-4

switchport mode trunk

exit

int range fa0/2,fa0/4

no shutdown 


end

write 

```
Вывод команды *show spanning-tree*:

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab7-4.PNG)

Роли STP:

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab7-5.JPG)


Какой коммутатор является корневым мостом? ___Коммутатор S1 является корневым___________

Почему этот коммутатор был выбран протоколом spanning-tree в качестве корневого моста?
__________________Потому что у него самое низкое знамение Mac адреса_____________________________________________________________________

Какие порты на коммутаторе являются корневыми портами? __ _____На коммутаторе S2 корневой порт fa0/2, на коммутаторе S3 - fa0/4____________________________

Какие порты на коммутаторе являются назначенными портами? _____S1 - fa0/2,fa0/4 S3 - fa0/2___________________________

Какой порт отображается в качестве альтернативного и в настоящее время заблокирован? _____S2 fa0/4_______________________

Почему протокол spanning-tree выбрал этот порт в качестве невыделенного (заблокированного) порта?
____Потому что коммутатор S1 является корневым__________________________________________________________________________________

