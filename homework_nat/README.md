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
[[Наверх]](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_nat/README.md#основные-протоколы-сети-интернет)
### 2. Настроим NAT(PAT) на R18
Трансляция должна осуществляться в пул из 5 адресов автономной системы AS2042. Для AS1001 выделим блок PI адресов 213.140.240.8/29.

На интерфейс Loopback2 назначим белый IP из диапозона PI. Добавим развешающее правило для подсети в prefix-list и проанонсируем через BGP:
```
St.Petersburg-R18#configure terminal 
St.Petersburg-R18(config)#interface Loopback2
St.Petersburg-R18(config-if)#ip address 213.140.240.9 255.255.255.248
St.Petersburg-R18(config-if)#exit
St.Petersburg-R18(config)#ip prefix-list LIST_OUT1 seq 10 permit 213.140.240.8/29     
St.Petersburg-R18(config)# router bgp 2042
St.Petersburg-R18(config-router)#address-family ipv4
St.Petersburg-R18(config-router-af)#network 213.140.240.8 mask 255.255.255.248
St.Petersburg-R18(config-router-af)#exit
St.Petersburg-R18(config-router)#exit
```
Создадим access-list для подсетей офиса:
```
St.Petersburg-R18(config)#access-list 1 permit 10.2.20.0 0.0.0.255
St.Petersburg-R18(config)#access-list 1 permit 10.2.21.0 0.0.0.255
```
Создадим pool для 5 NAT адресов:
```
St.Petersburg-R18(config)#ip nat pool NAT_POOL 213.140.240.9 213.140.240.13 netmask 255.255.255.248
```
Создадим запись для NAT трансляции:
```
ip nat inside source list 1 pool NAT_POOL overload
```
Проверим:
#### SRE
```
VPC8> ping 95.165.120.5

84 bytes from 95.165.120.5 icmp_seq=1 ttl=253 time=1.034 ms
84 bytes from 95.165.120.5 icmp_seq=2 ttl=253 time=1.179 ms
84 bytes from 95.165.120.5 icmp_seq=3 ttl=253 time=1.213 ms
84 bytes from 95.165.120.5 icmp_seq=4 ttl=253 time=1.139 ms
84 bytes from 95.165.120.5 icmp_seq=5 ttl=253 time=1.321 ms

VPC8>
```
#### QA
```
VPC> ping 95.165.140.5

84 bytes from 95.165.140.5 icmp_seq=1 ttl=253 time=1.249 ms
84 bytes from 95.165.140.5 icmp_seq=2 ttl=253 time=1.622 ms
84 bytes from 95.165.140.5 icmp_seq=3 ttl=253 time=1.235 ms
84 bytes from 95.165.140.5 icmp_seq=4 ttl=253 time=1.486 ms
84 bytes from 95.165.140.5 icmp_seq=5 ttl=253 time=1.247 ms

VPC> 
```
#### R18
```
St.Petersburg-R18#show ip nat translations 
Pro Inside global      Inside local       Outside local      Outside global
icmp 213.140.240.9:2801 10.2.20.4:2801    95.165.120.5:2801  95.165.120.5:2801
icmp 213.140.240.9:3057 10.2.20.4:3057    95.165.120.5:3057  95.165.120.5:3057
icmp 213.140.240.9:3313 10.2.20.4:3313    95.165.120.5:3313  95.165.120.5:3313
icmp 213.140.240.9:3569 10.2.20.4:3569    95.165.120.5:3569  95.165.120.5:3569
icmp 213.140.240.9:3825 10.2.20.4:3825    95.165.120.5:3825  95.165.120.5:3825
icmp 213.140.240.9:1009 10.2.21.4:1009    95.165.140.5:1009  95.165.140.5:1009
icmp 213.140.240.9:1265 10.2.21.4:1265    95.165.140.5:1265  95.165.140.5:1265
icmp 213.140.240.9:1521 10.2.21.4:1521    95.165.140.5:1521  95.165.140.5:1521
icmp 213.140.240.9:1777 10.2.21.4:1777    95.165.140.5:1777  95.165.140.5:1777
icmp 213.140.240.9:2033 10.2.21.4:2033    95.165.140.5:2033  95.165.140.5:2033
St.Petersburg-R18#
```



[[Наверх]](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_nat/README.md#основные-протоколы-сети-интернет)
### 3. Настроим статический NAT для R20
Добавим на маршрутизатор R20 на интерфейс Loopback0 адрес из подсети офиса Москвы:
```
Moscow-R20#configure terminal 
Moscow-R20(config)#interface Loopback0
Moscow-R20(config-if)#ip address 10.1.200.1 255.255.255.255
Moscow-R20(config-if)#ip ospf 1 area 102
Moscow-R20(config-if)#end
Moscow-R20#wr
```
На R15 cоздадим статическую NAT трансляицю:
```
Moscow-R15(config)#ip nat inside source static 10.1.200.1 213.140.240.2
```
[[Наверх]](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_nat/README.md#основные-протоколы-сети-интернет)
### 4. Настроим NAT так, чтобы R19 был доступен с любого узла для удаленного управления;
Настроим доступ к марщрутизатору R19 по SSH. Добавим интерфейс Loopback1 в протокол внутренней маршрутизации. 
```
Moscow-R19#configure terminal 
Moscow-R19(config)#interface Loopback1          
Moscow-R19(config-if)#ip ospf 1 area 101
Moscow-R19(config-if)#exit
Moscow-R19(config)#ip domain name otus-lab.ru
Moscow-R19(config)#crypto key generate rsa 
The name for the keys will be: Moscow-R19.otus-lab.ru
...
How many bits in the modulus [512]: 1024
...
Moscow-R19(config)#username admin secret otus
Moscow-R19(config)#line vty 0 4
Moscow-R19(config-line)#transport input ssh
Moscow-R19(config-line)#login local 
Moscow-R19(config-line)#exit
Moscow-R19(config)#ip ssh version 2
Moscow-R19(config)#exit
Moscow-R19#wr
```
На маршрутизаторе R14 пропишем команду перенаправления портов:
```
Moscow-R14(config)#ip nat inside source static tcp 10.1.99.19 22 213.140.240.1 22 
```


[[Наверх]](https://github.com/GAFisher/otus-network-engineer/blob/main/homework_nat/README.md#основные-протоколы-сети-интернет)
### 5. Настроим статический NAT(PAT) для офиса Чокурдах

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

### 6. Настроим для IPv4 DHCP сервер в офисе Москва на маршрутизаторах R12 и R13
VPC1 и VPC7 должны получать сетевые настройки по DHCP.
#### R12
```
Moscow-R12#configure terminal 
Moscow-R12(config)#ip dhcp excluded-address 10.1.100.1 10.1.100.3
Moscow-R12(config)#ip dhcp pool IT-VPC1
Moscow-R12(dhcp-config)# network 10.1.100.0 255.255.255.0
Moscow-R12(dhcp-config)# domain-name otus-lab.ru
Moscow-R12(dhcp-config)# default-router 10.1.100.1 
Moscow-R12(dhcp-config)#exit
Moscow-R12(config)#ip dhcp pool DIR-VPC7
Moscow-R12(dhcp-config)# network 10.1.110.0 255.255.255.0
Moscow-R12(dhcp-config)# domain-name otus-lab.ru
Moscow-R12(dhcp-config)# default-router 10.1.110.1
Moscow-R12(dhcp-config)#end 
Moscow-R12#wr
```
#### R12
```
Moscow-R13#configure terminal 
Moscow-R13(config)#ip dhcp excluded-address 10.1.110.1 10.1.110.3
Moscow-R13(config)#ip dhcp pool DIR-VPC7
Moscow-R13(dhcp-config)# network 10.1.110.0 255.255.255.0
Moscow-R13(dhcp-config)# domain-name otus-lab.ru
Moscow-R13(dhcp-config)# default-router 10.1.110.1 
Moscow-R13(dhcp-config)#exit
Moscow-R13(config)#ip dhcp pool IT-VPC1
Moscow-R13(dhcp-config)# network 10.1.100.0 255.255.255.0
Moscow-R13(dhcp-config)# domain-name otus-lab.ru
Moscow-R13(dhcp-config)# default-router 10.1.100.1
Moscow-R13(dhcp-config)#end 
Moscow-R13#wr
```
Проверим:
```
VPC1> ip dhcp -r
DORA IP 10.1.100.4/24 GW 10.1.100.1

VPC1>
```
```
VPC7> ip dhcp -r     
DORA IP 10.1.110.4/24 GW 10.1.110.1

VPC7>
```












