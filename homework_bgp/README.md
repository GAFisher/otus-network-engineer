# Настройка BGP между автономными системами
## Задание:
1. Настроить BGP между автономными системами
2. Организовать доступность между офисами Москва и С.-Петербург

## Решение:
1. Настроим eBGP между офисом Москва и двумя провайдерами - Киторн и Ламас;
2. Настроим eBGP между провайдерами Киторн и Ламас;
3. Настроим eBGP между Ламас и Триада;
4. Настроим eBGP между офисом С.-Петербург и провайдером Триада;
5. Организуем IP доступность между пограничным роутерами офисами Москва и С.-Петербург.

### 1. Настроим eBGP между офисом Москва и двумя провайдерами - Киторн и Ламас
Запустим BGP процесс на R22 и поднимем пиринг с R14:
```
Kirton-R22#configure terminal
Kirton-R22(config)#router bgp 101
Kirton-R22(config-router)#bgp router-id 22.22.22.22
Kirton-R22(config-router)#neighbor 84.52.118.226 remote-as 1001
Kirton-R22(config-router)#end
Kirton-R22#wr
```
Конфигурация с обратной стороны симметрична:
```
Moscow-R14#configure terminal 
Moscow-R14(config)#router bgp 1001
Moscow-R14(config-router)#bgp router-id 14.14.14.14
Moscow-R14(config-router)#neighbor 84.52.118.225 remote-as 101
Moscow-R14(config-router)#end
Moscow-R14#wr
```

<details>
  <summary>Убедимся, что BGP поднялся:</summary>

    Kirton-R22#show ip bgp neighbors
    BGP neighbor is 84.52.118.226,  remote AS 1001, external link
      BGP version 4, remote router ID 14.14.14.14
      BGP state = Established, up for 00:00:29
      Last read 00:00:29, last write 00:00:29, hold time is 180, keepalive interval is 60 seconds
      Neighbor sessions:
        1 active, is not multisession capable (disabled)
      Neighbor capabilities:
        Route refresh: advertised and received(new)
        Four-octets ASN Capability: advertised and received
        Address family IPv4 Unicast: advertised and received
        Enhanced Refresh Capability: advertised and received
        Multisession Capability: 
        Stateful switchover support enabled: NO for session 1
      Message statistics:
        InQ depth is 0
        OutQ depth is 0

                             Sent       Rcvd
        Opens:                  1          1
        Notifications:          0          0
        Updates:                0          0
        Keepalives:             1          1
        Route Refresh:          0          0
        Total:                  2          2
      Default minimum time between advertisement runs is 30 seconds
      ---вывод команды опущен---
  
    Moscow-R14#show ip bgp neighbors
    BGP neighbor is 84.52.118.225,  remote AS 101, external link
      BGP version 4, remote router ID 22.22.22.22
      BGP state = Established, up for 00:01:11
      Last read 00:00:14, last write 00:00:13, hold time is 180, keepalive interval is 60 seconds
      Neighbor sessions:
        1 active, is not multisession capable (disabled)
      Neighbor capabilities:
        Route refresh: advertised and received(new)
        Four-octets ASN Capability: advertised and received
        Address family IPv4 Unicast: advertised and received
        Enhanced Refresh Capability: advertised and received
        Multisession Capability: 
        Stateful switchover support enabled: NO for session 1
      Message statistics:
        InQ depth is 0
        OutQ depth is 0

                             Sent       Rcvd
        Opens:                  1          1
        Notifications:          0          0
        Updates:                1          0
        Keepalives:             2          2
        Route Refresh:          0          0
        Total:                  4          3
      Default minimum time between advertisement runs is 30 seconds
      ---вывод команды опущен---
  
</details>

Запустим BGP процесс на R22 и поднимем пиринг с R14:
```
Lamas-R21#configure terminal 
Lamas-R21(config)#router bgp 301
Lamas-R21(config-router)#bgp router-id 21.21.21.21
Lamas-R21(config-router)#neighbor 78.25.80.90 remote-as 1001 
Lamas-R21(config-router)#end
Lamas-R21#wr
```
Конфигурация с обратной стороны симметрична:
```
Moscow-R15#configure terminal
Moscow-R15(config)#router bgp 1001
Moscow-R15(config-router)#bgp router-id 15.15.15.15 
Moscow-R15(config-router)#neighbor 78.25.80.89 remote-as 301
Moscow-R15(config-router)#end
Moscow-R15#wr
```
<details>
  <summary>Убедимся, что BGP поднялся:</summary>

    Lamas-R21#show ip bgp neighbors
    BGP neighbor is 78.25.80.90,  remote AS 1001, external link
      BGP version 4, remote router ID 15.15.15.15
      BGP state = Established, up for 00:00:44
      Last read 00:00:44, last write 00:00:44, hold time is 180, keepalive interval is 60 seconds
      Neighbor sessions:
        1 active, is not multisession capable (disabled)
      Neighbor capabilities:
        Route refresh: advertised and received(new)
        Four-octets ASN Capability: advertised and received
        Address family IPv4 Unicast: advertised and received
        Enhanced Refresh Capability: advertised and received
        Multisession Capability: 
        Stateful switchover support enabled: NO for session 1
      Message statistics:
        InQ depth is 0
        OutQ depth is 0

                             Sent       Rcvd
        Opens:                  1          1
        Notifications:          0          0
        Updates:                0          0
        Keepalives:             1          1
        Route Refresh:          0          0
        Total:                  2          2
      Default minimum time between advertisement runs is 30 seconds
      ---вывод команды опущен---
  
      Moscow-R15#show ip bgp neighbors
      BGP neighbor is 78.25.80.89,  remote AS 301, external link
        BGP version 4, remote router ID 21.21.21.21
        BGP state = Established, up for 00:01:23
        Last read 00:00:24, last write 00:00:23, hold time is 180, keepalive interval is 60 seconds
        Neighbor sessions:
          1 active, is not multisession capable (disabled)
        Neighbor capabilities:
          Route refresh: advertised and received(new)
          Four-octets ASN Capability: advertised and received
          Address family IPv4 Unicast: advertised and received
          Enhanced Refresh Capability: advertised and received
          Multisession Capability: 
          Stateful switchover support enabled: NO for session 1
        Message statistics:
          InQ depth is 0
          OutQ depth is 0

                               Sent       Rcvd
          Opens:                  1          1
          Notifications:          0          0
          Updates:                1          0
          Keepalives:             2          2
          Route Refresh:          0          0
          Total:                  4          3
        Default minimum time between advertisement runs is 30 seconds
        ---вывод команды опущен---
  
</details>

[[Наверх]](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_bgp/README.md#настройка-bgp-между-автономными-системами)
### 2. Настроим eBGP между провайдерами Киторн и Ламас
```
Kirton-R22#configure terminal
Kirton-R22(config)#router bgp 101
Kirton-R22(config-router)#neighbor 128.0.128.2 remote-as 301
Kirton-R22(config-router)#end
```
```
Lamas-R21#configure terminal 
Lamas-R21(config)#router bgp 301
Lamas-R21(config-router)#neighbor 128.0.128.1 remote-as 101
Lamas-R21(config-router)#end
```


[[Наверх]](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_bgp/README.md#настройка-bgp-между-автономными-системами)
### 3. Настроим eBGP между Ламас и Триада
```
Lamas-R21#configure terminal
Lamas-R21(config)#router bgp 301
Lamas-R21(config-router)#neighbor 95.165.120.1 remote-as 520
Lamas-R21(config-router)#end
```
```
Triad-R24#configure terminal
Triad-R24(config)#router bgp 520
Triad-R24(config-router)#bgp router-id 24.24.24.24
Triad-R24(config-router)#neighbor 95.165.120.2 remote-as 301
Triad-R24(config-router)#end
Triad-R24#wr
```
[[Наверх]](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_bgp/README.md#настройка-bgp-между-автономными-системами)
### 4. Настроим eBGP между офисом С.-Петербург и провайдером Триада
```
Triad-R24#configure terminal
Triad-R24(config)#router bgp 520
Triad-R24(config-router)#neighbor  95.165.120.6 remote-as 2042
Triad-R24(config-router)#end
```
```
Triad-R26#configure terminal 
Triad-R26(config)#router bgp 520
Triad-R26(config-router)#bgp router-id 26.26.26.26
Triad-R26(config-router)#neighbor  95.165.140.6 remote-as 2042
Triad-R26(config-router)#end
Triad-R26#wr
```
```
St.Petersburg-R18#configure terminal 
St.Petersburg-R18(config)#router bgp 2042
St.Petersburg-R18(config-router)#bgp router-id 18.18.18.18
St.Petersburg-R18(config-router)#neighbor  95.165.140.5 remote-as 520
St.Petersburg-R18(config-router)#neighbor  95.165.120.5 remote-as 520
St.Petersburg-R18(config-router)#end
St.Petersburg-R18#wr
```

[[Наверх]](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_bgp/README.md#настройка-bgp-между-автономными-системами)
### 5. Организуем IP доступность между пограничным роутерами офисами Москва и С.-Петербург

[[Наверх]](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_bgp/README.md#настройка-bgp-между-автономными-системами)
