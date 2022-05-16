# Настройка EIGRP в офисе С.-Петербург
## Задание:
В офисе С.-Петербург настроить named EIGRP.
## Решение:
1. Произведём настройку R18
2. Произведём настройку R17 и R16
3. Произведём настройку R32
4. Проверим таблицы маршрутизации для IPv4 и IPv6 на маршрутизаторах


### Произведём настройку R18
R18 также подключён двумя линками к ISP. Пропишем и настроим распространение статических маршрутов по умолчанию. 
```
St.Petersburg-R18#configure terminal
St.Petersburg-R18(config)#ip route 0.0.0.0 0.0.0.0 95.165.120.5
St.Petersburg-R18(config)#ip route 0.0.0.0 0.0.0.0 95.165.140.5
St.Petersburg-R18(config)#ipv6 route ::/0  2001:20DA:EDA:3::5
St.Petersburg-R18(config)#ipv6 route ::/0  2001:20DA:EDA:7::5
St.Petersburg-R18(config)#router eigrp SPB
St.Petersburg-R18(config-router)#address-family ipv4 unicast autonomous-system 1 
St.Petersburg-R18(config-router-af)#eigrp router-id 10.0.0.18
St.Petersburg-R18(config-router-af)#network 0.0.0.0
St.Petersburg-R18(config-router-af)#topology base
St.Petersburg-R18(config-router-af-topology)#redistribute static
St.Petersburg-R18(config-router-af-topology)#exit
St.Petersburg-R18(config-router-af)#exit
St.Petersburg-R18(config-router)#address-family ipv6 unicast autonomous-system 1 
St.Petersburg-R18(config-router-af)#eigrp router-id 10.0.0.18
St.Petersburg-R18(config-router-af)#topology base
St.Petersburg-R18(config-router-af-topology)#redistribute static
St.Petersburg-R18(config-router-af-topology)#end
St.Petersburg-R18#wr
```
### Произведём настройку R17 и R16
R16-17 анонсируют только суммарные префиксы.
#### R17
```
St.Petersburg-R17#configure terminal 
St.Petersburg-R17(config)#router eigrp SPB
St.Petersburg-R17(config-router)#address-family ipv4 unicast autonomous-system 1       
St.Petersburg-R17(config-router-af)#eigrp router-id 10.0.0.17
St.Petersburg-R17(config-router-af)#network 0.0.0.0
St.Petersburg-R17(config-router-af)#af-interface  Ethernet0/1
St.Petersburg-R17(config-router-af-interface)#summary-address 10.2.0.0/16
St.Petersburg-R17(config-router-af-interface)#exit
St.Petersburg-R17(config-router-af)#exit
St.Petersburg-R17(config-router-af)#af-interface Vl20 
St.Petersburg-R17(config-router-af-interface)#passive-interface 
St.Petersburg-R17(config-router-af-interface)#exit              
St.Petersburg-R17(config-router-af)#af-interface Vl21 
St.Petersburg-R17(config-router-af-interface)#passive-interface 
St.Petersburg-R17(config-router-af-interface)#exit
St.Petersburg-R17(config-router-af)#exit
St.Petersburg-R17(config-router)#address-family ipv6 unicast autonomous-system 1 
St.Petersburg-R17(config-router-af)#eigrp router-id 10.0.0.17
St.Petersburg-R17(config-router-af)#af-interface  Ethernet0/1
St.Petersburg-R17(config-router-af-interface)#summary-address 2A00:FACE:C002::/48      
St.Petersburg-R17(config-router-af-interface)#end
St.Petersburg-R17#wr
```
#### R16
```
St.Petersburg-R16#configure terminal 
St.Petersburg-R16(config)#router eigrp SPB
St.Petersburg-R16(config-router)#address-family ipv4 unicast autonomous-system 1       
St.Petersburg-R16(config-router-af)#eigrp router-id 10.0.0.16
St.Petersburg-R16(config-router-af)#network 0.0.0.0
St.Petersburg-R16(config-router-af)#af-interface  Ethernet0/1
St.Petersburg-R16(config-router-af-interface)#summary-address 10.2.0.0/16
St.Petersburg-R16(config-router-af-interface)#exit
St.Petersburg-R16(config-router-af)#exit
St.Petersburg-R16(config-router-af)#af-interface Vl20 
St.Petersburg-R16(config-router-af-interface)#passive-interface 
St.Petersburg-R16(config-router-af-interface)#exit              
St.Petersburg-R16(config-router-af)#af-interface Vl21 
St.Petersburg-R16(config-router-af-interface)#passive-interface 
St.Petersburg-R16(config-router-af-interface)#exit
St.Petersburg-R16(config-router-af)#exit
St.Petersburg-R16(config-router)#address-family ipv6 unicast autonomous-system 1 
St.Petersburg-R16(config-router-af)#eigrp router-id 10.0.0.16
St.Petersburg-R16(config-router-af)#af-interface  Ethernet0/1
St.Petersburg-R16(config-router-af-interface)#summary-address 2A00:FACE:C002::/48      
St.Petersburg-R16(config-router-af-interface)#end
St.Petersburg-R16#wr
```
### Произведём настройку R32 
```
St.Petersburg-R32#configure terminal 
St.Petersburg-R32(config)#router eigrp SPB
St.Petersburg-R32(config-router)#address-family ipv4 unicast autonomous-system 1
St.Petersburg-R32(config-router-af)#eigrp router-id 10.0.0.32
St.Petersburg-R32(config-router-af)#network 0.0.0.0
St.Petersburg-R32(config-router-af)#exit
St.Petersburg-R32(config-router)#address-family ipv6 unicast autonomous-system 1 
St.Petersburg-R32(config-router-af)#eigrp router-id 10.0.0.32
St.Petersburg-R32(config-router-af)#end
St.Petersburg-R32#write 
```
R32 должен получать только маршрут по умолчанию. 
Настроим фильтрацию маршрутов с помощью списка префиксов. 
```
St.Petersburg-R16#configure terminal
St.Petersburg-R32(config)#ip prefix-list acl-EIGRP-IN seq 10 permit 0.0.0.0/0
St.Petersburg-R32(config)#ipv6 prefix-list acl-EIGRP-IN seq 10 permit ::/0
St.Petersburg-R32(config)#router eigrp SPB
St.Petersburg-R32(config-router)#address-family ipv4 unicast autonomous-system 1
St.Petersburg-R32(config-router-af)#topology base
St.Petersburg-R32(config-router-af-topology)#distribute-list prefix acl-EIGRP-IN in Ethernet0/0 
St.Petersburg-R32(config-router-af-topology)#exit
St.Petersburg-R32(config-router-af)#exit
St.Petersburg-R32(config-router)#address-family ipv6 unicast autonomous-system 1 
St.Petersburg-R32(config-router-af-topology)#distribute-list prefix-list acl-EIGRP-IN in Ethernet0/0 
St.Petersburg-R32(config-router-af-topology)#end
St.Petersburg-R32#write 
```
Таблица маршрутизации R32 до настройки distribute-list:
```
St.Petersburg-R32#show ip route eigrp | begin Gateway
Gateway of last resort is 10.2.10.2 to network 0.0.0.0

D*EX  0.0.0.0/0 [170/2048000] via 10.2.10.2, 00:01:51, Ethernet0/0
      10.0.0.0/8 is variably subnetted, 9 subnets, 3 masks
D        10.2.10.4/30 [90/1536000] via 10.2.10.2, 00:01:51, Ethernet0/0
D        10.2.10.8/30 [90/1541120] via 10.2.10.2, 00:01:51, Ethernet0/0
D        10.2.10.12/30 [90/1029120] via 10.2.10.2, 00:01:51, Ethernet0/0
D        10.2.20.0/24 [90/1029120] via 10.2.10.2, 00:01:51, Ethernet0/0
D        10.2.21.0/24 [90/1029120] via 10.2.10.2, 00:01:51, Ethernet0/0
      95.0.0.0/30 is subnetted, 2 subnets
D        95.165.120.4 [90/2048000] via 10.2.10.2, 00:01:51, Ethernet0/0
D        95.165.140.4 [90/2048000] via 10.2.10.2, 00:01:51, Ethernet0/0
St.Petersburg-R32#
St.Petersburg-R32#show ipv6 route | begin Application
       lr - LISP site-registrations, ld - LISP dyn-eid, a - Application
D   2001:20DA:EDA:3::/64 [90/2048000]
     via FE80::16, Ethernet0/0
D   2001:20DA:EDA:7::/64 [90/2048000]
     via FE80::16, Ethernet0/0
C   2A00:FACE:C002:10::/64 [0/0]
     via Ethernet0/0, directly connected
L   2A00:FACE:C002:10::1/128 [0/0]
     via Ethernet0/0, receive
D   2A00:FACE:C002:20::/64 [90/1029120]
     via FE80::16, Ethernet0/0
D   2A00:FACE:C002:21::/64 [90/1029120]
     via FE80::16, Ethernet0/0
D   2A00:FACE:C002:40::/64 [90/1536000]
     via FE80::16, Ethernet0/0
D   2A00:FACE:C002:80::/64 [90/1541120]
     via FE80::16, Ethernet0/0
C   2A00:FACE:C002:99::/64 [0/0]
     via Loopback1, directly connected
L   2A00:FACE:C002:99::32/128 [0/0]
     via Loopback1, receive
D   2A00:FACE:C002:120::/64 [90/1029120]
     via FE80::16, Ethernet0/0
L   FF00::/8 [0/0]
     via Null0, receive
St.Petersburg-R32# 
```
После настройки distribute-list:
```
St.Petersburg-R32#show ip route eigrp | begin Gateway      
Gateway of last resort is 10.2.10.2 to network 0.0.0.0

D*EX  0.0.0.0/0 [170/2048000] via 10.2.10.2, 00:15:42, Ethernet0/0
St.Petersburg-R32#
St.Petersburg-R32#show ipv6 route eigrp | begin Application
       lr - LISP site-registrations, ld - LISP dyn-eid, a - Application

St.Petersburg-R32#
```
### Проверим таблицы маршрутизации для IPv4 и IPv6 на маршрутизаторах 
и убедимся, что настройки произведены корректно, согласно заданию:
#### R16
```
St.Petersburg-R16#show ip route eigrp | begin Gateway
Gateway of last resort is 10.2.10.6 to network 0.0.0.0

D*EX  0.0.0.0/0 [170/1536000] via 10.2.10.6, 00:22:08, Ethernet0/1
      10.0.0.0/8 is variably subnetted, 14 subnets, 4 masks
D        10.2.0.0/16 is a summary, 00:21:53, Null0
D        10.2.10.8/30 [90/1029120] via 10.2.99.17, 00:22:08, Vlan99
                      [90/1029120] via 10.2.10.13, 00:22:08, Vlan25
      95.0.0.0/30 is subnetted, 2 subnets
D        95.165.120.4 [90/1536000] via 10.2.10.6, 00:22:08, Ethernet0/1
D        95.165.140.4 [90/1536000] via 10.2.10.6, 00:22:08, Ethernet0/1
St.Petersburg-R16#
St.Petersburg-R16#show ipv6 route eigrp | begin Application
       a - Application
D   2001:20DA:EDA:3::/64 [90/1536000]
     via FE80::18, Ethernet0/1
D   2001:20DA:EDA:7::/64 [90/1536000]
     via FE80::18, Ethernet0/1
D   2A00:FACE:C002::/48 [5/10240]
     via Null0, directly connected
D   2A00:FACE:C002:80::/64 [90/1029120]
     via FE80::17, Vlan99
     via FE80::17, Vlan25
St.Petersburg-R16#
```
#### R17
```
St.Petersburg-R17#show ip route eigrp | begin Gateway
Gateway of last resort is 10.2.10.10 to network 0.0.0.0

D*EX  0.0.0.0/0 [170/1536000] via 10.2.10.10, 00:23:20, Ethernet0/1
      10.0.0.0/8 is variably subnetted, 13 subnets, 4 masks
D        10.2.0.0/16 is a summary, 00:23:04, Null0
D        10.2.10.0/30 [90/1029120] via 10.2.99.16, 00:23:20, Vlan99
                      [90/1029120] via 10.2.10.14, 00:23:20, Vlan25
D        10.2.10.4/30 [90/1029120] via 10.2.99.16, 00:23:20, Vlan99
                      [90/1029120] via 10.2.10.14, 00:23:20, Vlan25
      95.0.0.0/30 is subnetted, 2 subnets
D        95.165.120.4 [90/1536000] via 10.2.10.10, 00:23:20, Ethernet0/1
D        95.165.140.4 [90/1536000] via 10.2.10.10, 00:23:20, Ethernet0/1
St.Petersburg-R17#
St.Petersburg-R17#show ipv6 route eigrp | begin Application
       a - Application
D   2001:20DA:EDA:3::/64 [90/1536000]
     via FE80::18, Ethernet0/1
D   2001:20DA:EDA:7::/64 [90/1536000]
     via FE80::18, Ethernet0/1
D   2A00:FACE:C002::/48 [5/10240]
     via Null0, directly connected
D   2A00:FACE:C002:10::/64 [90/1029120]
     via FE80::16, Vlan99
     via FE80::16, Vlan25
D   2A00:FACE:C002:40::/64 [90/1029120]
     via FE80::16, Vlan99
     via FE80::16, Vlan25
St.Petersburg-R17#
```
#### R18
```
St.Petersburg-R18#show ip route eigrp | begin Gateway
Gateway of last resort is 95.165.140.5 to network 0.0.0.0

      10.0.0.0/8 is variably subnetted, 7 subnets, 4 masks
D        10.2.0.0/16 [90/1029120] via 10.2.10.9, 00:23:42, Ethernet0/1
                     [90/1029120] via 10.2.10.5, 00:23:42, Ethernet0/0
St.Petersburg-R18#                                         
St.Petersburg-R18#show ipv6 route eigrp | begin Application
       lr - LISP site-registrations, ld - LISP dyn-eid, a - Application
D   2A00:FACE:C002::/48 [90/1029120]
     via FE80::17, Ethernet0/1
     via FE80::16, Ethernet0/0
St.Petersburg-R18#
```

