# Развертывание коммутируемой сети с резервными каналами
## Задание: 

## Топология

## Таблица адресации
|  				Устройство 			 |  				Интерфейс 			 |    				IP-адрес 			  |  				Маска подсети 			 |
|:------------:|:-----------:|:-------------:|:---------------:|
|  				S1 			         |  				VLAN 1 			    |  				192.168.1.1 			 |  				255.255.255.0 			 |
|  				S2 			         |  				VLAN 1 			    |  				192.168.1.2 			 |  				255.255.255.0 			 |
|  				S3 			         |  				VLAN 1 			    |  				192.168.1.3 			 |  				255.255.255.0 			 |
## Решение:
1. Создание сети и настройка основных параметров устройств
    * Настроим базовые параметры каждого коммутатора
    
    
    
    
    
    
    
    
    
## 1. Создание сети и настройка основных параметров устройств
#### Настроим базовые параметры каждого коммутатора:

```
Switch>enable
Switch#configure terminal
Switch(config)#no ip domain-lookup
Switch(config)#hostname S1
S1(config)#enable secret class
S1(config)#line console 0
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#logging synchronous
S1(config-line)#exit
S1(config)#line vty 0 4
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#banner motd #Authorized Access Only!#
S1(config)#interface vlan 1
S1(config-if)#ip address 192.168.1.1 255.255.255.0
S1(config-if)#no shutdown
S1(config-if)#end
S1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
Compressed configuration from 3204 bytes to 1578 bytes[OK]
S1#
```
```
Switch>enable
Switch#configure terminal
Switch(config)#no ip domain-lookup
Switch(config)#hostname S2
S2(config)#enable secret class
S2(config)#line console 0
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#logging synchronous
S2(config-line)#exit
S2(config)#line vty 0 4
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#banner motd #Authorized Access Only!#
S2(config)#interface vlan 1
S2(config-if)#ip address 192.168.1.2 255.255.255.0
S2(config-if)#no shutdown
S2(config-if)#end
S2#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
Compressed configuration from 3204 bytes to 1580 bytes[OK]
S2#
```
```
Switch>enable
Switch#configure terminal
Switch(config)#no ip domain-lookup
Switch(config)#hostname S3
S3(config)#enable secret class
S3(config)#line console 0
S3(config-line)#password cisco
S3(config-line)#login
S3(config-line)#logging synchronous
S3(config-line)#exit
S3(config)#line vty 0 4
S3(config-line)#password cisco
S3(config-line)#login
S3(config-line)#exit
S3(config)#banner motd #Authorized Access Only!#
S3(config)#interface vlan 1
S3(config-if)#ip address 192.168.1.3 255.255.255.0
S3(config-if)#no shutdown
S3(config-if)#end
S3#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
Compressed configuration from 3204 bytes to 1571 bytes[OK]
S3#
```
