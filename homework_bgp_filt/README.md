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





