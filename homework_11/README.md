# Маршрутизация на основе политик (PBR) 
## Задание:
1. Настроить политику маршрутизации для сетей офиса
2. Распределить трафик между двумя линками с провайдером
3. Настроить отслеживание линка через технологию IP SLA
4. Настройть для офиса Лабытнанги маршрут по-умолчанию
5. План работы и изменения зафиксируем в документации
## Решение: 
1. mm
2. mm
3. mm
4. Настроим для офиса Лабытнанги маршрут по-умолчанию
5. 



### 4. Настроим для офиса Лабытнанги маршрут по-умолчанию
```
**R27**#configure terminal 
Labytnangi-R27(config)#ip route 0.0.0.0 0.0.0.0 Ethernet0/0
Labytnangi-R27(config)#ipv6 route ::/0 Ethernet0/0
Labytnangi-R27(config)#exit
Labytnangi-R27#show ip route 
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override

Gateway of last resort is 0.0.0.0 to network 0.0.0.0

S*    0.0.0.0/0 is directly connected, Ethernet0/0
      10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C        10.4.99.0/24 is directly connected, Loopback1
L        10.4.99.1/32 is directly connected, Loopback1
      95.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C        95.165.130.0/30 is directly connected, Ethernet0/0
L        95.165.130.2/32 is directly connected, Ethernet0/0
Labytnangi-R27#
```

