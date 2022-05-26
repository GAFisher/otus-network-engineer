# Настройка iBGP
## Задание:
1. Настроить iBGP в офисе Москва
2. Настроить iBGP в сети провайдера Триада
3. Организовать полную IP связанность всех сетей 

## Решение:
1. Настроите iBGP в офисом Москва между маршрутизаторами R14 и R15.
2. Настроите iBGP в провайдере Триада.
3. Настройте офиса Москва так, чтобы приоритетным провайдером стал Ламас.
4. Настройте офиса С.-Петербург так, чтобы трафик до любого офиса распределялся по двум линкам одновременно.



### Настроим iBGP в провайдере Триада
На всех маршрутизаторах на интерфейсе Loopback0 настроим IP-адрес X.X.X.X, где Х — номер маршрутизатора:
#### R23
```
Triad-R23#configure terminal
Triad-R23(config)#interface Loopback0
Triad-R23(config-if)#ip address 23.23.23.23 255.255.255.255
```
#### R24
```
Triad-R24#configure terminal
Triad-R24(config)#interface Loopback0
Triad-R24(config-if)#ip address 24.24.24.24 255.255.255.255
```
#### R25
```
Triad-R25#configure terminal
Triad-R25(config)#interface Loopback0
Triad-R25(config-if)#ip address 25.25.25.25 255.255.255.255
```
#### R26
```
Triad-R26#configure terminal
Triad-R26(config)#interface Loopback0
Triad-R26(config-if)#ip address 26.26.26.26 255.255.255.255
```
С помощью протокола внутренней маршрутизации IS-IS все маршрутизаторы должны узнать обо всех адресах Loopback-интерфейсов. Включим 
#### R23
```
Triad-R23(config-if)#ip router isis
```
#### R24
```
Triad-R24(config-if)#ip router isis
```
#### R25
```
Triad-R25(config-if)#ip router isis
```
#### R26
```
Triad-R26(config-if)#ip router isis
```
Проверим, что появилась связность со всеми Loopback-адресами:
#### R23
```
Triad-R23#show ip route isis | begin Gateway
Gateway of last resort is not set

      24.0.0.0/32 is subnetted, 1 subnets
i L2     24.24.24.24 [115/20] via 35.10.65.34, 00:19:30, Ethernet0/2
      25.0.0.0/32 is subnetted, 1 subnets
i L1     25.25.25.25 [115/20] via 35.10.65.2, 00:19:40, Ethernet0/1
      26.0.0.0/32 is subnetted, 1 subnets
i L2     26.26.26.26 [115/30] via 35.10.65.34, 00:19:25, Ethernet0/2
                     [115/30] via 35.10.65.2, 00:19:25, Ethernet0/1
      35.0.0.0/8 is variably subnetted, 6 subnets, 2 masks
i L2     35.10.65.16/28 [115/20] via 35.10.65.34, 00:19:30, Ethernet0/2
i L1     35.10.65.48/28 [115/20] via 35.10.65.2, 00:19:40, Ethernet0/1
Triad-R23#
```
#### R24
```
Triad-R24#show ip route isis | begin Gateway
Gateway of last resort is not set

      23.0.0.0/32 is subnetted, 1 subnets
i L2     23.23.23.23 [115/20] via 35.10.65.33, 00:22:23, Ethernet0/2
      25.0.0.0/32 is subnetted, 1 subnets
i L2     25.25.25.25 [115/30] via 35.10.65.33, 00:21:55, Ethernet0/2
                     [115/30] via 35.10.65.18, 00:21:55, Ethernet0/1
      26.0.0.0/32 is subnetted, 1 subnets
i L2     26.26.26.26 [115/20] via 35.10.65.18, 00:21:45, Ethernet0/1
      35.0.0.0/8 is variably subnetted, 6 subnets, 2 masks
i L2     35.10.65.0/28 [115/20] via 35.10.65.33, 00:22:23, Ethernet0/2
i L2     35.10.65.48/28 [115/20] via 35.10.65.18, 00:21:45, Ethernet0/1
Triad-R24#
```
#### R25
```
Triad-R25#show ip route isis | begin Gateway
Gateway of last resort is not set

      23.0.0.0/32 is subnetted, 1 subnets
i L1     23.23.23.23 [115/20] via 35.10.65.1, 00:20:59, Ethernet0/0
      24.0.0.0/32 is subnetted, 1 subnets
i L2     24.24.24.24 [115/30] via 35.10.65.50, 00:20:26, Ethernet0/2
                     [115/30] via 35.10.65.1, 00:20:26, Ethernet0/0
      26.0.0.0/32 is subnetted, 1 subnets
i L2     26.26.26.26 [115/20] via 35.10.65.50, 00:20:21, Ethernet0/2
      35.0.0.0/8 is variably subnetted, 6 subnets, 2 masks
i L2     35.10.65.16/28 [115/20] via 35.10.65.50, 00:20:21, Ethernet0/2
i L1     35.10.65.32/28 [115/20] via 35.10.65.1, 00:20:59, Ethernet0/0
Triad-R25#
```
#### R26
```
Triad-R26#show ip route isis | begin Gateway
Gateway of last resort is not set

      23.0.0.0/32 is subnetted, 1 subnets
i L2     23.23.23.23 [115/30] via 35.10.65.49, 00:22:22, Ethernet0/2
                     [115/30] via 35.10.65.17, 00:22:22, Ethernet0/0
      24.0.0.0/32 is subnetted, 1 subnets
i L2     24.24.24.24 [115/20] via 35.10.65.17, 00:22:12, Ethernet0/0
      25.0.0.0/32 is subnetted, 1 subnets
i L2     25.25.25.25 [115/20] via 35.10.65.49, 00:22:22, Ethernet0/2
      35.0.0.0/8 is variably subnetted, 6 subnets, 2 masks
i L2     35.10.65.0/28 [115/20] via 35.10.65.49, 00:22:22, Ethernet0/2
i L2     35.10.65.32/28 [115/20] via 35.10.65.17, 00:22:12, Ethernet0/0
Triad-R26#
```
