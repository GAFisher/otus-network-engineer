# Реализация DHCPv4
## Задание: 
1. Создать сеть и настроить основные параметры устройств
2. Настроить и проверить работу двух серверов DHCPv4 на R1
3. Настроить и проверить работу DHCP-ретрансляции на R2
## Топология
![](topology.png)
## Таблица адресации
## Таблица VLAN
## Решение:



## 1. Создание сети и настройка основных параметров устройств
### Создать схему адресации
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
### Настроим G0/1 на R2, затем G0/0/0 и статическую маршрутизацию для обоих маршрутизаторов
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

#### Вручную настроим интерфейс S1 Et0/0 в качестве транка 802.1Q
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















