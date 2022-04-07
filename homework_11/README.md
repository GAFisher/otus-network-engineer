# Маршрутизация на основе политик (PBR) 
## Задание:
1. Настроить политику маршрутизации для сетей офиса
2. Распределить трафик между двумя линками с провайдером
3. Настроить отслеживание линка через технологию IP SLA
4. Настройть для офиса Лабытнанги маршрут по-умолчанию
5. План работы и изменения зафиксируем в документации
## Решение: 
1. Настроим политику маршрутизации для распределения трафика между двумя линками с провайдером
2. mm
3. Настроим для офиса Лабытнанги маршрут по-умолчанию
5. 

### 1. Настроим политику маршрутизации для распределения трафика между двумя линками с провайдером
Настроим правила для отбора трафика из каждого VLAN’а:
```
Chokurdah-R28#configure terminal 
Chokurdah-R28(config)#ip access-list extended BUH-VPC30
Chokurdah-R28(config-ext-nacl)#permit ip 10.3.30.0 0.0.0.255 any
Chokurdah-R28(config-ext-nacl)#exit
Chokurdah-R28(config)#ip access-list extended PR-VPC31
Chokurdah-R28(config-ext-nacl)#permit ip 10.3.31.0 0.0.0.255 any
Chokurdah-R28(config-ext-nacl)#exit
Chokurdah-R28(config)#
```
Настроим политику маршрутизации так, чтобы трафик VLAN30 отправлялся через ISP1, а трафик VLAN31 отправлялся через ISP2:
```
Chokurdah-R28(config)#route-map ISP2 permit 10
Chokurdah-R28(config-route-map)#description Triad-R25 
Chokurdah-R28(config-route-map)#match ip address PR-VPC31
Chokurdah-R28(config-route-map)#set ip next-hop 95.165.130.5
Chokurdah-R28(config-ext-nacl)#exit
Chokurdah-R28(config)#route-map ISP1 permit 10
Chokurdah-R28(config-route-map)#description Triad-R26 
Chokurdah-R28(config-route-map)#match ip address BUH-VPC30
Chokurdah-R28(config-route-map)#set ip next-hop 95.165.140.1
Chokurdah-R28(config-route-map)#exit
Chokurdah-R28(config)#
```
Настроим NAT трансляцию:
```
Chokurdah-R28(config)#ip nat source list BUH-VPC30 interface Ethernet0/0 overload
Chokurdah-R28(config)#ip nat source list PR-VPC31 interface Ethernet0/1 overload
Chokurdah-R28(config)#
```
Проверим, что всё работает:
```
VPC30> show ip  

NAME        : VPC30[1]
IP/MASK     : 10.3.30.2/24
GATEWAY     : 10.3.30.1
DNS         : 
DHCP SERVER : 10.3.30.1
DHCP LEASE  : 86339, 86400/43200/75600
DOMAIN NAME : otus-lab.ru
MAC         : 00:50:79:66:68:1e
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

VPC30> ping 95.165.140.1

84 bytes from 95.165.140.1 icmp_seq=1 ttl=254 time=0.947 ms
84 bytes from 95.165.140.1 icmp_seq=2 ttl=254 time=1.320 ms
84 bytes from 95.165.140.1 icmp_seq=3 ttl=254 time=0.927 ms
84 bytes from 95.165.140.1 icmp_seq=4 ttl=254 time=1.076 ms
84 bytes from 95.165.140.1 icmp_seq=5 ttl=254 time=0.858 ms

VPC30> ping 95.165.130.5

95.165.130.5 icmp_seq=1 timeout
95.165.130.5 icmp_seq=2 timeout
95.165.130.5 icmp_seq=3 timeout
95.165.130.5 icmp_seq=4 timeout
95.165.130.5 icmp_seq=5 timeout
```
```
VPC30>

VPC31> show ip

NAME        : VPC31[1]
IP/MASK     : 10.3.31.2/24
GATEWAY     : 10.3.31.1
DNS         : 
DHCP SERVER : 10.3.31.1
DHCP LEASE  : 86315, 86400/43200/75600
DOMAIN NAME : otus-lab.ru
MAC         : 00:50:79:66:68:1f
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

VPC31> ping 95.165.130.5

84 bytes from 95.165.130.5 icmp_seq=1 ttl=254 time=0.915 ms
84 bytes from 95.165.130.5 icmp_seq=2 ttl=254 time=1.156 ms
84 bytes from 95.165.130.5 icmp_seq=3 ttl=254 time=0.986 ms
84 bytes from 95.165.130.5 icmp_seq=4 ttl=254 time=0.988 ms
84 bytes from 95.165.130.5 icmp_seq=5 ttl=254 time=1.284 ms

VPC31> ping 95.165.140.1

95.165.140.1 icmp_seq=1 timeout
95.165.140.1 icmp_seq=2 timeout
95.165.140.1 icmp_seq=3 timeout
95.165.140.1 icmp_seq=4 timeout
95.165.140.1 icmp_seq=5 timeout

VPC31>
```
### 2. Настроим отслеживание линков в сторону ISP
Настроим IP SLA для проверки доступности провайдеров (icmp-echo):
```
Chokurdah-R28#configure terminal 
Chokurdah-R28(config)#ip sla 1
Chokurdah-R28(config-ip-sla)# icmp-echo 95.165.140.1 source-ip 95.165.140.2
Chokurdah-R28(config-ip-sla-echo)# frequency 5
Chokurdah-R28(config-ip-sla-echo)#ip sla schedule 1 life forever start-time now        
Chokurdah-R28(config)#ip sla 2
Chokurdah-R28(config-ip-sla)# icmp-echo 95.165.130.5 source-ip 95.165.130.6
Chokurdah-R28(config-ip-sla-echo)# frequency 5
Chokurdah-R28(config-ip-sla-echo)#ip sla schedule 2 life forever start-time now        
Chokurdah-R28(config)#
```
Настроим track для отслеживания теста IP SLA:
```
Chokurdah-R28(config)#track 10 ip sla 1 reachability
Chokurdah-R28(config-track)#delay down 10 up 5                     
Chokurdah-R28(config-track)#exit
Chokurdah-R28(config)#track 20  ip sla 2 reachability
Chokurdah-R28(config-track)#delay down 10 up 5                     
Chokurdah-R28(config-track)#exit
Chokurdah-R28(config)#
```
Добавим статические маршруты на провайдеров:
```
Chokurdah-R28(config)#ip route 0.0.0.0 0.0.0.0 95.165.140.1 track 1   
Chokurdah-R28(config)#ip route 0.0.0.0 0.0.0.0 95.165.130.5 track 2
Chokurdah-R28(config)#
```

### 3. Настроим для офиса Лабытнанги маршрут по-умолчанию
```
Labytnangi-R27#configure terminal 
Labytnangi-R27(config)#ip route 0.0.0.0 0.0.0.0 Ethernet0/0
Labytnangi-R27(config)#ipv6 route ::/0 Ethernet0/0
Labytnangi-R27(config)#exit
Labytnangi-R27#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
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
Labytnangi-R27#sh ipv6 route
IPv6 Routing Table - default - 6 entries
Codes: C - Connected, L - Local, S - Static, U - Per-user Static route
       B - BGP, HA - Home Agent, MR - Mobile Router, R - RIP
       H - NHRP, I1 - ISIS L1, I2 - ISIS L2, IA - ISIS interarea
       IS - ISIS summary, D - EIGRP, EX - EIGRP external, NM - NEMO
       ND - ND Default, NDp - ND Prefix, DCE - Destination, NDr - Redirect
       O - OSPF Intra, OI - OSPF Inter, OE1 - OSPF ext 1, OE2 - OSPF ext 2
       ON1 - OSPF NSSA ext 1, ON2 - OSPF NSSA ext 2, la - LISP alt
       lr - LISP site-registrations, ld - LISP dyn-eid, a - Application
S   ::/0 [1/0]
     via Ethernet0/0, directly connected
C   2001:20DA:EDA:4::/64 [0/0]
     via Ethernet0/0, directly connected
L   2001:20DA:EDA:4::2/128 [0/0]
     via Ethernet0/0, receive
C   2A00:FACE:C004:99::/64 [0/0]
     via Loopback1, directly connected
L   2A00:FACE:C004:99::1/128 [0/0]
     via Loopback1, receive
L   FF00::/8 [0/0]
     via Null0, receive
Labytnangi-R27#
```

