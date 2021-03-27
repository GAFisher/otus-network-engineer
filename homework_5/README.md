# Реализация DHCPv4
## Задание: 
1. Создать сеть и настроить основные параметры устройств
2. Настроить и проверить работу двух серверов DHCPv4 на R1
3. Настроить и проверить работу DHCP Relay на R2
## Топология
![](topology.png)
## Таблица адресации
|  				Device 			 |   				Interface 			  |  				IP Address 			 |    				Subnet Mask 			   |  				Default Gateway 			 |
|:--------:|:-------------:|:------------:|:-----------------:|:-----------------:|
|  				R1 			     |  				G0/0/0 			      |  				10.0.0.1 			   |  				255.255.255.252 			 |  				N/A 			             |
|  				R1 			     |  				G0/0/1 			      |  				N/A 			        |  				N/A 			             |  				N/A 			             |
|  				R1 			     |  				G0/0/1.100 			  | 192.168.1.1  | 255.255.255.192   |  				N/A 			             |
|  				R1 			     |  				G0/0/1.200 			  | 192.168.1.65 | 255.255.255.224   |  				N/A 			             |
|  				R1 			     |  				G0/0/1.1000 			 |  				N/A 			        |  				N/A 			             |  				N/A 			             |
|  				R2 			     |  				G0/0/0 			      |  				10.0.0.2 			   |  				255.255.255.252 			 |  				N/A 			             |
|  				R2 			     |  				G0/0/1 			      | 192.168.1.97 | 255.255.255.240   |  				N/A 			             |
|  				S1 			     |  				VLAN 200 			    | 192.168.1.66 | 255.255.255.224   | 192.168.1.65      |
|  				S2 			     |  				VLAN 1 			      | 192.168.1.98 | 255.255.255.240   | 192.168.1.97      |
|  				PC-A 			   |  				NIC 			         |  				DHCP 			       |  				DHCP 			            |  				DHCP 			            |
|  				PC-B 			   |  				NIC 			         |  				DHCP 			       |  				DHCP 			            |  				DHCP 			            |
## Таблица VLAN
|  					VLAN 				 |      					Name 				    |  					Interface Assigned 				 |
|:------:|:-------------:|:--------------------:|
|  					1 				    |  					N/A 				         |  					S2: Et0/1 				          |
|  					100 				  |  					Clients 				     |  					S1: Et0/3 				          |
|  					200 				  |  					Management 				  |  					S1: VLAN 200  					 				     |
|  					999 				  |  					Parking_Lot 				 |  					S1: Et0/1, Et0/2 				   |
|  					1000 				 |  					Native 				      |  					N/A 				                |
## Решение:
1. [Создание сети и настройка основных параметров устройства](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/README.md#1-%D1%81%D0%BE%D0%B7%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-%D1%81%D0%B5%D1%82%D0%B8-%D0%B8%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-%D0%BE%D1%81%D0%BD%D0%BE%D0%B2%D0%BD%D1%8B%D1%85-%D0%BF%D0%B0%D1%80%D0%B0%D0%BC%D0%B5%D1%82%D1%80%D0%BE%D0%B2-%D1%83%D1%81%D1%82%D1%80%D0%BE%D0%B9%D1%81%D1%82%D0%B2)
	* [Создадим схему адресации](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/README.md#%D1%81%D0%BE%D0%B7%D0%B4%D0%B0%D0%B4%D0%B8%D0%BC-%D1%81%D1%85%D0%B5%D0%BC%D1%83-%D0%B0%D0%B4%D1%80%D0%B5%D1%81%D0%B0%D1%86%D0%B8%D0%B8)
	* [Произведем базовую настройку маршрутизаторов](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/README.md#%D0%BF%D1%80%D0%BE%D0%B8%D0%B7%D0%B2%D0%B5%D0%B4%D0%B5%D0%BC-%D0%B1%D0%B0%D0%B7%D0%BE%D0%B2%D1%83%D1%8E-%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D1%83-%D0%BC%D0%B0%D1%80%D1%88%D1%80%D1%83%D1%82%D0%B8%D0%B7%D0%B0%D1%82%D0%BE%D1%80%D0%BE%D0%B2)
	* [Настроим маршрутизацию между сетями VLAN на маршрутизаторе R1](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/README.md#%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B8%D0%BC-%D0%BC%D0%B0%D1%80%D1%88%D1%80%D1%83%D1%82%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8E-%D0%BC%D0%B5%D0%B6%D0%B4%D1%83-%D1%81%D0%B5%D1%82%D1%8F%D0%BC%D0%B8-vlan-%D0%BD%D0%B0-%D0%BC%D0%B0%D1%80%D1%88%D1%80%D1%83%D1%82%D0%B8%D0%B7%D0%B0%D1%82%D0%BE%D1%80%D0%B5-r1)
	* [Настроим G0/1 на R2, затем G0/0 и статическую маршрутизацию для обоих маршрутизаторов](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/README.md#%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B8%D0%BC-g01-%D0%BD%D0%B0-r2-%D0%B7%D0%B0%D1%82%D0%B5%D0%BC-g00-%D0%B8-%D1%81%D1%82%D0%B0%D1%82%D0%B8%D1%87%D0%B5%D1%81%D0%BA%D1%83%D1%8E-%D0%BC%D0%B0%D1%80%D1%88%D1%80%D1%83%D1%82%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8E-%D0%B4%D0%BB%D1%8F-%D0%BE%D0%B1%D0%BE%D0%B8%D1%85-%D0%BC%D0%B0%D1%80%D1%88%D1%80%D1%83%D1%82%D0%B8%D0%B7%D0%B0%D1%82%D0%BE%D1%80%D0%BE%D0%B2)
	* [Настроим базовые параметры каждого коммутатора](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/README.md#%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B8%D0%BC-%D0%B1%D0%B0%D0%B7%D0%BE%D0%B2%D1%8B%D0%B5-%D0%BF%D0%B0%D1%80%D0%B0%D0%BC%D0%B5%D1%82%D1%80%D1%8B-%D0%BA%D0%B0%D0%B6%D0%B4%D0%BE%D0%B3%D0%BE-%D0%BA%D0%BE%D0%BC%D0%BC%D1%83%D1%82%D0%B0%D1%82%D0%BE%D1%80%D0%B0)
	* [Создаим сети VLAN на коммутаторе S1](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/README.md#%D1%81%D0%BE%D0%B7%D0%B4%D0%B0%D0%B8%D0%BC-%D1%81%D0%B5%D1%82%D0%B8-vlan-%D0%BD%D0%B0-%D0%BA%D0%BE%D0%BC%D0%BC%D1%83%D1%82%D0%B0%D1%82%D0%BE%D1%80%D0%B5-s1-%D1%81%D0%BE%D0%B3%D0%BB%D0%B0%D1%81%D0%BD%D0%BE-%D1%82%D0%B0%D0%B1%D0%BB%D0%B8%D1%86%D1%8B-vlan)
	* [Назначим сети VLAN соответствующим интерфейсам коммутаторов](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/README.md#%D0%BD%D0%B0%D0%B7%D0%BD%D0%B0%D1%87%D0%B8%D0%BC-%D1%81%D0%B5%D1%82%D0%B8-vlan-%D1%81%D0%BE%D0%BE%D1%82%D0%B2%D0%B5%D1%82%D1%81%D1%82%D0%B2%D1%83%D1%8E%D1%89%D0%B8%D0%BC-%D0%B8%D0%BD%D1%82%D0%B5%D1%80%D1%84%D0%B5%D0%B9%D1%81%D0%B0%D0%BC-%D0%BA%D0%BE%D0%BC%D0%BC%D1%83%D1%82%D0%B0%D1%82%D0%BE%D1%80%D0%BE%D0%B2)
	* [Вручную настроим интерфейс Et0/0 на S1 в качестве транка 802.1Q](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/README.md#%D0%B2%D1%80%D1%83%D1%87%D0%BD%D1%83%D1%8E-%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B8%D0%BC-%D0%B8%D0%BD%D1%82%D0%B5%D1%80%D1%84%D0%B5%D0%B9%D1%81-et00-%D0%BD%D0%B0-s1-%D0%B2-%D0%BA%D0%B0%D1%87%D0%B5%D1%81%D1%82%D0%B2%D0%B5-%D1%82%D1%80%D0%B0%D0%BD%D0%BA%D0%B0-8021q)
2. [Настройка и проверка двух серверов DHCPv4 на R1](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/README.md#2-%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-%D0%B8-%D0%BF%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D0%BA%D0%B0-%D0%B4%D0%B2%D1%83%D1%85-%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%BE%D0%B2-dhcpv4-%D0%BD%D0%B0-r1)
	* [Настроим R1 с пулами DHCPv4 для двух поддерживаемых подсетей](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/README.md#%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B8%D0%BC-r1-%D1%81-%D0%BF%D1%83%D0%BB%D0%B0%D0%BC%D0%B8-dhcpv4-%D0%B4%D0%BB%D1%8F-%D0%B4%D0%B2%D1%83%D1%85-%D0%BF%D0%BE%D0%B4%D0%B4%D0%B5%D1%80%D0%B6%D0%B8%D0%B2%D0%B0%D0%B5%D0%BC%D1%8B%D1%85-%D0%BF%D0%BE%D0%B4%D1%81%D0%B5%D1%82%D0%B5%D0%B9)
	* [Проверим конфигурацию сервера DHCPv4](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/README.md#%D0%BF%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D0%B8%D0%BC-%D0%BA%D0%BE%D0%BD%D1%84%D0%B8%D0%B3%D1%83%D1%80%D0%B0%D1%86%D0%B8%D1%8E-%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0-dhcpv4)
	* [Попытаемся получить IP-адрес от DHCP на PC-A](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/README.md#%D0%BF%D0%BE%D0%BF%D1%8B%D1%82%D0%B0%D0%B5%D0%BC%D1%81%D1%8F-%D0%BF%D0%BE%D0%BB%D1%83%D1%87%D0%B8%D1%82%D1%8C-ip-%D0%B0%D0%B4%D1%80%D0%B5%D1%81-%D0%BE%D1%82-dhcp-%D0%BD%D0%B0-pc-a)
3. [Настройка и проверка DHCP Relay на R2](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/README.md#3-%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-%D0%B8-%D0%BF%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D0%BA%D0%B0-dhcp-relay-%D0%BD%D0%B0-r2)
	* [Настроим R2 в качестве агента DHCP Relay для локальной сети на G0/1](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/README.md#%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B8%D0%BC-r2-%D0%B2-%D0%BA%D0%B0%D1%87%D0%B5%D1%81%D1%82%D0%B2%D0%B5-%D0%B0%D0%B3%D0%B5%D0%BD%D1%82%D0%B0-dhcp-relay-%D0%B4%D0%BB%D1%8F-%D0%BB%D0%BE%D0%BA%D0%B0%D0%BB%D1%8C%D0%BD%D0%BE%D0%B9-%D1%81%D0%B5%D1%82%D0%B8-%D0%BD%D0%B0-g01)
	* [Попытаемся получить IP-адрес от DHCP на PC-B](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/README.md#%D0%BF%D0%BE%D0%BF%D1%8B%D1%82%D0%B0%D0%B5%D0%BC%D1%81%D1%8F-%D0%BF%D0%BE%D0%BB%D1%83%D1%87%D0%B8%D1%82%D1%8C-ip-%D0%B0%D0%B4%D1%80%D0%B5%D1%81-%D0%BE%D1%82-dhcp-%D0%BD%D0%B0-pc-b)
	* [Проверим назначенные адреса в DHCP и сообщения](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_5/README.md#%D0%BF%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D0%B8%D0%BC-%D0%BD%D0%B0%D0%B7%D0%BD%D0%B0%D1%87%D0%B5%D0%BD%D0%BD%D1%8B%D0%B5-%D0%B0%D0%B4%D1%80%D0%B5%D1%81%D0%B0-%D0%B2-dhcp-%D0%B8-%D1%81%D0%BE%D0%BE%D0%B1%D1%89%D0%B5%D0%BD%D0%B8%D1%8F)


## 1. Создание сети и настройка основных параметров устройств
### Создадим схему адресации
Подсеть сети 192.168.1.0/24 разобьем в соответствии со следующими требованиями:
##### «Подсеть A» поддерживает 58 хостов: 
```
192.168.1.0/26 
Диапазон: 192.168.1.0 - 192.168.1.63
```
##### «Подсеть B» поддерживает 28 хостов:
```
192.168.1.64/27  
Диапазон: 192.168.1.65 - 192.168.1.94
```
##### «Подсеть C» поддерживает 12 хостов:
```
192.168.1.96/28	
Диапазон: 192.168.1.97 - 192.168.1.110
```
### Произведем базовую настройку маршрутизаторов
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
R1(config)#end                    
R1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
R1#clock set 11:57:00 27 Mar 2021
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
R2(config)#banner motd #Authorized Access Only!#
R2(config)#end
R2#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
R2#clock set 11:59:00 27 Mar 2021
R2#
```
### Настроим маршрутизацию между сетями VLAN на маршрутизаторе R1
```
R1#configure terminal 
R1(config-subif)#interface GigabitEthernet0/1    
R1(config-if)#no shutdown 
R1(config-if)#exit
R1(config)#interface GigabitEthernet0/1.100
R1(config-subif)#description Client
R1(config-subif)#encapsulation dot1Q 100
R1(config-subif)#ip address 192.168.1.1 255.255.255.192
R1(config-subif)#interface GigabitEthernet0/1.200
R1(config-subif)#description Management                
R1(config-subif)#encapsulation dot1Q 200
R1(config-subif)#ip address 192.168.1.65 255.255.255.224
R1(config-subif)#interface GigabitEthernet0/1.1000      
R1(config-subif)#description Native                     
R1(config-subif)#encapsulation dot1Q 1000               
R1(config-subif)#end
R1#show ip interface brief 
Interface                  IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0         unassigned      YES unset  administratively down down    
GigabitEthernet0/1         unassigned      YES unset  up                    up      
GigabitEthernet0/1.100     192.168.1.1     YES manual up                    up      
GigabitEthernet0/1.200     192.168.1.65    YES manual up                    up      
GigabitEthernet0/1.1000    unassigned      YES unset  up                    up      
GigabitEthernet0/2         unassigned      YES unset  administratively down down    
GigabitEthernet0/3         unassigned      YES unset  administratively down down    
R1#
```
### Настроим G0/1 на R2, затем G0/0 и статическую маршрутизацию для обоих маршрутизаторов
##### Маршрутизатор R1:
```
R1#configure terminal 
R1(config)#interface GigabitEthernet0/0 
R1(config-if)#ip address 10.0.0.1 255.255.255.252
R1(config-if)#no shutdown
R1(config-if)#exit
R1(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.2 
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
R2(config)#interface GigabitEthernet0/1 
R2(config-if)#ip address 192.168.1.97 255.255.255.240
R2(config-if)#no shutdown 
R2(config-if)#interface GigabitEthernet0/0           
R2(config-if)#ip address 10.0.0.2 255.255.255.252
R2(config-if)#no shutdown 
R2(config-if)exit
R2(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.1
R2(config)#end
R2#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
R2#
```
##### Убедимся, что статическая маршрутизация работает с помощью пинга до адреса G0/1 R2 от R1:
```
R1#ping 192.168.1.97
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.97, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
R1#
```
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
S1(config)#end
S1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
Compressed configuration from 934 bytes to 683 bytes[OK]
S1#clock set 12:49:00 27 Mar 2021
S1#
```
##### Коммутатор S2:
```
Switch>enable
Switch#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.
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
S2(config)#end
S2#
*Mar 27 09:50:48.559: %SYS-5-CONFIG_I: Configured from console by console
S2#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
Compressed configuration from 934 bytes to 680 bytes[OK]
S2#clock set 12:51:00 27 Mar 2021
S2#
```
### Создаим сети VLAN на коммутаторе S1 согласно таблицы VLAN
```
S1#configure terminal 
S1(config)#vlan 100
S1(config-vlan)#name Clients
S1(config-vlan)#vlan 200    
S1(config-vlan)#name Management
S1(config-vlan)#vlan 999       
S1(config-vlan)#name Parking_Lot
S1(config-vlan)#vlan 1000       
S1(config-vlan)#name Native
S1(config-vlan)#exit
S1(config)#interface range Et0/1,Et0/2
S1(config-if-range)#switchport mode access 
S1(config-if-range)#switchport access vlan 999
S1(config-if-range)#shutdown 
S1(config-if-range)#exit
S1(config)#
```
#### На S2 административно деактивируем все неиспользуемые порты:
```
S2(config)#interface range Et0/2-3
S2(config-if-range)#shutdown 
S2(config-if-range)#exit
S2(config)#
```
#### Настроим и активируем интерфейс управления 
##### На S1:
```
S1(config)#interface vlan 200
S1(config-if)#ip address 192.168.1.66 255.255.255.224
S1(config-if)#no shutdown 
S1(config-if)#exit
S1(config)#ip default-gateway 192.168.1.65
```
##### На S2:
```
S2#configure terminal 
S2(config)#interface vlan 1
S2(config-if)#ip address 192.168.1.98 255.255.255.240
S2(config-if)#no shutdown 
S2(config-if)#exit
S2(config)#ip default-gateway 192.168.1.97
S2(config)#
```
### Назначим сети VLAN соответствующим интерфейсам коммутаторов
```
S1(config)#interface Et0/3
S1(config-if)#switchport mode access 
S1(config-if)#switchport access vlan 100
S1(config-if)#end
S1#show vlan brief 

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et0/0
100  Clients                          active    Et0/3
200  Management                       active    
999  Parking_Lot                      active    Et0/1, Et0/2
1000 Native                           active    
1002 fddi-default                     act/unsup 
1003 token-ring-default               act/unsup 
1004 fddinet-default                  act/unsup 
1005 trnet-default                    act/unsup 
S1#
```
Порт Et0/0 находится во VLAN 1, потому что он не был настроен как магистральный. 

### Вручную настроим интерфейс Et0/0 на S1 в качестве транка 802.1Q
```
S1#configure terminal
S1(config)#interface Et0/0
S1(config-if)#switchport mode trunk 
S1(config-if)#switchport trunk native vlan 1000
S1(config-if)#switchport trunk allowed vlan 100,200,1000
S1(config-if)#end
S1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
Compressed configuration from 1403 bytes to 942 bytes[OK]

S1#show interfaces trunk 

Port        Mode             Encapsulation  Status        Native vlan
Et0/0       on               802.1q         trunking      1000

Port        Vlans allowed on trunk
Et0/0       100,200,1000

Port        Vlans allowed and active in management domain
Et0/0       100,200,1000

Port        Vlans in spanning tree forwarding state and not pruned
Et0/0       100,200,1000
S1#
```
## 2. Настройка и проверка двух серверов DHCPv4 на R1
### Настроим R1 с пулами DHCPv4 для двух поддерживаемых подсетей
```
R1#configure terminal
R1(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.5
R1(config)#ip dhcp pool R1_Client_LAN 
R1(dhcp-config)# network 192.168.1.0 255.255.255.192
R1(dhcp-config)#domain-name ccna-lab.com
R1(dhcp-config)#default-router 192.168.1.1
R1(dhcp-config)#lease 2 12 30
R1(config)#ip dhcp excluded-address 192.168.1.97 192.168.1.101
R1(config)#ip dhcp pool R2_Client_LAN
R1(dhcp-config)#network 192.168.1.96 255.255.255.240
R1(dhcp-config)#default-router 192.168.1.97
R1(dhcp-config)#domain-name ccna-lab.com     
R1(dhcp-config)#lease 2 12 30
R1(dhcp-config)#end
R1#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
R1#
```
### Проверим конфигурацию сервера DHCPv4
```
R1# show ip dhcp pool

Pool R1_Client_LAN :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 62
 Leased addresses               : 0
 Pending event                  : none
 1 subnet is currently in the pool :
 Current index        IP address range                    Leased addresses
 192.168.1.1          192.168.1.1      - 192.168.1.62      0

Pool R2_Client_LAN :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 14
 Leased addresses               : 0
 Pending event                  : none
 1 subnet is currently in the pool :
 Current index        IP address range                    Leased addresses
 192.168.1.97         192.168.1.97     - 192.168.1.110     0

R1#show ip dhcp binding 
Bindings from all pools not associated with VRF:
IP address          Client-ID/              Lease expiration        Type
                    Hardware address/
                    User name
R1#
R1#show ip dhcp binding 
Bindings from all pools not associated with VRF:
IP address          Client-ID/              Lease expiration        Type
                    Hardware address/
                    User name
192.168.1.6         0100.5079.6668.01       Mar 30 2021 02:21 AM    Automatic
R1#show ip dhcp server statistics 
Memory usage         50285
Address pools        2
Database agents      0
Automatic bindings   2
Manual bindings      0
Expired bindings     0
Malformed messages   0
Secure arp entries   0

Message              Received
BOOTREQUEST          0
DHCPDISCOVER         0
DHCPREQUEST          0
DHCPDECLINE          0
DHCPRELEASE          0
DHCPINFORM           0

Message              Sent
BOOTREPLY            0
DHCPOFFER            0
DHCPACK              0
DHCPNAK              0
R1#
```
### Попытаемся получить IP-адрес от DHCP на PC-A
```
PC-A> ip dhcp -r
DORA IP 192.168.1.6/26 GW 192.168.1.1

PC-A> show ip  

NAME        : PC-A[1]
IP/MASK     : 192.168.1.6/26
GATEWAY     : 192.168.1.1
DNS         : 
DHCP SERVER : 192.168.1.1
DHCP LEASE  : 217745, 217800/108900/190575
DOMAIN NAME : ccna-lab.com
MAC         : 00:50:79:66:68:01
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

PC-A> ping 192.168.1.1

84 bytes from 192.168.1.1 icmp_seq=1 ttl=255 time=0.820 ms
84 bytes from 192.168.1.1 icmp_seq=2 ttl=255 time=1.074 ms
84 bytes from 192.168.1.1 icmp_seq=3 ttl=255 time=0.984 ms
84 bytes from 192.168.1.1 icmp_seq=4 ttl=255 time=0.967 ms
84 bytes from 192.168.1.1 icmp_seq=5 ttl=255 time=1.093 ms

PC-A>
```
## 3. Настройка и проверка DHCP Relay на R2
### Настроим R2 в качестве агента DHCP Relay для локальной сети на G0/1
```
R2#configure terminal 
R2(config)#interface GigabitEthernet0/1
R2(config-if)#ip helper-address 10.0.0.1
R2(config-if)#end
R2#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
R2#
```
### Попытаемся получить IP-адрес от DHCP на PC-B
```
PC-B> ip dhcp -r
DORA IP 192.168.1.102/28 GW 192.168.1.97

PC-B> show ip

NAME        : PC-B[1]
IP/MASK     : 192.168.1.102/28
GATEWAY     : 192.168.1.97
DNS         : 
DHCP SERVER : 10.0.0.1
DHCP LEASE  : 217795, 217800/108900/190575
DOMAIN NAME : ccna-lab.com
MAC         : 00:50:79:66:68:02
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

PC-B> ping 192.168.1.97

84 bytes from 192.168.1.97 icmp_seq=1 ttl=255 time=0.948 ms
84 bytes from 192.168.1.97 icmp_seq=2 ttl=255 time=1.219 ms
84 bytes from 192.168.1.97 icmp_seq=3 ttl=255 time=0.963 ms
84 bytes from 192.168.1.97 icmp_seq=4 ttl=255 time=0.913 ms
84 bytes from 192.168.1.97 icmp_seq=5 ttl=255 time=1.013 ms

PC-B>
```
### Проверим назначенные адреса в DHCP и сообщения
```
R1# show ip dhcp binding
Bindings from all pools not associated with VRF:
IP address          Client-ID/              Lease expiration        Type
                    Hardware address/
                    User name
192.168.1.6         0100.5079.6668.01       Mar 30 2021 02:33 AM    Automatic
192.168.1.102       0100.5079.6668.02       Mar 30 2021 02:53 AM    Automatic
R1#show ip dhcp server statistics
Memory usage         50285
Address pools        2
Database agents      0
Automatic bindings   2
Manual bindings      0
Expired bindings     0
Malformed messages   0
Secure arp entries   0

Message              Received
BOOTREQUEST          0
DHCPDISCOVER         2
DHCPREQUEST          2
DHCPDECLINE          0
DHCPRELEASE          0
DHCPINFORM           0

Message              Sent
BOOTREPLY            0
DHCPOFFER            2
DHCPACK              2
DHCPNAK              0
R1#
```









