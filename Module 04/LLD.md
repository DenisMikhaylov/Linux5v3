Переключаемся на gate
Настройка мониторинга дисков по средствам lld
Создаем шаблон 

```
Templates->Create template
  Template name: My Template Linux disks utilization
  Groups: Templates/Server hardware

  Discovery rules->
    Name: my disks discovery
    Key: my.disks.discovery
    Filters->
      {#TYPE} matches disk
    Item prototypes->
      Name: disk {#NAME} read bytes
      Key: vfs.dev.read[{#NAME},sps]
      Type of information: Numeric (float)
      Units: bite
      Preprocessing->
        Preprocessing steps
        Custom multiplier 512

      Name: disk {#NAME} write bytes
      Key: vfs.dev.write[{#NAME},sps]
      Type of information: Numeric (float)
      Units: bite
      Preprocessing->
        Preprocessing steps
        Custom multiplier 512
```

Создание Статистика DHCP сервера

Переходим на Gate 

Устанавливаем приложение
```
apt install jq
apt install xmlstarlet
```

Редактируем файл настройки DHCP

```
nano /etc/dhcp/dhcpd.conf
```
Находим блок с subnet
```
добавлем 
shared-network LAN1 { 

  subnet 192.168.10.0 netmask 255.255.255.0 {
    range 192.168.10.101 192.168.10.109;
    option routers 192.168.10.1;
  }

}
shared-network LAN2 { 

  subnet 192.168.30.0 netmask 255.255.255.0 {
    range 192.168.30.101 192.168.30.109;
    option routers 192.168.30.1;
  }

} 
```

Создаем скрипты

```
# nano /etc/zabbix/dhcp-pools-discovery.sh
```
```
#!/bin/bash

echo -n '{"data":['

str=`/usr/bin/dhcpd-pools -c /etc/dhcp/dhcpd.conf -f x | \
/usr/bin/xmlstarlet sel -T -t -m '//shared-network' \
-o '{"{#POOLNAME}":"' -v location -o '"},'`

echo -n ${str::-1}

echo -n ']}'
```
Разрешем выполняться скрипту 
```
chmod +x /etc/zabbix/dhcp-pools-discovery.sh
```

Проверяем работу

```
# /etc/zabbix/dhcp-pools-discovery.sh | jq
```

Создание еще одного скрипта

```
# nano /etc/zabbix/dhcp-pools-shared-network.sh
```

```
#!/bin/sh

res_field=2
test "x$2" = "xused" && res_field=3

/usr/bin/dhcpd-pools -c /etc/dhcp/dhcpd.conf -f x | \
  /usr/bin/xmlstarlet sel -T -t -m '//shared-network' \
  -v location -o ' ' -v defined -o ' ' -v used -n | \
  grep $1 | cut -d ' ' -f $res_field
```
Разрешем выполняться скрипту 
```
chmod +x /etc/zabbix/dhcp-pools-shared-network.sh
```

тестирование скрипта

```
# /etc/zabbix/dhcp-pools-shared-network.sh LAN1 defined

# /etc/zabbix/dhcp-pools-shared-network.sh LAN1 used
```

Создаем  на Gate новый файл для агента.
```
# nano  /etc/zabbix/zabbix_agentd.d/dhcp_stat.conf
```
```
UserParameter=dhcp.pools.discovery,/etc/zabbix/dhcp-pools-discovery.sh

UserParameter=dhcp.pools.shared-network[*],/etc/zabbix/dhcp-pools-shared-network.sh $1 $2
```
Создаем настройки 

```
Templates->Create template
  Template name: Template App DHCP Pools
  Groups In groups: Templates/Applications

  Macros: {$DHCP.POOLS.MAX.PERCENT}=90
Add


  Discovery rules
    Name: Search DHCP Pools
    Type: Zabbix Agent
    Key: dhcp.pools.discovery
  Add
    Item prototypes
      Name: DHCP Pool {#POOLNAME} max addr
      Type: Zabbix Agent
      Key: dhcp.pools.shared-network[{#POOLNAME},defined]
      
    Add

      Name: DHCP Pool {#POOLNAME} cur addr
      Type: Zabbix Agent
      Key: dhcp.pools.shared-network[{#POOLNAME},used]
      
    Add

    Graph prototypes
      Name: DHCP Pool {#POOLNAME} max cur
      Y axis MIN value: Fixed 0
      Items: 
        Template App DHCP Pools: DHCP Pool {#POOLNAME} cur addr
        Template App DHCP Pools: DHCP Pool {#POOLNAME} max addr

    Trigger prototypes

      Name: On {HOST.NAME} in the DHCP pool {#POOLNAME}
     
      Expression:
                  last(/Template App DHCP Pools/dhcp.pools.shared-network[{#POOLNAME},used])/last(/Template App DHCP Pools/dhcp.pools.shared-network[{#POOLNAME},defined])*100 > {$DHCP.POOLS.MAX.PERCENT}
      Severity: Warning
