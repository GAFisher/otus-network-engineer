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
Kirton-R22(config-router)#neighbor 84.52.118.226 remote-as 1001
Kirton-R22(config-router)#end
```
Конфигурация с обратной стороны симметрична:
```
Moscow-R14#configure terminal 
Moscow-R14(config)#router bgp 1001
Moscow-R14(config-router)#neighbor 84.52.118.225 remote-as 101
Kirton-R14(config-router)#end
```

<details>
  <summary>Убедимся, что BGP поднялся:</summary>

    Kirton-R22#show ip bgp neighbors 
    BGP neighbor is 84.52.118.226,  remote AS 1001, external link
      BGP version 4, remote router ID 10.1.99.14
      BGP state = Established, up for 00:02:01
      Last read 00:00:11, last write 00:00:11, hold time is 180, keepalive interval is 60 seconds
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
        Updates:                1          1
        Keepalives:             4          4
        Route Refresh:          0          0
        Total:                  6          6
      Default minimum time between advertisement runs is 30 seconds
      ---вывод команды опущен---
  
</details>

Запустим BGP процесс на R22 и поднимем пиринг с R14:
```
Lamas-R21#configure terminal 
Lamas-R21(config)#router bgp 301
Lamas-R21(config-router)#neighbor 78.25.80.90 remote-as 1001 
Lamas-R21(config-router)#end
```
Конфигурация с обратной стороны симметрична:
```
Moscow-R15#configure terminal
Moscow-R15(config)#router bgp 1001
Moscow-R15(config-router)#neighbor 78.25.80.89 remote-as 301
Moscow-R15(config-router)#end
```
<details>
  <summary>Убедимся, что BGP поднялся:</summary>

    Lamas-R21#show ip bgp neighbors
    BGP neighbor is 78.25.80.90,  remote AS 1001, external link
      BGP version 4, remote router ID 10.1.99.15
      BGP state = Established, up for 00:01:31
      Last read 00:00:33, last write 00:00:33, hold time is 180, keepalive interval is 60 seconds
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
        Updates:                1          1
        Keepalives:             3          3
        Route Refresh:          0          0
        Total:                  5          5
      Default minimum time between advertisement runs is 30 seconds
      ---вывод команды опущен---
  
</details>

### 2. Настроим eBGP между провайдерами Киторн и Ламас


### 3. Настроим eBGP между Ламас и Триада


### 4. Настроим eBGP между офисом С.-Петербург и провайдером Триада


### 5. Организуем IP доступность между пограничным роутерами офисами Москва и С.-Петербург

