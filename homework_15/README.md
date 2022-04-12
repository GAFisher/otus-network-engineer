# Многозонный OSPF
## Задание:
1. Настроить OSPF офисе Москва
2. Разделить сеть на зоны
3. Настроить фильтрацию между зонами
## Решение:
1. Настроим маршрутизаторы R14-R15 в зоне 0;
2. Настроим маршрутизаторы R12-R13 в зоне 10;
3. Настроим маршрутизатор R19 в зоне 101 и получание только маршрут по умолчанию;
4. Настроим маршрутизатор R20 в зоне 102 и получание всех маршрутов, кроме маршрутов до сетей зоны 101.


### Настроим маршрутизаторы R14-R15 в зоне 0
Маршрутизаторы являются граничными маршрутизатороми автономной системы (ASBR), т.к. подключены к Интернету, поэтому необходимо настроить распространение маршрута по умолчанию на другие маршрутизаторы с помощью команды ```default-information originate```. 
#### R14
```
Moscow-R14#configure terminal
Moscow-R14(config)#ip route 0.0.0.0 0.0.0.0 84.52.118.225
Moscow-R14(config)#ipv6 route ::/0 2606:4700:D0:C009::225
Moscow-R14(config)#router ospf 1 
Moscow-R14(config-router)#default-information originate 
Moscow-R14(config-router)#exit
Moscow-R14(config)#ipv6 router ospf 1
Moscow-R14(config-rtr)#default-information originate 
Moscow-R14(config-rtr)#exit
Moscow-R14(config-if-range)#interface range Ethernet0/0-1
Moscow-R14(config-if-range)#ip ospf 1 area 0
Moscow-R14(config-if-range)#ipv6 ospf 1 area 0
```
#### R15
```
Moscow-R15#configure terminal
Moscow-R15(config)#ip route 0.0.0.0 0.0.0.0 78.25.80.89
Moscow-R15(config)#ipv6 route ::/0 1A00:4700:D0:C005::89
Moscow-R15(config)#router ospf 1 
Moscow-R15(config-router)#default-information originate 
Moscow-R15(config-router)#exit
Moscow-R15(config)#ipv6 router ospf 1
Moscow-R15(config-rtr)#default-information originate 
Moscow-R15(config-rtr)#exit
Moscow-R15(config-if-range)#interface range Ethernet0/0-1
Moscow-R15(config-if-range)#ip ospf 1 area 0
Moscow-R15(config-if-range)#ipv6 ospf 1 area 0
```
### Настроим маршрутизаторы R12-R13 в зоне 10
Дополнительной настройки, кроме включения процесса ospf и включения его на инетерфейсах, т.к. маршрутизаторы являются пограничными (ABR), следовательно они будут передавать всю информацию о маршрутах из граничных областей. 
#### R12
```
Moscow-R12(config)#router ospf 1
Moscow-R12(config-router)#ro
Moscow-R12(config-router)#router-id 1.1.1.12
Moscow-R12(config-router)#exit
Moscow-R12(config)#interface range Et0/2-3
Moscow-R12(config-if-range)#ip ospf 1 area 0 
Moscow-R12(config-if-range)#ipv6 ospf 1 area 0
Moscow-R12(config)#int Et0/0.10                
Moscow-R12(config-subif)#ip ospf 1 area 10
Moscow-R12(config-subif)#ipv6 ospf 1 area 10
Moscow-R12(config-subif)#exit               
Moscow-R12(config)#int Et0/0.11       
Moscow-R12(config-subif)#ip ospf 1 area 10  
Moscow-R12(config-subif)#ipv6 ospf 1 area 10
```
#### Для R13 настройка аналогична
