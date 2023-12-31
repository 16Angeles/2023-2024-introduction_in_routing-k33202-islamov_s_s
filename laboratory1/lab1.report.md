Year: 2023/2024

Group: K33202

Author: Islamov Sergey

Lab: Laboratory1

Отчёт по лабораторной работе №1 "Установка ContainerLab и развертывание тестовой сети связи"

Цель работы: ознакомиться с инструментом ContainerLab и методами работы с ним, изучить работу VLAN, IP адресации и т. д.

Ход работы:

1) Содержимое файла yaml для развертывания виртуальной сети можно посмотреть в lab1.yaml
2) Схема связи в файле network.png
3) Тексты конфигурация для сетевых устройств 

* Роутер
```

/interface vlan
add interface=ether2 name=vlan10 vlan-id=10
add interface=ether2 name=vlan20 vlan-id=20
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/ip pool
add name=pool10 ranges=192.168.10.10-192.168.10.254
add name=pool20 ranges=192.168.20.10-192.168.20.254
/ip dhcp-server
add address-pool=pool10 disabled=no interface=vlan10 name=dhcp10
add address-pool=pool20 disabled=no interface=vlan20 name=dhcp20
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
add address=192.168.10.1/24 interface=vlan10 network=192.168.10.0
add address=192.168.20.1/24 interface=vlan20 network=192.168.20.0
/ip dhcp-client
add disabled=no interface=ether1
/ip dhcp-server network
add address=192.168.10.0/24 gateway=192.168.10.1
add address=192.168.20.0/24 gateway=192.168.20.1
/system identity
set name=R01.TEST
```

* Свитч 1 уровня
```

/interface bridge
add name=bridge10
add name=bridge20
/interface vlan
add interface=ether2 name=vlan10 vlan-id=10
add interface=ether2 name=vlan20 vlan-id=20
add interface=ether3 name=vlan100 vlan-id=10
add interface=ether4 name=vlan200 vlan-id=20
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/interface bridge port
add bridge=bridge10 interface=vlan10
add bridge=bridge20 interface=vlan20
add bridge=bridge10 interface=vlan100
add bridge=bridge20 interface=vlan200
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
/ip dhcp-client
add disabled=no interface=ether1
add disabled=no interface=bridge10
add disabled=no interface=bridge20
/system identity
set name=SW01.L3.01.TEST
```
* Первый свитч 2 уровня
```

/interface bridge
add name=bridge10
/interface vlan
add interface=ether2 name=vlan10 vlan-id=10
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/interface bridge port
add bridge=bridge10 interface=vlan10
add bridge=bridge10 interface=ether3
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
/ip dhcp-client
add disabled=no interface=ether1
add disabled=no interface=bridge10
/system identity
set name=SW02.L3.01.TEST
```

* Второй свитч 2 уровня
```

/interface bridge
add name=bridge20
/interface vlan
add interface=ether2 name=vlan20 vlan-id=20
/interface wireless security-profiles
set [ find default=yes ] supplicant-identity=MikroTik
/interface bridge port
add bridge=bridge20 interface=vlan20
add bridge=bridge20 interface=ether3
/ip address
add address=172.31.255.30/30 interface=ether1 network=172.31.255.28
/ip dhcp-client
add disabled=no interface=ether1
add disabled=no interface=bridge20
/system identity
set name=SW02.L3.02.TEST
```

4) Результаты пингов для проверки целостности локальной сети
![](https://github.com/16Angeles/2023-2024-introduction_in_routing-k33202-islamov_s_s/blob/54804b8b10e5ecc3bb224269b6570267676e47f1/Ping1.png "Проверка локальной сети и пингов с роутера")

![](https://github.com/16Angeles/2023-2024-introduction_in_routing-k33202-islamov_s_s/blob/54804b8b10e5ecc3bb224269b6570267676e47f1/Ping2.png "Проверка пингов со свитча 2 уровня")

**Вывод:**
В ходе данной лабораторной работы были освоены методы работы с ContainerLab, была изучена работа VLAN, IP адресация, был установлен докер и проведена работа по освоению докера через документацию


