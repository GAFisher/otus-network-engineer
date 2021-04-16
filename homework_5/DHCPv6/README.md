# Реализация DHCPv6
## Задание: 
1. Создать сеть и настроить основные параметры устройств
2. Проверить назначения адреса SLAAC от R1
3. Настроить и проверить сервера DHCPv6 без сохранения состояния на R1
4. Настроить и проверка состояния DHCPv6 сервера на R1
5. Настроить и проверить DHCPv6 Relay на R2
## Таблица адресации
| Device | Interface   | IPv6 Address           |
|--------|-------------|------------------------|
| R1     | Ethernet0/0 | 2001:db8:acad:2::1 /64 |
| R1     | Ethernet0/0 | fe80::1                |
| R1     | Ethernet0/1 | 2001:db8:acad:1::1/64  |
| R1     | Ethernet0/1 | fe80::1                |
| R2     | Ethernet0/0 | 2001:db8:acad:2::2/64  |
| R2     | Ethernet0/0 | fe80::2                |
| R2     | Ethernet0/1 | 2001:db8:acad:3::1 /64 |
| R2     | Ethernet0/1 | fe80::1                |
| PC-A   | NIC         | DHCP                   |
| PC-B   | NIC         | DHCP                   |
| R-A    | Ethernet0/0 | DHCP                   |
| R-B    | Ethernet0/0 | DHCP                   |			                   |
## Решение:
1. [Создание сети и настройка основных параметров устройства](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/DHCPv6/README.md#1-%D1%81%D0%BE%D0%B7%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-%D1%81%D0%B5%D1%82%D0%B8-%D0%B8-%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-%D0%BE%D1%81%D0%BD%D0%BE%D0%B2%D0%BD%D1%8B%D1%85-%D0%BF%D0%B0%D1%80%D0%B0%D0%BC%D0%B5%D1%82%D1%80%D0%BE%D0%B2-%D1%83%D1%81%D1%82%D1%80%D0%BE%D0%B9%D1%81%D1%82%D0%B2%D0%B0)
	* [Настроим базовые параметры каждого коммутатора](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/DHCPv6/README.md#%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B8%D0%BC-%D0%B1%D0%B0%D0%B7%D0%BE%D0%B2%D1%8B%D0%B5-%D0%BF%D0%B0%D1%80%D0%B0%D0%BC%D0%B5%D1%82%D1%80%D1%8B-%D0%BA%D0%B0%D0%B6%D0%B4%D0%BE%D0%B3%D0%BE-%D0%BA%D0%BE%D0%BC%D0%BC%D1%83%D1%82%D0%B0%D1%82%D0%BE%D1%80%D0%B0)
	* [Произведем базовую настройку маршрутизаторов](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/DHCPv6/README.md#%D0%BF%D1%80%D0%BE%D0%B8%D0%B7%D0%B2%D0%B5%D0%B4%D0%B8%D1%82%D0%B5-%D0%B1%D0%B0%D0%B7%D0%BE%D0%B2%D1%83%D1%8E-%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D1%83-%D0%BC%D0%B0%D1%80%D1%88%D1%80%D1%83%D1%82%D0%B8%D0%B7%D0%B0%D1%82%D0%BE%D1%80%D0%BE%D0%B2)
	* [Настроим интерфейсы и маршрутизацию для обоих маршрутизаторов](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/DHCPv6/README.md#%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B8%D0%BC-%D0%B8%D0%BD%D1%82%D0%B5%D1%80%D1%84%D0%B5%D0%B9%D1%81%D1%8B-%D0%B8-%D0%BC%D0%B0%D1%80%D1%88%D1%80%D1%83%D1%82%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8E-%D0%B4%D0%BB%D1%8F-%D0%BE%D0%B1%D0%BE%D0%B8%D1%85-%D0%BC%D0%B0%D1%80%D1%88%D1%80%D1%83%D1%82%D0%B8%D0%B7%D0%B0%D1%82%D0%BE%D1%80%D0%BE%D0%B2)
2. [Проверка назначения адреса SLAAC от R1](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/DHCPv6/README.md#2-%D0%BF%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D0%BA%D0%B0-%D0%BD%D0%B0%D0%B7%D0%BD%D0%B0%D1%87%D0%B5%D0%BD%D0%B8%D1%8F-%D0%B0%D0%B4%D1%80%D0%B5%D1%81%D0%B0-slaac-%D0%BE%D1%82-r1)
3. [Настройка и проверка сервера DHCPv6 на R1](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/DHCPv6/README.md#3-%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-%D0%B8-%D0%BF%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D0%BA%D0%B0-%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0-dhcpv6-%D0%BD%D0%B0-r1)
	* [Настроим R1 для предоставления DHCPv6 без сохранения состояния для PC-A](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/DHCPv6/README.md#%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B8%D0%BC-r1-%D0%B4%D0%BB%D1%8F-%D0%BF%D1%80%D0%B5%D0%B4%D0%BE%D1%81%D1%82%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D1%8F-dhcpv6-%D0%B1%D0%B5%D0%B7-%D1%81%D0%BE%D1%85%D1%80%D0%B0%D0%BD%D0%B5%D0%BD%D0%B8%D1%8F-%D1%81%D0%BE%D1%81%D1%82%D0%BE%D1%8F%D0%BD%D0%B8%D1%8F-%D0%B4%D0%BB%D1%8F-pc-a)
4. [Настройка сервера DHCPv6 с сохранением состояния на R1](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/DHCPv6/README.md#4-%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0-dhcpv6-%D1%81-%D1%81%D0%BE%D1%85%D1%80%D0%B0%D0%BD%D0%B5%D0%BD%D0%B8%D0%B5%D0%BC-%D1%81%D0%BE%D1%81%D1%82%D0%BE%D1%8F%D0%BD%D0%B8%D1%8F-%D0%BD%D0%B0-r1)
5. [Настройка и проверка  DHCPv6 relay на R2](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/DHCPv6/README.md#5-%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-%D0%B8-%D0%BF%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D0%BA%D0%B0-%D1%80%D0%B5%D1%82%D1%80%D0%B0%D0%BD%D1%81%D0%BB%D1%8F%D1%86%D0%B8%D0%B8-dhcpv6-%D0%BD%D0%B0-r2)
 	* [Включим PC-B и проверим адрес SLAAC, который он генерирует](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/DHCPv6/README.md#%D0%B2%D0%BA%D0%BB%D1%8E%D1%87%D0%B8%D0%BC-pc-b-%D0%B8-%D0%BF%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D0%B8%D0%BC-%D0%B0%D0%B4%D1%80%D0%B5%D1%81-slaac-%D0%BA%D0%BE%D1%82%D0%BE%D1%80%D1%8B%D0%B9-%D0%BE%D0%BD-%D0%B3%D0%B5%D0%BD%D0%B5%D1%80%D0%B8%D1%80%D1%83%D0%B5%D1%82)
 	* [Настроим R2 в качестве агента DHCP relay для локальной сети на G0/1](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/DHCPv6/README.md#%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B8%D0%BC-r2-%D0%B2-%D0%BA%D0%B0%D1%87%D0%B5%D1%81%D1%82%D0%B2%D0%B5-%D0%B0%D0%B3%D0%B5%D0%BD%D1%82%D0%B0-dhcp-relay-%D0%B4%D0%BB%D1%8F-%D0%BB%D0%BE%D0%BA%D0%B0%D0%BB%D1%8C%D0%BD%D0%BE%D0%B9-%D1%81%D0%B5%D1%82%D0%B8-%D0%BD%D0%B0-g01)
 	* [Попытаемся получить адрес IPv6 из DHCPv6 на PC-B](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/DHCPv6/README.md#%D0%BF%D0%BE%D0%BF%D1%8B%D1%82%D0%B0%D0%B5%D0%BC%D1%81%D1%8F-%D0%BF%D0%BE%D0%BB%D1%83%D1%87%D0%B8%D1%82%D1%8C-%D0%B0%D0%B4%D1%80%D0%B5%D1%81-ipv6-%D0%B8%D0%B7-dhcpv6-%D0%BD%D0%B0-pc-b)



 
## 1. Создание сети и настройка основных параметров устройства
## Топология
![]()
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
R1(config)#interface Ethernet0/0 
R1(config-if)#ipv6 address fe80::1 link-local
R1(config-if)#ipv6 address 2001:db8:acad:2::1/64 
R1(config-if)#no shutdown
R1(config-if)#exit
R1(config)#interface  Ethernet0/1 
R1(config-if)#ipv6 address fe80::1 link-local
R1(config-if)#ipv6 address 2001:db8:acad:1::1/64
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
R2(config)#interface Ethernet0/0
R2(config-if)#ipv6 address fe80::2 link-local
R2(config-if)#ipv6 address 2001:db8:acad:2::2/64
R2(config-if)#no shutdown
R2(config-if)#exit
R2(config)#interface Ethernet0/1
R2(config-if)#ipv6 address fe80::1 link-local
R2(config-if)#ipv6 address 2001:db8:acad:3::1/64
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
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/9/35 ms
R1#
```

## 2. Проверка назначения адреса SLAAC от R1
```
PC-A> ip auto
GLOBAL SCOPE      : 2001:db8:acad:1:2050:79ff:fe66:6807/64
ROUTER LINK-LAYER : aa:bb:cc:00:b0:10

PC-A> show ipv6

NAME              : PC-A[1]
LINK-LOCAL SCOPE  : fe80::250:79ff:fe66:6807/64
GLOBAL SCOPE      : 2001:db8:acad:1:2050:79ff:fe66:6807/64
DNS               : 
ROUTER LINK-LAYER : aa:bb:cc:00:b0:10
MAC               : 00:50:79:66:68:07
LPORT             : 20000
RHOST:PORT        : 127.0.0.1:30000
MTU:              : 1500

PC-A> 
```
## 3. Настройка и проверка сервера DHCPv6 на R1
Маршрутизатор также может быть клиентом DHCPv6.
## Топология
![]()
### Настроим маршрутизатор R-A в качестве DHCPv6-сервера без отслеживания состояния
```
Router>enable
Router#configure terminal
Router(config)#hostname R-A
R-A(config)#no ip domain lookup
R-A(config)#enable secret class
R-A(config)#line console 0
R-A(config-line)#password cisco
R-A(config-line)#login
R-A(config-line)#exit
R-A(config)#line vty 0 4
R-A(config-line)#password cisco
R-A(config-line)#login
R-A(config-line)#exit
R-A(config)#service password-encryption 
R-A(config)#banner motd #Authorized Access Only!#
R-A(config)#ipv6 unicast-routing
R-A(config)#interface Ethernet0/0 
R-A(config)#no shutdown
R-A(config-if)#ipv6 enable 
R-A(config-if)#ipv6 address autoconfig
R-A(config)#end 
R-A#copy running-config startup-config                              
Destination filename [startup-config]? 
Building configuration...
[OK]
R-A#
```
### Настроим R1 для предоставления DHCPv6 без сохранения состояния для R-A
```
R1#configure terminal 
R1(config)#ipv6 dhcp pool R1-STATELESS
R1(config-dhcpv6)#dns-server 2001:db8:acad::254
R1(config-dhcpv6)#domain-name STATELESS.com
R1(config-dhcpv6)#exit
R1(config)#interface Ethernet0/1
R1(config-if)#ipv6 nd other-config-flag
R1(config-if)#ipv6 dhcp server R1-STATELESS
R1(config-if)#end
R1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
R1#
```
####  Убедимся, что маршрутизатору R-A назначен GUA и получена другая необходимая информация DHCPv6
```
R-A# show ipv6 interface brief          
Ethernet0/0            [up/up]
    FE80::A8BB:CCFF:FE00:F000
    2001:DB8:ACAD:1:A8BB:CCFF:FE00:F000
Ethernet0/1            [administratively down/down]
    unassigned
Ethernet0/2            [administratively down/down]
    unassigned
Ethernet0/3            [administratively down/down]
    unassigned
R-A#show ipv6 dhcp interface Ethernet0/0
Ethernet0/0 is in client mode
  Prefix State is IDLE (0)
  Information refresh timer expires in 23:57:07
  Address State is IDLE
  List of known servers:
    Reachable via address: FE80::1
    DUID: 00030001AABBCC00B000
    Preference: 0
    Configuration parameters:
      DNS server: 2001:DB8:ACAD::254
      Domain name: STATELESS.com
      Information refresh time: 0
  Prefix Rapid-Commit: disabled
  Address Rapid-Commit: disabled
R-A#
```
#### Проверим подключение с помощью пинга IP-адреса интерфейса Ethernet0/1 R2
```
R-A#ping 2001:DB8:ACAD:1::1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:DB8:ACAD:1::1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/5/24 ms
R-A#
```
## 4. Настройка сервера DHCPv6 с сохранением состояния на R1
```
R1(config)#ipv6 dhcp pool R2-STATEFUL
R1(config-dhcpv6)#address prefix 2001:db8:acad:3:aaa::/80
R1(config-dhcpv6)#dns-server 2001:db8:acad::254
R1(config-dhcpv6)#domain-name STATEFUL.com
R1(config-dhcpv6)#exit
R1(config)#interface Ethernet0/0
R1(config-if)#ipv6 dhcp server R2-STATEFUL
R1(config-if)#exit
R1(config)#
```
## 5. Настройка и проверка ретрансляции DHCPv6 на R2
### Настроим маршрутизатор R-B
```
Router>enable
Router#configure terminal
Router(config)#hostname R-B
R-B(config)#no ip domain lookup
R-B(config)#enable secret class
R-B(config)#line console 0
R-B(config-line)#password cisco
R-B(config-line)#login
R-B(config-line)#exit
R-B(config)#line vty 0 4
R-B(config-line)#password cisco
R-B(config-line)#login
R-B(config-line)#exit
R-B(config)#service password-encryption 
R-B(config)#banner motd #Authorized Access Only!#
R-B(config)#ipv6 unicast-routing
R-B(config)#interface Ethernet0/0
R-B(config-if)#no shutdown 
R-B(config-if)#ipv6 address dhcp
R-B(config)#end 
R-B#copy running-config startup-config                              
Destination filename [startup-config]? 
Building configuration...
[OK]
R-B#
```
### Настроим R2 в качестве агента DHCP relay для локальной сети на G0/1
```
R2#configure terminal 
R2(config)#interface Ethernet0/1
R2(config-if)#ipv6 nd managed-config-flag
R2(config-if)#ipv6 dhcp relay destination 2001:db8:acad:2::1  Ethernet0/0
R2(config-if)#end
R2#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
R2#
```
### Убедимся, что маршрутизатору R-B назначен GUA и получена другая необходимая информация DHCPv6
```
R-B#show ipv6 interface brief            
Ethernet0/0            [up/up]
    FE80::A8BB:CCFF:FE01:1000
    2001:DB8:ACAD:3:AAA:FB13:D82D:8A6D
Ethernet0/1            [administratively down/down]
    unassigned
Ethernet0/2            [administratively down/down]
    unassigned
Ethernet0/3            [administratively down/down]
    unassigned
R-B# show ipv6 dhcp interface Ethernet0/0
Ethernet0/0 is in client mode
  Prefix State is IDLE
  Address State is OPEN
  Renew for address will be sent in 11:59:03
  List of known servers:
    Reachable via address: FE80::1
    DUID: 00030001AABBCC00B000
    Preference: 0
    Configuration parameters:
      IA NA: IA ID 0x00030001, T1 43200, T2 69120
        Address: 2001:DB8:ACAD:3:AAA:FB13:D82D:8A6D/128
                preferred lifetime 86400, valid lifetime 172800
                expires at Apr 18 2021 03:39 PM (172743 seconds)
      DNS server: 2001:DB8:ACAD::254
      Domain name: STATEFUL.com
      Information refresh time: 0
  Prefix Rapid-Commit: disabled
  Address Rapid-Commit: disabled
R-B#
```
#### Проверим подключение с помощью пинга IP-адреса интерфейса Ethernet0/1 R2
