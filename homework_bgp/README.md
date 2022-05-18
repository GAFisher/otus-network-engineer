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
Kirton-R22(config-router)#neighbor 2606:4700:D0:C009::226 remote-as 1001
Kirton-R22(config-router)#address-family ipv6 
Kirton-R22(config-router-af)#neighbor 2606:4700:D0:C009::226 activate
Kirton-R22(config-router)#end
Kirton-R22#wr
```
Конфигурация с обратной стороны симметрична:
```
Moscow-R14#configure terminal 
Moscow-R14(config)#router bgp 1001
Moscow-R14(config-router)#bgp router-id 14.14.14.14
Moscow-R14(config-router)#neighbor 84.52.118.225 remote-as 101
Moscow-R14(config-router)#neighbor 2606:4700:D0:C009::225 remote-as 101
Moscow-R14(config-router)#address-family ipv6
Moscow-R14(config-router-af)#neighbor 2606:4700:D0:C009::225 activate
Moscow-R14(config-router)#end
Moscow-R14#wr
```

Запустим BGP процесс на R21 и поднимем пиринг с R15:
```
Lamas-R21#configure terminal 
Lamas-R21(config)#router bgp 301
Lamas-R21(config-router)#bgp router-id 21.21.21.21
Lamas-R21(config-router)#neighbor 78.25.80.90 remote-as 1001 
Lamas-R21(config-router)#neighbor 1A00:4700:D0:C005::90 remote-as 1001
Lamas-R21(config-router)#address-family ipv6
Lamas-R21(config-router-af)#neighbor 1A00:4700:D0:C005::90 activate
Lamas-R21(config-router)#end
Lamas-R21#wr
```
Конфигурация с обратной стороны симметрична:
```
Moscow-R15#configure terminal
Moscow-R15(config)#router bgp 1001
Moscow-R15(config-router)#bgp router-id 15.15.15.15 
Moscow-R15(config-router)#neighbor 78.25.80.89 remote-as 301
Moscow-R15(config-router)#neighbor 1A00:4700:D0:C005::89 remote-as 301
Moscow-R15(config-router)#address-family ipv6
Moscow-R15(config-router-af)#neighbor 1A00:4700:D0:C005::89 activate
Moscow-R15(config-router)#end
Moscow-R15#wr
```
Убедимся, что BGP поднялся:

[[Наверх]](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_bgp/README.md#настройка-bgp-между-автономными-системами)
### 2. Настроим eBGP между провайдерами Киторн и Ламас
#### R22
```
Kirton-R22#configure terminal
Kirton-R22(config)#router bgp 101
Kirton-R22(config-router)#neighbor 128.0.128.2 remote-as 301
Kirton-R22(config-router)#neighbor 64:FF9B:5276:1EB4::2 remote-as 301
Kirton-R22(config-router)#address-family ipv6 
Kirton-R22(config-router-af)#neighbor 64:FF9B:5276:1EB4::2 activate
Kirton-R22(config-router)#end
Kirton-R22#wr
```
#### R21
```
Lamas-R21#configure terminal 
Lamas-R21(config)#router bgp 301
Lamas-R21(config-router)#neighbor 128.0.128.1 remote-as 101
Lamas-R21(config-router)#neighbor 64:FF9B:5276:1EB4::1 remote-as 101
Lamas-R21(config-router)#address-family ipv6
Lamas-R21(config-router-af)#neighbor 64:FF9B:5276:1EB4::1 activate 
Lamas-R21(config-router)#end
Lamas-R21#wr
```


[[Наверх]](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_bgp/README.md#настройка-bgp-между-автономными-системами)
### 3. Настроим eBGP между Ламас и Триада
```
Lamas-R21#configure terminal
Lamas-R21(config)#router bgp 301
Lamas-R21(config-router)#neighbor 95.165.120.1 remote-as 520
Lamas-R21(config-router)#neighbor 2001:20DA:EDA:2::1 remote-as 520
Lamas-R21(config-router)#address-family ipv6
Lamas-R21(config-router-af)#neighbor 2001:20DA:EDA:2::1 activate
Lamas-R21(config-router)#end
```
```
Triad-R24#configure terminal
Triad-R24(config)#router bgp 520
Triad-R24(config-router)#bgp router-id 24.24.24.24
Triad-R24(config-router)#neighbor 95.165.120.2 remote-as 301
Triad-R24(config-router)#neighbor 2001:20DA:EDA:2::2 remote-as 301
Triad-R24(config-router)#address-family ipv6 
Triad-R24(config-router-af)#neighbor 2001:20DA:EDA:2::2 activate
Triad-R24(config-router)#end
Triad-R24#wr
```
[[Наверх]](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_bgp/README.md#настройка-bgp-между-автономными-системами)
### 4. Настроим eBGP между офисом С.-Петербург и провайдером Триада
```
Triad-R24#configure terminal
Triad-R24(config)#router bgp 520
Triad-R24(config-router)#neighbor  95.165.120.6 remote-as 2042
Triad-R24(config-router)#neighbor 2001:20DA:EDA:3::6 remote-as 2042
Triad-R24(config-router)#address-family ipv6
Triad-R24(config-router-af)#neighbor 2001:20DA:EDA:3::6 activate
Triad-R24(config-router)#end
Triad-R24#wr
```
```
Triad-R26#configure terminal 
Triad-R26(config)#router bgp 520
Triad-R26(config-router)#bgp router-id 26.26.26.26
Triad-R26(config-router)#neighbor  95.165.140.6 remote-as 2042
Triad-R26(config-router)#neighbor 2001:20DA:EDA:7::6 remote-as 2042
Triad-R26(config-router)#address-family ipv6 
Triad-R26(config-router-af)#neighbor 2001:20DA:EDA:7::6 activate
Triad-R26(config-router)#end
Triad-R26#wr
```
```
St.Petersburg-R18#configure terminal 
St.Petersburg-R18(config)#router bgp 2042
St.Petersburg-R18(config-router)#bgp router-id 18.18.18.18
St.Petersburg-R18(config-router)#neighbor  95.165.140.5 remote-as 520
St.Petersburg-R18(config-router)#neighbor  95.165.120.5 remote-as 520
St.Petersburg-R18(config-router)#neighbor 2001:20DA:EDA:3::5 remote-as 520
St.Petersburg-R18(config-router)#neighbor 2001:20DA:EDA:7::5 remote-as 520
St.Petersburg-R18(config-router)#address-family ipv6 
St.Petersburg-R18(config-router-af)#neighbor 2001:20DA:EDA:3::5 activate
St.Petersburg-R18(config-router-af)#neighbor 2001:20DA:EDA:7::5 activate
St.Petersburg-R18(config-router)#end
St.Petersburg-R18#wr
```

[[Наверх]](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_bgp/README.md#настройка-bgp-между-автономными-системами)
### 5. Организуем IP доступность между пограничным роутерами офисами Москва и С.-Петербург

[[Наверх]](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_bgp/README.md#настройка-bgp-между-автономными-системами)
