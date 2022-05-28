# Настройка iBGP
## Задание:
1. Настроить iBGP в офисе Москва
2. Настроить iBGP в сети провайдера Триада
3. Организовать полную IP связанность всех сетей 

## Решение:
1. Настроим iBGP в офисе Москва между маршрутизаторами R14 и R15;
2. Настроим офис Москва так, чтобы приоритетным провайдером стал Ламас;
3. Настроим iBGP в провайдере Триада;
4. Настром офис С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно;
5. Проверим, что все сети в лабораторной работе имеют IP связность.


### 1. Настроим iBGP в офисе Москва между маршрутизаторами R14 и R15
Поднимем Loopback-интерфейсы на маршрутизаторах R14 и R15, распространим их адреса через протокол внутренней маршрутизации OSPF для связности:
#### R14
```
Moscow-R14#configure terminal
Moscow-R14(config)#interface Loopback0
Moscow-R14(config-if)#ip address 14.14.14.14 255.255.255.255
Moscow-R14(config-if)#ip ospf 1 area 0
Moscow-R14(config-if)#ipv6 address FC00::14/128
Moscow-R14(config-if)#ipv6 ospf 1 area 0
Moscow-R14(config-if)#end
```
#### R15
```
Moscow-R15#configure terminal
Moscow-R15(config)#interface Loopback0
Moscow-R15(config-if)#ip address 15.15.15.15 255.255.255.255
Moscow-R15(config-if)#ip ospf 1 area 0
Moscow-R15(config-if)#ipv6 address FC00::15/128
Moscow-R15(config-if)#ipv6 ospf 1 area 0
Moscow-R15(config-if)#end
```
Проверим связность:
#### R14 -> R15
```
Moscow-R14#ping 15.15.15.15 source Loopback0
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 15.15.15.15, timeout is 2 seconds:
Packet sent with a source address of 14.14.14.14 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
Moscow-R14#
```
#### R15 -> R14
```
Moscow-R15#ping 14.14.14.14 source Loopback0
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 14.14.14.14, timeout is 2 seconds:
Packet sent with a source address of 15.15.15.15 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
Moscow-R15#
```

Настроим соседство между маршрутизаторами и проанонсируем стыковочные подсети:
#### R14
```
Moscow-R14(config)#router bgp 1001
Moscow-R14(config-router)#neighbor 15.15.15.15 remote-as 1001
Moscow-R14(config-router)#neighbor 15.15.15.15 update-source Loopback1
Moscow-R14(config-router)#neighbor FC00::15 remote-as 1001
Moscow-R14(config-router)#neighbor FC00::15 update-source Loopback1
Moscow-R14(config-router)# address-family ipv4
Moscow-R14(config-router-af)#network 84.52.118.224 mask 255.255.255.252
Moscow-R14(config-router)# address-family ipv6
Moscow-R14(config-router-af)#network 2606:4700:D0:C009::/64 
Moscow-R14(config-router-af)#end
Moscow-R14#wr
```
#### R15
```
Moscow-R15(config)#router bgp 1001
Moscow-R15(config-router)#neighbor 14.14.14.14 remote-as 1001
Moscow-R15(config-router)#neighbor 14.14.14.14 update-source Loopback1
Moscow-R15(config-router)#neighbor FC00::15 remote-as 1001
Moscow-R15(config-router)#neighbor FC00::15 update-source Loopback1
Moscow-R15(config-router)# address-family ipv4
Moscow-R15(config-router-af)#network 78.25.80.88 mask 255.255.255.252
Moscow-R15(config-router)# address-family ipv6
Moscow-R15(config-router-af)#network 1A00:4700:D0:C005::/64 
Moscow-R15(config-router-af)#exit
Moscow-R15(config-router)#exit
```
### 2. Настроим офис Москва так, чтобы приоритетным провайдером стал Ламас
Используем атрибут Local Preference. На маршрутизаторе R15 опишем route-map и привяжем к соседу R21:
```
Moscow-R15(config)#route-map LP permit 10
Moscow-R15(config-route-map)#set local-preference 150
Moscow-R15(config-route-map)#exit
Moscow-R15(config)#router bgp 1001
Moscow-R15(config-router)#address-family ipv4
Moscow-R15(config-router-af)#neighbor 78.25.80.89 route-map LP in
Moscow-R15(config-router-af)#exit
Moscow-R15(config-router)#address-family ipv6 
Moscow-R15(config-router-af)#neighbor FC00::14 route-map LP in
Moscow-R15(config-router-af)#end
Moscow-R15#wr
```
### 3. Настроим iBGP в провайдере Триада
Произведён настройку Loopback-интерфейсов. С помощью протокола внутренней маршрутизации IS-IS все маршрутизаторы должны узнать обо всех адресах Loopback-интерфейсов. 
#### R23
```
Triad-R23#configure terminal
Triad-R23(config)#interface Loopback0
Triad-R23(config-if)#ip address 23.23.23.23 255.255.255.255
Triad-R23(config-if)#ip router isis
Triad-R23(config-if)#ipv6 address FC00::23/128
Triad-R23(config-if)#ipv6 router isis
Triad-R23(config-if)#exit
```
#### R24
```
Triad-R24#configure terminal
Triad-R24(config)#interface Loopback0
Triad-R24(config-if)#ip address 24.24.24.24 255.255.255.255
Triad-R24(config-if)#ip router isis
Triad-R24(config-if)#ipv6 address FC00::24/128
Triad-R24(config-if)#ipv6 router isis 
Triad-R24(config-if)#exit
```
#### R25
```
Triad-R25#configure terminal
Triad-R25(config)#interface Loopback0
Triad-R25(config-if)#ip address 25.25.25.25 255.255.255.255
Triad-R25(config-if)#ip router isis
Triad-R25(config-if)#ipv6 address FC00::25/128
Triad-R25(config-if)#ipv6 router isis
Triad-R25(config-if)#exit
```
#### R26
```
Triad-R26#configure terminal
Triad-R26(config)#interface Loopback0
Triad-R26(config-if)#ip address 26.26.26.26 255.255.255.255
Triad-R26(config-if)#ip router isis
Triad-R26(config-if)#ipv6 address FC00::26/128
Triad-R26(config-if)#ipv6 router isis
Triad-R26(config-if)#exit
```
Произведём настройку соседей. В качестве Route Reflectror настроим маршрутизатор R23. 
Для IPv4 объединим всех соседей в peer-group. 
#### R23
```
Triad-R23(config)#router bgp 520
Triad-R23(config-router)#neighbor AS520 peer-group 
Triad-R23(config-router)#neighbor AS520 remote-as 520
Triad-R23(config-router)#neighbor AS520 update-source Loopback0
Triad-R23(config-router)#neighbor AS520 route-reflector-client 
Triad-R23(config-router)#neighbor AS520 next-hop-self
Triad-R23(config-router)#neighbor 24.24.24.24 peer-group AS520
Triad-R23(config-router)#neighbor 25.25.25.25 peer-group AS520
Triad-R23(config-router)#neighbor 26.26.26.26 peer-group AS520
Triad-R23(config-router)#neighbor FC00::24 remote-as 520
Triad-R23(config-router)#neighbor FC00::24 update-source Loopback0
Triad-R23(config-router)#neighbor FC00::25 remote-as 520
Triad-R23(config-router)#neighbor FC00::25 update-source Loopback0
Triad-R23(config-router)#neighbor FC00::26 remote-as 520
Triad-R23(config-router)#neighbor FC00::26 update-source Loopback0
Triad-R23(config-router)# address-family ipv6
Triad-R23(config-router-af)#neighbor FC00::24 activate
Triad-R23(config-router-af)#neighbor FC00::24 next-hop-self
Triad-R23(config-router-af)#neighbor FC00::24 route-reflector-client
Triad-R23(config-router-af)#neighbor FC00::25 activate
Triad-R23(config-router-af)#neighbor FC00::25 route-reflector-client
Triad-R23(config-router-af)#neighbor FC00::25 next-hop-self
Triad-R23(config-router-af)#neighbor FC00::26 activate
Triad-R23(config-router-af)#neighbor FC00::26 route-reflector-client
Triad-R23(config-router-af)#neighbor FC00::26 next-hop-self
Triad-R23(config-router-af)#exit
```
На маршрутизаторах R24-26 пропишем соседом только R23 (настройка идентична для всех маршрутизаторов Триады):
```
Triad-R25(config)#router bgp 520
Triad-R25(config-router)#neighbor 23.23.23.23 remote-as 520
Triad-R25(config-router)#neighbor 23.23.23.23 update-source Loopback0
Triad-R25(config-router)#neighbor 23.23.23.23 next-hop-self 
Triad-R25(config-router)#neighbor FC00::23 remote-as 520
Triad-R25(config-router)#neighbor FC00::23 update-source Loopback0
Triad-R25(config-router)#address-family ipv6 
Triad-R25(config-router-af)#neighbor FC00::23 next-hop-self 
Triad-R25(config-router-af)#exit
```
Проанонсируем стыковочные подсети до офисов Лабытнанги и Чокурдах и провайдера Киртон.
#### Киртон
```
Triad-R23(config-router)#address-family ipv4
Triad-R23(config-router-af)#network 95.165.110.0 mask 255.255.255.252
Triad-R23(config-router-af)#exit
Triad-R23(config-router)#address-family ipv6
Triad-R23(config-router-af)#network 2001:20DA:EDA:1::/64
Triad-R23(config-router-af)#end
Triad-R23#wr
```
#### Лабытнанги
```
Triad-R25(config-router)#address-family ipv4
Triad-R25(config-router-af)#network 95.165.130.0 mask 255.255.255.252
Triad-R25(config-router-af)#exit
Triad-R25(config-router)#address-family ipv6
Triad-R25(config-router-af)#network 2001:20DA:EDA:4::/64
Triad-R25(config-router-af)#exit
```
#### Чокурдах
```
Triad-R25(config-router)#address-family ipv4
Triad-R25(config-router-af)#network 95.165.130.4 mask 255.255.255.252
Triad-R25(config-router-af)#exit
Triad-R25(config-router)#address-family ipv6
Triad-R25(config-router-af)#network 2001:20DA:EDA:5::/64
Triad-R25(config-router-af)#end
Triad-R25#wr
```
```
Triad-R25(config)#router bgp 520
Triad-R26(config-router)#address-family ipv4
Triad-R26(config-router-af)#network 95.165.140.0 mask 255.255.255.252
Triad-R26(config-router-af)#exit
Triad-R26(config-router)#address-family ipv6
Triad-R26(config-router-af)#network 2001:20DA:EDA:6::/64
Triad-R26(config-router-af)#end
Triad-R26#wr
```

### 4. Настром офис С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно
Таблицы маршрутизации и таблицы bgp до изменений:

<details>
  <summary>R18</summary>

    St.Petersburg-R18#show bgp ipv4 unicast | begin Network
         Network          Next Hop            Metric LocPrf Weight Path
     *   78.25.80.88/30   95.165.140.5                           0 520 301 i
     *>                   95.165.120.5                           0 520 301 i
     *   84.52.118.224/30 95.165.140.5                           0 520 301 101 i
     *>                   95.165.120.5                           0 520 301 101 i
     *   95.165.110.0/30  95.165.120.5                           0 520 i
     *>                   95.165.140.5                           0 520 i
     *   95.165.120.0/30  95.165.140.5                           0 520 i
     *>                   95.165.120.5             0             0 520 i
     *   95.165.120.4/30  95.165.140.5                           0 520 i
     *                    95.165.120.5             0             0 520 i
     *>                   0.0.0.0                  0         32768 i
     *   95.165.130.0/30  95.165.120.5                           0 520 i
     *>                   95.165.140.5                           0 520 i
     *   95.165.130.4/30  95.165.120.5                           0 520 i
     *>                   95.165.140.5                           0 520 i
     *   95.165.140.0/30  95.165.120.5                           0 520 i
     *>                   95.165.140.5             0             0 520 i
     *   95.165.140.4/30  95.165.120.5                           0 520 i
     *                    95.165.140.5             0             0 520 i
     *>                   0.0.0.0                  0         32768 i
     *   128.0.128.0/30   95.165.140.5                           0 520 301 i
     *>                   95.165.120.5                           0 520 301 i                                       
    St.Petersburg-R18#show ip route bgp | begin Gateway
    Gateway of last resort is 95.165.140.5 to network 0.0.0.0

          78.0.0.0/30 is subnetted, 1 subnets
    B        78.25.80.88 [20/0] via 95.165.120.5, 00:06:28
          84.0.0.0/30 is subnetted, 1 subnets
    B        84.52.118.224 [20/0] via 95.165.120.5, 00:06:28
          95.0.0.0/8 is variably subnetted, 9 subnets, 2 masks
    B        95.165.110.0/30 [20/0] via 95.165.140.5, 00:07:32
    B        95.165.120.0/30 [20/0] via 95.165.120.5, 00:06:28
    B        95.165.130.0/30 [20/0] via 95.165.140.5, 00:07:32
    B        95.165.130.4/30 [20/0] via 95.165.140.5, 00:07:32
    B        95.165.140.0/30 [20/0] via 95.165.140.5, 00:07:32
          128.0.0.0/30 is subnetted, 1 subnets
    B        128.0.128.0 [20/0] via 95.165.120.5, 00:06:28
    St.Petersburg-R18#show bgp ipv6 unicast | begin Network  
         Network          Next Hop            Metric LocPrf Weight Path
     *>  64:FF9B:5276:1EB4::/64
                           2001:20DA:EDA:3::5
                                                                  0 520 301 i
     *>  1A00:4700:D0:C005::/64
                           2001:20DA:EDA:3::5
                                                                  0 520 301 i
     *>  2001:20DA:EDA:1::/64
                           2001:20DA:EDA:3::5
                                                                  0 520 i
     *>  2001:20DA:EDA:2::/64
                           2001:20DA:EDA:3::5
                                                    0             0 520 i
     *   2001:20DA:EDA:3::/64
                           2001:20DA:EDA:3::5
                                                    0             0 520 i
     *>                   ::                       0         32768 i
     *>  2001:20DA:EDA:4::/64
                           2001:20DA:EDA:3::5
                                                                  0 520 i
     *>  2001:20DA:EDA:5::/64
                           2001:20DA:EDA:3::5
                                                                  0 520 i
         Network          Next Hop            Metric LocPrf Weight Path
     *>  2001:20DA:EDA:6::/64
                           2001:20DA:EDA:3::5
                                                                  0 520 i
     *   2001:20DA:EDA:7::/64
                           2001:20DA:EDA:3::5
                                                                  0 520 i
     *>                   ::                       0         32768 i
     *>  2606:4700:D0:C009::/64
                           2001:20DA:EDA:3::5
                                                                  0 520 301 101 i
    St.Petersburg-R18#show ipv6 route bgp | begin Application
           lr - LISP site-registrations, ld - LISP dyn-eid, a - Application
    B   64:FF9B:5276:1EB4::/64 [20/0]
         via FE80::24, Ethernet0/2
    B   1A00:4700:D0:C005::/64 [20/0]
         via FE80::24, Ethernet0/2
    B   2001:20DA:EDA:1::/64 [20/0]
         via FE80::24, Ethernet0/2
    B   2001:20DA:EDA:2::/64 [20/0]
         via FE80::24, Ethernet0/2
    B   2001:20DA:EDA:4::/64 [20/0]
         via FE80::24, Ethernet0/2
    B   2001:20DA:EDA:5::/64 [20/0]
         via FE80::24, Ethernet0/2
    B   2001:20DA:EDA:6::/64 [20/0]
         via FE80::24, Ethernet0/2
    B   2606:4700:D0:C009::/64 [20/0]
         via FE80::24, Ethernet0/2
    St.Petersburg-R18#
  
</details>

Для балансировки нагрузки между линками применим команду *maximum-paths*.
```
St.Petersburg-R18#configure terminal 
St.Petersburg-R18(config)# router bgp 2042
St.Petersburg-R18(config-router)#address-family ipv4
St.Petersburg-R18(config-router-af)#maximum-paths 2
St.Petersburg-R18(config-router-af)#exit          
St.Petersburg-R18(config-router)#address-family ipv6
St.Petersburg-R18(config-router-af)#end
St.Petersburg-R18#wr
```
Таблицы маршрутизации и таблицы bgp после изменений:

<details>
  <summary>R18</summary>
  
    St.Petersburg-R18#show bgp ipv4 unicast | begin Network
         Network          Next Hop            Metric LocPrf Weight Path
     *m  78.25.80.88/30   95.165.140.5                           0 520 301 i
     *>                   95.165.120.5                           0 520 301 i
     *m  84.52.118.224/30 95.165.140.5                           0 520 301 101 i
     *>                   95.165.120.5                           0 520 301 101 i
     *m  95.165.110.0/30  95.165.140.5                           0 520 i
     *>                   95.165.120.5                           0 520 i
     *m  95.165.120.0/30  95.165.140.5                           0 520 i
     *>                   95.165.120.5             0             0 520 i
     *   95.165.120.4/30  95.165.140.5                           0 520 i
     *                    95.165.120.5             0             0 520 i
     *>                   0.0.0.0                  0         32768 i
     *m  95.165.130.0/30  95.165.140.5                           0 520 i
     *>                   95.165.120.5                           0 520 i
     *m  95.165.130.4/30  95.165.140.5                           0 520 i
     *>                   95.165.120.5                           0 520 i
     *m  95.165.140.0/30  95.165.140.5             0             0 520 i
     *>                   95.165.120.5                           0 520 i
     *   95.165.140.4/30  95.165.140.5             0             0 520 i
     *                    95.165.120.5                           0 520 i
     *>                   0.0.0.0                  0         32768 i
     *m  128.0.128.0/30   95.165.140.5                           0 520 301 i
     *>                   95.165.120.5                           0 520 301 i
    St.Petersburg-R18#show ip route bgp | begin Gateway
    Gateway of last resort is 95.165.140.5 to network 0.0.0.0

          78.0.0.0/30 is subnetted, 1 subnets
    B        78.25.80.88 [20/0] via 95.165.140.5, 00:13:30
                         [20/0] via 95.165.120.5, 00:13:30
          84.0.0.0/30 is subnetted, 1 subnets
    B        84.52.118.224 [20/0] via 95.165.140.5, 00:13:30
                           [20/0] via 95.165.120.5, 00:13:30
          95.0.0.0/8 is variably subnetted, 9 subnets, 2 masks
    B        95.165.110.0/30 [20/0] via 95.165.140.5, 00:13:30
                             [20/0] via 95.165.120.5, 00:13:30
    B        95.165.120.0/30 [20/0] via 95.165.140.5, 00:13:30
                             [20/0] via 95.165.120.5, 00:13:30
    B        95.165.130.0/30 [20/0] via 95.165.140.5, 00:13:30
                             [20/0] via 95.165.120.5, 00:13:30
    B        95.165.130.4/30 [20/0] via 95.165.140.5, 00:13:30
                             [20/0] via 95.165.120.5, 00:13:30
    B        95.165.140.0/30 [20/0] via 95.165.140.5, 00:13:30
                             [20/0] via 95.165.120.5, 00:13:30
          128.0.0.0/30 is subnetted, 1 subnets
    B        128.0.128.0 [20/0] via 95.165.140.5, 00:13:30
                         [20/0] via 95.165.120.5, 00:13:30
    St.Petersburg-R18#show bgp ipv6 unicast | begin Network
         Network          Next Hop            Metric LocPrf Weight Path
     *>  64:FF9B:5276:1EB4::/64
                           2001:20DA:EDA:3::5
                                                                  0 520 301 i
     *>  1A00:4700:D0:C005::/64
                           2001:20DA:EDA:3::5
                                                                  0 520 301 i
     *>  2001:20DA:EDA:1::/64
                           2001:20DA:EDA:3::5
                                                                  0 520 i
     *>  2001:20DA:EDA:2::/64
                           2001:20DA:EDA:3::5
                                                    0             0 520 i
     *   2001:20DA:EDA:3::/64
                           2001:20DA:EDA:3::5
                                                    0             0 520 i
     *>                   ::                       0         32768 i
     *>  2001:20DA:EDA:4::/64
                           2001:20DA:EDA:3::5
                                                                  0 520 i
     *>  2001:20DA:EDA:5::/64
                           2001:20DA:EDA:3::5
                                                                  0 520 i
         Network          Next Hop            Metric LocPrf Weight Path
     *>  2001:20DA:EDA:6::/64
                           2001:20DA:EDA:3::5
                                                                  0 520 i
     *   2001:20DA:EDA:7::/64
                           2001:20DA:EDA:3::5
                                                                  0 520 i
     *>                   ::                       0         32768 i
     *>  2606:4700:D0:C009::/64
                           2001:20DA:EDA:3::5
                                                                  0 520 301 101 i
    St.Petersburg-R18#show ipv6 route bgp | begin Application
           lr - LISP site-registrations, ld - LISP dyn-eid, a - Application
    B   64:FF9B:5276:1EB4::/64 [20/0]
         via FE80::24, Ethernet0/2
    B   1A00:4700:D0:C005::/64 [20/0]
         via FE80::24, Ethernet0/2
    B   2001:20DA:EDA:1::/64 [20/0]
         via FE80::24, Ethernet0/2
    B   2001:20DA:EDA:2::/64 [20/0]
         via FE80::24, Ethernet0/2
    B   2001:20DA:EDA:4::/64 [20/0]
         via FE80::24, Ethernet0/2
    B   2001:20DA:EDA:5::/64 [20/0]
         via FE80::24, Ethernet0/2
    B   2001:20DA:EDA:6::/64 [20/0]
         via FE80::24, Ethernet0/2
    B   2606:4700:D0:C009::/64 [20/0]
         via FE80::24, Ethernet0/2
    St.Petersburg-R18#
  
</details>

### 5. Проверим, что все сети в лабораторной работе имеют IP связность

R14 -> R15
```
Moscow-R14#ping 78.25.80.90 source 84.52.118.226
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 78.25.80.90, timeout is 2 seconds:
Packet sent with a source address of 84.52.118.226 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
Moscow-R14#
```
R14 -> R18
R14 -> R27
```
Moscow-R14#ping 95.165.130.2 so
Moscow-R14#ping 95.165.130.2 source 84.52.118.226
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 95.165.130.2, timeout is 2 seconds:
Packet sent with a source address of 84.52.118.226 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/3 ms
Moscow-R14
```
R14 -> R28

R15 -> R14
```
Moscow-R15#ping 84.52.118.226 source 78.25.80.90
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 84.52.118.226, timeout is 2 seconds:
Packet sent with a source address of 78.25.80.90 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
Moscow-R15#
```


R27 -> R14
```
Labytnangi-R27#ping 84.52.118.226 so
Labytnangi-R27#ping 84.52.118.226 source 95.165.130.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 84.52.118.226, timeout is 2 seconds:
Packet sent with a source address of 95.165.130.2 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
Labytnangi-R27#
```

R18 -> R27
St.Petersburg-R18#ping 95.165.130.2 source 95.165.120.6
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 95.165.130.2, timeout is 2 seconds:
Packet sent with a source address of 95.165.120.6 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/1 ms
St.Petersburg-R18#ping 95.165.130.2 source 95.165.140.6
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 95.165.130.2, timeout is 2 seconds:
Packet sent with a source address of 95.165.140.6 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/4 ms
St.Petersburg-R18#


R28 -> R27
```
Chokurdah-R28#ping 95.165.130.2 source 95.165.140.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 95.165.130.2, timeout is 2 seconds:
Packet sent with a source address of 95.165.140.2 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/4 ms
Chokurdah-R28#ping 95.165.130.2 source 95.165.130.6
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 95.165.130.2, timeout is 2 seconds:
Packet sent with a source address of 95.165.130.6 
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/5 ms
Chokurdah-R28#
```
