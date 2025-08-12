# Базовая настройка коммутатора

# Задачи:
      Часть 1. Проверка конфигурации коммутатора по умолчанию

    
      Часть 2. Создание сети и настройка основных параметров устройства
  *       Настройте базовые параметры коммутатора.

  *       Настройте IP-адрес для ПК.

    Часть 3. Проверка сетевых подключений

  *   Отобразите конфигурацию устройства.

  *     Протестируйте сквозное соединение, отправив эхо-запрос.

  *         Протестируйте возможности удаленного управления с помощью Telnet.


![alt-текст](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/Снимок.PNG)

# Выполнение:
      Часть 1. Создать сеть согласно топологии.

![alt-текст](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/1.PNG)
# Подсоединён консольный кабель. Кабель Ethernet на данном этапе не подключен.


# Установлено консольное подключение к коммутатору с компьютера PC-A:

![alt-текст](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/2.PNG)

Для первоначальной настройки коммутатора возможно только консольное подключение, для подключения по Telnet и SSH необходимо выполнить настройку коммутатора.

# Проверка настроек коммутатора по умолчанию: 
enable

show running-config

![alt-текст](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/3.PNG)
  * Коммутатор 2960 исеет 24 интерфейса FastEthernet и 2 интерфейса Gigabit Ethernet
  * Диапазон VTY линий - 0 15
  * При вводе команды show startup configuration появляется сообщение startup-config is not present, потому что файл ещё не создан.
  * Ip адрес сети Vlan1 не назначен, интерфейс выключен.
  * При подключении кабеля Ethernet компьютера к порту 6 коммутатора появляется сообщение %LINK-5-CHANGED: Interface FastEthernet0/6, changed state to up


show version
  * Версия Cisco IOS - 15.0(2)SE4
  * Файл образа системы C2960-LANBASEK9-M

![alt-текст](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/4.PNG)

  * Интерфейс F0/6 включен
  * mac адреса нет
  * Full-duplex, 100Mb/s

show flash

  * Образу Cisco IOS присвоено имя 2960-lanbasek9-mz.150-2.SE4.bin

    Часть 2. Настройка базовых параметров сетевых устройств


no ip domain-lookup

hostname S1

service password-encryption

enable secret class

banner motd #!!!!STOP!!!!!#

<img width="620" height="998" alt="image" src="https://github.com/user-attachments/assets/21676082-ac9b-4fa6-a1f5-a850896ef11e" />

Команда login необходима для включения пароля в работу.

# Настройка каналов виртуального соединения для удаленного управления (vty).

   configure 
   
   configure terminal
   
   line vty 0 4
   
   password cisco
   
   login
   
   end

# Настройка IP-адрес на компьютере PC-A

![alt-текст](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/6.PNG)

# Проверка сетевых подключений

![alt-текст](https://github.com/ALEKSANDR-D19/OtusBasic/blob/main/Jpeg/7.PNG)

  * Подключение по TelNet












      


      
