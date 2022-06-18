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




