### Задание:
1. Настроить IS-IS в ISP Триада.
### Решение:
1. Настроим маршрутизаторы R23 и R25 в зоне 2222.
2. Настроим маршрутизатор R24 в зоне 24.
3. Настроим маршрутизатор R26 в зоне 26. 

#### Настроим маршрутизаторы R23 и R25 в зоне 2222
```
Triad-R23#configure terminal 
Triad-R23(config)#router isis
Triad-R23(config-router)#net 49.2222.0000.0000.0023.00
Triad-R23(config-router)#exit
Triad-R23(config)#interface Ethernet0/1
Triad-R23(config-if)#ip router isis
Triad-R23(config-if)#end
Triad-R23#
```
```
Triad-R25#configure terminal
Triad-R25(config)#router isis
Triad-R25(config-router)#net 49.2222.0000.0000.0025.00
Triad-R25(config-router)#exit
Triad-R25(config)#interface Ethernet0/0
Triad-R25(config-if)#ip router isis
Triad-R25(config-if)#end
Triad-R25#
```
