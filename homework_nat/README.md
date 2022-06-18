# Основные протоколы сети интернет
## Задание:
1. Настроить DHCP в офисе Москва
2. Настроить синхронизацию времени в офисе Москва
3. Настроить NAT в офисе Москва, C.-Перетбруг и Чокурдах

## Решение:
1. Настроим NAT(PAT) на R14 и R15; 
2. Настроим NAT(PAT) на R18; 
3. Настроим статический NAT для R20;
4. Настроим NAT так, чтобы R19 был доступен с любого узла для удаленного управления;
5. Настроим статический NAT(PAT) для офиса Чокурдах;
6. Настроим для IPv4 DHCP сервер в офисе Москва на маршрутизаторах R12 и R13;
7. Настроим NTP сервер на R12 и R13.

### 1. Настроим NAT(PAT) на R14 и R15
Трансляция должна осуществляться в адрес автономной системы AS1001. Для AS1001 выделим блок PI адресов 213.140.240.0/29.

#### Сначала произведём конфигурацию R14. 

На интерфейс Loopback2 назначим белый IP из диапозона PI, проанонсируем его через BGP:
```
Moscow-R14#configure terminal 
Moscow-R14(config)#interface Loopback2
Moscow-R14(config-if)#ip address 213.140.240.1 255.255.255.255
Moscow-R14(config-if)#exit
Moscow-R14(config)# router bgp 1001
Moscow-R14(config-router)#address-family ipv4
Moscow-R14(config-router-af)#network 213.140.240.1 mask 255.255.255.255
Moscow-R14(config-router-af)#exit
Moscow-R14(config-router)#exit
```
Интерфейс в сторону ISP обозначим как outside, остальные интерфейсы как inside:
```
Moscow-R14(config)#interface Ethernet0/2
Moscow-R14(config-if)#ip nat outside
Moscow-R14(config-if)#exit          
Moscow-R14(config)#interface range Ethernet0/0-1, Ethernet0/3
Moscow-R14(config-if-range)#ip nat inside 
Moscow-R14(config-if-range)#exit
```
Создадим access-list для подсетей офиса:
```
Moscow-R14(config)#access-list 1 permit 10.1.110.0 0.0.0.255
Moscow-R14(config)#access-list 1 permit 10.1.100.0 0.0.0.255
```
Создадим запись для NAT трансляции:
```
Moscow-R14(config)#ip nat inside source list 1 interface Loopback2 overload
Moscow-R14(config)#end
Moscow-R14#wr
```
#### Настройка для R15 аналогична:

```
Moscow-R15#configure terminal 
Moscow-R15(config)#interface Loopback2
Moscow-R15(config-if)#ip address 213.140.240.2 255.255.255.255
Moscow-R15(config-if)#exit
Moscow-R15(config)# router bgp 1001
Moscow-R15(config-router)#address-family ipv4
Moscow-R15(config-router-af)#network 213.140.240.2 mask 255.255.255.255
Moscow-R15(config-router-af)#exit
Moscow-R15(config-router)#exit
Moscow-R15(config)#interface Ethernet0/2
Moscow-R15(config-if)#ip nat outside
Moscow-R15(config-if)#exit          
Moscow-R15(config)#interface range Ethernet0/0-1, Ethernet0/3
Moscow-R15(config-if-range)#ip nat inside 
Moscow-R15(config-if-range)#exit
Moscow-R15(config)#access-list 1 permit 10.1.110.0 0.0.0.255
Moscow-R15(config)#access-list 1 permit 10.1.100.0 0.0.0.255
Moscow-R15(config)#ip nat inside source list 1 interface Loopback2 overload
Moscow-R15(config)#end
Moscow-R15#wr
```



5. Настроим статический NAT(PAT) для офиса Чокурдах

На R28 пометим интерфейсы в сторону ISP как outside, а сабинтерфейсы в сторону ПК как inside:
```
Chokurdah-R28#configure terminal
Chokurdah-R28(config)#interface range Ethernet0/0-1
Chokurdah-R28(config-if-range)#ip nat outside
Chokurdah-R28(config-if-range)#exit
Chokurdah-R28(config)#interface Ethernet0/2.30
Chokurdah-R28(config-subif)#ip nat inside 
Chokurdah-R28(config-subif)#exit
Chokurdah-R28(config)#interface Ethernet0/2.31             
Chokurdah-R28(config-subif)#ip nat inside           
Chokurdah-R28(config-subif)#exit
```




