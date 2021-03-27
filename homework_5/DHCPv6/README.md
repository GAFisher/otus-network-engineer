# Реализация DHCPv6
## Задание: 
1. Создать сеть и настроить основные параметры устройств
2. Проверить назначения адреса SLAAC от R1
3. Настроить и проверить сервера DHCPv6 без гражданства на R1
4. Настроить и проверка состояния DHCPv6 сервера на R1
5. Настроить и проверить DHCPv6 Relay на R2
## Топология
![](Topology.png)
## Таблица адресации
## Решение:
1. Создание сети и настройка основных параметров устройства
	* Настроим базовые параметры каждого коммутатора
  
## 1. Создание сети и настройка основных параметров устройства
### Настроим базовые параметры каждого коммутатора
##### Коммутатор S1:
```
Switch>enable 
Switch#configure terminal
Switch(config)#hostname S1
S1(config)#no ip domain lookup
S1(config)#enable secret class
S1(config)#line console 0
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#line vty 0 4
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#service password-encryption 
S1(config)#banner motd #Authorized Access Only!#
S1(config)#interface range Et0/2-3
S1(config-if-range)#shutdown 
S1(config-if-range)#end
S1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
Compressed configuration from 954 bytes to 700 bytes[OK]
S1#
```
##### Коммутатор S2:
```
Switch>enable
Switch#configure terminal
Switch(config)#hostname S2
S2(config)#no ip domain lookup
S2(config)#enable secret class
S2(config)#line console 0
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#line vty 0 4
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#service password-encryption 
S2(config)#banner motd #Authorized Access Only!#
S2(config)#interface range Et0/1, Et0/2
S2(config-if-range)#shutdown
S2(config-if-range)#end
S2#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
Compressed configuration from 954 bytes to 694 bytes[OK]
S2#
```














