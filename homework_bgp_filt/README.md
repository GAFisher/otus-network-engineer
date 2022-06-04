# BGP. Фильтрация
## Задание:
1. Настроить фильтрацию для офиса Москва
2. Настроить фильтрацию для офиса С.-Петербург
## Решение:
1. Настроить фильтрацию в офисе Москва так, чтобы не появилось транзитного трафика(As-path);
2. Настроить фильтрацию в офисе С.-Петербург так, чтобы не появилось транзитного трафика(Prefix-list);
3. Настроить провайдера Киторн так, чтобы в офис Москва отдавался только маршрут по умолчанию;
4. Настроить провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по умолчанию и префикс офиса С.-Петербург.



### 1. Настроим фильтрацию в офисе Москва так, чтобы не появилось транзитного трафика 
С помощью As-path ACL разрешим анонсирование локальных маршрутов и запретим анонсы маршрутов из из других AS:
#### R14
```
Moscow-R14#configure terminal
Moscow-R14(config)#ip as-path access-list 1 permit ^$
Moscow-R14(config)#ip as-path access-list 1 deny .*
Moscow-R14(config)#router bgp 1001
Moscow-R14(config-router)#address-family ipv4
Moscow-R14(config-router-af)#neighbor 84.52.118.225 filter-list 1 out
Moscow-R14(config-router-af)#exit
Moscow-R14(config-router)#address-family ipv6
Moscow-R14(config-router-af)#neighbor 2606:4700:D0:C009::225 filter-list 1 out
Moscow-R14(config-router-af)#end
Moscow-R14#wr
```
#### R15
```
Moscow-R15#configure terminal
Moscow-R15(config)#ip as-path access-list 1 permit ^$
Moscow-R15(config)#ip as-path access-list 1 deny .*
Moscow-R15(config)#router bgp 1001
Moscow-R15(config-router)#address-family ipv4
Moscow-R15(config-router-af)#neighbor 78.25.80.89 filter-list 1 out 
Moscow-R15(config-router-af)#exit
Moscow-R15(config-router)#address-family ipv6
Moscow-R15(config-router-af)#neighbor 1A00:4700:D0:C005::89 filter-list 1 out          
Moscow-R15(config-router-af)#end
Moscow-R15#wr
```
### 2. Настроим фильтрацию в офисе С.-Петербург так, чтобы не появилось транзитного трафика
```
St.Petersburg-R18#configure terminal
St.Petersburg-R18(config)#ip prefix-list LIST_OUT1 seq 5 permit 95.165.120.0/30 
St.Petersburg-R18(config)#ip prefix-list LIST_OUT1 seq 10 permit 95.165.140.0/30
St.Petersburg-R18(config)#ip prefix-list LIST_OUT1 seq 15 deny 0.0.0.0/0 le 32 
St.Petersburg-R18(config)#ipv6 prefix-list LIST_OUT2 seq 5 permit 2001:20DA:EDA:3::/64
St.Petersburg-R18(config)#ipv6 prefix-list LIST_OUT2 seq 10 permit 2001:20DA:EDA:7::/64
St.Petersburg-R18(config)#ipv6 prefix-list LIST_OUT2 seq 15 deny deny ::/0 le 128
St.Petersburg-R18(config)#router bgp 2042
St.Petersburg-R18(config-router)#address-family ipv4
St.Petersburg-R18(config-router-af)#neighbor 95.165.120.5 prefix-list LIST_OUT1 out
St.Petersburg-R18(config-router-af)#neighbor 95.165.140.5 prefix-list LIST_OUT1 out
St.Petersburg-R18(config-router-af)#exit
St.Petersburg-R18(config-router)#address-family ipv6
St.Petersburg-R18(config-router-af)#neighbor 2001:20DA:EDA:3::5 prefix-list LIST_OUT2 out
St.Petersburg-R18(config-router-af)#neighbor 2001:20DA:EDA:7::5 prefix-list LIST_OUT2 out
St.Petersburg-R18(config-router-af)#end
St.Petersburg-R18#wr
```

### 3. Настроим провайдера Киторн так, чтобы в офис Москва отдавался только маршрут по умолчанию
Настроим передачу Default Route нижестоящему маршрутизатору (R14) и с помощью prefix-list укажем, чтобы R22 передавал только маршрут по умолчанию и ничего лишнего:
```
Kirton-R22#configure terminal
Kirton-R22(config)#ip prefix-list DEFAULT seq 5 permit 0.0.0.0/0 
Kirton-R22(config)#ipv6 prefix-list DEFAULT seq 5 permit ::/0 
Kirton-R22(config)#router bgp 101
Kirton-R22(config-router)#address-family ipv4 
Kirton-R22(config-router-af)#neighbor 84.52.118.226 default-originate 
Kirton-R22(config-router-af)#neighbor 84.52.118.226 prefix-list DEFAULT Out 
Kirton-R22(config-router)#address-family ipv6
Kirton-R22(config-router-af)#neighbor 2606:4700:D0:C009::226 default-originate
Kirton-R22(config-router-af)#neighbor 2606:4700:D0:C009::226 prefix-list DEFAULT Out
Kirton-R22(config-router-af)#end
Kirton-R22#wr
```

### 4. Настроиим провайдера Ламас так, чтобы в офис Москва отдавался только маршрут по умолчанию и префикс офиса С.-Петербург
```
Lamas-R21#configure terminal
Lamas-R21(config)#ip prefix-list DEFAULT seq 5 permit 95.165.120.4/30 
Lamas-R21(config)#ip prefix-list DEFAULT seq 10 permit 95.165.140.4/30 
Lamas-R21(config)#ip prefix-list DEFAULT seq 15 permit 0.0.0.0/0 
Lamas-R21(config)#ipv6 prefix-list DEFAULT seq 5 permit 2001:20DA:EDA:3::/64
Lamas-R21(config)#ipv6 prefix-list DEFAULT seq 10 permit 2001:20DA:EDA:7::/64
Lamas-R21(config)#ipv6 prefix-list DEFAULT seq 15 permit ::/0
Lamas-R21(config)#router bgp 301
Lamas-R21(config-router)#address-family ipv4 
Lamas-R21(config-router-af)#neighbor 78.25.80.90 default-originate 
Lamas-R21(config-router-af)#neighbor 78.25.80.90 prefix-list DEFAULT Out
Lamas-R21(config-router-af)#exit
Lamas-R21(config-router)#address-family ipv6
Lamas-R21(config-router-af)#neighbor 1A00:4700:D0:C005::90 default-originate 
Lamas-R21(config-router-af)#neighbor 1A00:4700:D0:C005::90 prefix-list DEFAULT OutLamas-R21(config-router-af)#end
Lamas-R21#wr
```



