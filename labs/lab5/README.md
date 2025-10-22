# Лабораторная работа. Доступ к сетевым устройствам по протоколу SSH

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/lab5.1.PNG)

# Задачи:
  * Настройка основных параметров устройства
  * Настройка маршрутизатора для доступа по протоколу SSH
  * Настройка коммутатора для доступа по протоколу SSH
  * SSH через интерфейс командной строки (CLI) коммутатора

# Выполнение:
  * Настройка коммутатора:
```
enable

clock set 16:30:00 22 oct 2025

configure

no ip domain-lookup

hostname S1

banner motd #!!!!STOP!!!!!#

int fa0/5

no shutdown

exit

int fa0/6

no shutdown

exit

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

write 
```







  * Настройка маршрутизатора:

```
enable

clock set 16:30:00 22 oct 2025

configure

no ip domain-lookup

hostname R1

banner motd #!!!!STOP!!!!!#

service password-encryption

enable secret class

banner motd #!!!!STOP!!!!!#

int g0/0/1

ip address 192.168.1.1 255.255.255.0

no shutdown

exit

line vty 0 4

password cisco

login

end

write
```
  * Настройка  PC-A:

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab53.PNG)

  * Проверка подключения:

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab5.4.PNG)


  *  Настройка маршрутизатора для доступа по протоколу SSH

```
configure

ip domain-name otus.ru

crypto key generate rsa

2048

ip ssh version 2

username admin  secret Adm1nP @55

username superadmin privilege 15 secret cisco

line vty 0 4

login local

transport input all

end

write
```

  * Аналогично настраиваем коммутатор

  * Проверка работы:

![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab5.5.PNG)
![](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Lab5.6.PNG)


 # 	Вопрос для повторения

 Как предоставить доступ к сетевому устройству нескольким пользователям, у каждого из которых есть собственное имя пользователя?

 # Ответ: 

 Настраиваем SSH и добывляем несколько пользователей командой username

 

 













    


