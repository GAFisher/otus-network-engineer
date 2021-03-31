# Реализация DHCPv6
## Задание: 
1. Создать сеть и настроить основные параметры устройств
2. Проверить назначения адреса SLAAC от R1
3. Настроить и проверить сервера DHCPv6 без сохранения состояния на R1
4. Настроить и проверка состояния DHCPv6 сервера на R1
5. Настроить и проверить DHCPv6 Relay на R2
## Топология
![](Topology.png)
## Таблица адресации
|  				Device 			 |  				Interface 			 |       				IPv6 Address 			      |
|:--------:|:-----------:|:------------------------:|
|  				R1 			     |  				G0/0/0 			    |  				2001:db8:acad:2::1 /64 			 |
|  				R1 			     |  				G0/0/0 			    |  				fe80::1 			                |
|  				R1 			     |  				G0/0/1 			    |  				2001:db8:acad:1::1/64 			  |
|  				R1 			     |  				G0/0/1 			    |  				fe80::1 			                |
|  				R2 			     |  				G0/0/0 			    |  				2001:db8:acad:2::2/64 			  |
|  				R2 			     |  				G0/0/0 			    |  				fe80::2 			                |
|  				R2 			     |  				G0/0/1 			    |  				2001:db8:acad:3::1 /64 			 |
|  				R2 			     |  				G0/0/1 			    |  				fe80::1 			                |
|  				PC-A 			   |  				NIC 			       |  				DHCP 			                   |
|  				PC-B 			   |  				NIC 			       |  				DHCP 			                   |
## Решение:
1. Создание сети и настройка основных параметров устройства
	* Настроим базовые параметры каждого коммутатора
	* Произведем базовую настройку маршрутизаторов
	* Настроим интерфейсы и маршрутизацию для обоих маршрутизаторов
2. Проверка назначения адреса SLAAC от R1
3. Настройка и проверка сервера DHCPv6 на R1
	* Настроим R1 для предоставления DHCPv6 без состояния для PC-A
4. Настройка сервера DHCPv6 с сохранением состояния на R1
5. Настройка и проверка  DHCPv6 relay на R2
 	* Включим PC-B и проверим адрес SLAAC, который он генерирует
 	* Настроим R2 в качестве агента DHCP relay для локальной сети на G0/1
 	* Попытаемся получить адрес IPv6 из DHCPv6 на PC-B



 
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
### Произведите базовую настройку маршрутизаторов
##### Маршрутизатор R1:
```
Router>enable
Router#configure terminal
Router(config)#hostname R1
R1(config)#no ip domain lookup
R1(config)#enable secret class
R1(config)#line console 0
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#line vty 0 4
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#service password-encryption 
R1(config)#banner motd #Authorized Access Only!#
R1(config)#ipv6 unicast-routing
R1(config)#end 
R1#copy running-config startup-config                              
Destination filename [startup-config]? 
Building configuration...
[OK]
R1#
```
##### Маршрутизатор R2:
```
Router>enable
Router#configure terminal
Router(config)#hostname R2
R2(config)#no ip domain lookup
R2(config)#enable secret class
R2(config)#line console 0
R2(config-line)#password cisco
R2(config-line)#login
R2(config-line)#exit
R2(config)#line vty 0 4
R2(config-line)#password cisco
R2(config-line)#login
R2(config-line)#exit
R2(config)#service password-encryption 
R2(config)##banner motd #Authorized Access Only!#
R2(config)#ipv6 unicast-routing
R2(config)#end
R2#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
R2#
```
### Настроим интерфейсы и маршрутизацию для обоих маршрутизаторов
##### Маршрутизатор R1:
```
R1#configure terminal
R1(config)#interface GigabitEthernet0/0 
R1(config-if)#ipv6 address 2001:db8:acad:2::1/64 
R1(config-if)#ipv6 address fe80::1 link-local
R1(config-if)#no shutdown
R1(config-if)#exit
R1(config)#interface GigabitEthernet0/1
R1(config-if)#ipv6 address 2001:db8:acad:1::1/64
R1(config-if)#ipv6 address fe80::1 link-local
R1(config-if)#no shutdown
R1(config-if)#exit
R1(config)#ipv6 route ::/0 2001:DB8:ACAD:2::2
R1(config)#end
R1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
R1#
```
##### Маршрутизатор R2:
```
R2#configure terminal
R2(config)#interface GigabitEthernet0/0
R2(config-if)#ipv6 address 2001:db8:acad:2::2/64
R2(config-if)#ipv6 address fe80::2 link-local
R2(config-if)#no shutdown 
R2(config-if)#exit
R2(config)#interface GigabitEthernet0/1
R2(config-if)#ipv6 address 2001:db8:acad:3::1/64 
R2(config-if)#ipv6 address fe80::1 link-local
R2(config-if)#no shutdown
R2(config-if)#exit
R2(config)#ipv6 route ::/0 2001:DB8:ACAD:2::1
R2(config)#end
R2#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
R2#
```
##### Убедимся, что маршрутизация работает с помощью пинга:
```
R1#ping 2001:DB8:ACAD:2::2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:DB8:ACAD:2::2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
R1#
```

## 2. Проверка назначения адреса SLAAC от R1
```
PC-A> ip auto  
GLOBAL SCOPE      : 2001:db8:acad:1:2050:79ff:fe66:6807/64
ROUTER LINK-LAYER : 50:00:00:0b:00:01

PC-A> show ipv6

NAME              : PC-A[1]
LINK-LOCAL SCOPE  : fe80::250:79ff:fe66:6807/64
GLOBAL SCOPE      : 2001:db8:acad:1:2050:79ff:fe66:6807/64
DNS               : 
ROUTER LINK-LAYER : 50:00:00:0b:00:01
MAC               : 00:50:79:66:68:07
LPORT             : 20000
RHOST:PORT        : 127.0.0.1:30000
MTU:              : 1500

PC-A>
```
## 3. Настройка и проверка сервера DHCPv6 на R1
### Настроим R1 для предоставления DHCPv6 без сохранения состояния для PC-A
```
R1#configure terminal 
R1(config)#ipv6 dhcp pool R1-STATELESS
R1(config-dhcpv6)#dns-server 2001:db8:acad::254
R1(config-dhcpv6)#domain-name STATELESS.com
R1(config-dhcpv6)#exit
R1(config)#interface GigabitEthernet0/1
R1(config-if)#ipv6 nd other-config-flag
R1(config-if)#ipv6 dhcp server R1-STATELESS
R1(config-if)#end
R1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
R1#
```
#### Перезапустим PC-A и проверим подключение с помощью пинга IP-адреса интерфейса R1 G0/1 



## 4. Настройка сервера DHCPv6 с сохранением состояния на R1
```
R1(config)#ipv6 dhcp pool R2-STATEFUL
R1(config-dhcpv6)#address prefix 2001:db8:acad:3:aaa::/80
R1(config-dhcpv6)#dns-server 2001:db8:acad::254
R1(config-dhcpv6)#domain-name STATEFUL.com
R1(config-dhcpv6)#exit
R1(config)#interface GigabitEthernet0/0
R1(config-if)#ipv6 dhcp server R2-STATEFUL
R1(config-if)#exit
R1(config)#
```
## 5. Настройка и проверка ретрансляции DHCPv6 на R2
### Включим PC-B и проверим адрес SLAAC, который он генерирует
```
PC-B> show ipv6

NAME              : PC-B[1]
LINK-LOCAL SCOPE  : fe80::250:79ff:fe66:6808/64
GLOBAL SCOPE      : 2001:db8:acad:3:2050:79ff:fe66:6808/64
DNS               : 
ROUTER LINK-LAYER : 50:00:00:0c:00:01
MAC               : 00:50:79:66:68:08
LPORT             : 20000
RHOST:PORT        : 127.0.0.1:30000
MTU:              : 1500

PC-B> 
```
### Настроим R2 в качестве агента DHCP relay для локальной сети на G0/1
```
R2#configure terminal 
R2(config)#interface GigabitEthernet0/1
R2(config-if)#ipv6 nd managed-config-flag
R2(config-if)#ipv6 dhcp relay destination 2001:db8:acad:2::1  Gi0/0
R2(config-if)#end
R2#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
R2#
```
### Попытаемся получить адрес IPv6 из DHCPv6 на PC-B
#### Перезапустим PC-B и проверим подключение с помощью пинга IP-адреса интерфейса R1 G0/1
