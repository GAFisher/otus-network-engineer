# Настройка iBGP
## Задание:
1. Настроить iBGP в офисе Москва
2. Настроить iBGP в сети провайдера Триада
3. Организовать полную IP связанность всех сетей 

## Решение:
1. Настроим iBGP в офисом Москва между маршрутизаторами R14 и R15;
2. Настройте офиса Москва так, чтобы приоритетным провайдером стал Ламас;
3. Настроите iBGP в провайдере Триада.
4. Настройте офиса Москва так, чтобы приоритетным провайдером стал Ламас.
5. Настройте офиса С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно.


### 1. Настроим iBGP в офисом Москва между маршрутизаторами R14 и R15
Поднимем Loopback-интерфейсы на маршрутизаторах R14 и R15, распространим их через протокол OSPF:
#### R14
```
Moscow-R14#configure terminal
Moscow-R14(config)#interface Loopback0
Moscow-R14(config-if)#ip address 14.14.14.14 255.255.255.255
Moscow-R14(config-if)#ip ospf 1 area 0
Moscow-R14(config-if)#ipv6 address FC00::14/128
Moscow-R14(config-if)#ipv6 ospf 1 area 0
```
#### R15
```
Moscow-R15#configure terminal
Moscow-R15(config)#interface Loopback0
Moscow-R15(config-if)#ip address 15.15.15.15 255.255.255.255
Moscow-R15(config-if)#ip ospf 1 area 0
Moscow-R15(config-if)#ipv6 address FC00::15/128
Moscow-R15(config-if)#ipv6 ospf 1 area 0
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
```
### 2. Настроим офис Москва так, чтобы приоритетным провайдером стал Ламас:
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
```

### Настроим iBGP в провайдере Триада
Произведён настройку Loopback-интерфейсов. С помощью протокола внутренней маршрутизации IS-IS все маршрутизаторы должны узнать обо всех адресах Loopback-интерфейсов. 
#### R23
```
Triad-R23#configure terminal
Triad-R23(config)#interface Loopback0
Triad-R23(config-if)#ip address 23.23.23.23 255.255.255.255
Triad-R23(config-if)#ip router isis
Triad-R23(config-if)#ipv6 address FC00::23/128
Triad-R23(config-if)#ipv6 router isis
Triad-R23(config-if)#end
Triad-R23#wr
```
#### R24
```
Triad-R24#configure terminal
Triad-R24(config)#interface Loopback0
Triad-R24(config-if)#ip address 24.24.24.24 255.255.255.255
Triad-R24(config-if)#ip router isis
Triad-R24(config-if)#ipv6 address FC00::23/128
Triad-R24(config-if)#ipv6 router isis 
Triad-R24(config-if)#end
Triad-R24#wr
```
#### R25
```
Triad-R25#configure terminal
Triad-R25(config)#interface Loopback0
Triad-R25(config-if)#ip address 25.25.25.25 255.255.255.255
Triad-R25(config-if)#ip router isis
Triad-R25(config-if)#ipv6 address FC00::25/128
Triad-R25(config-if)#ipv6 router isis
Triad-R25(config-if)#end
Triad-R25#wr
```
#### R26
```
Triad-R26#configure terminal
Triad-R26(config)#interface Loopback0
Triad-R26(config-if)#ip address 26.26.26.26 255.255.255.255
Triad-R26(config-if)#ip router isis
Triad-R26(config-if)#ipv6 address FC00::26/128
Triad-R26(config-if)#ipv6 router isis
Triad-R26(config-if)#end
Triad-R26#wr
```
Произведём настройку соседей. В качестве Route Reflectror настроим маршрутизатор R23. 
Для IPv4 объединим всех соседей в peer-group.
#### R23
```
Triad-R23#configure terminal
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
Triad-R23(config-router-af)#end
Triad-R23#wr
```
На маршрутизаторах R24-26 пропишем соседом только R23 (настройка идентична на всех маршрутизаторах):
```
Triad-R25#configure terminal
Triad-R25(config)#router bgp 520
Triad-R25(config-router)#neighbor 23.23.23.23 remote-as 520
Triad-R25(config-router)#neighbor 23.23.23.23 update-source Loopback0
Triad-R25(config-router)#neighbor 23.23.23.23 next-hop-self 
Triad-R25(config-router)#end
Triad-R25#wr
```

