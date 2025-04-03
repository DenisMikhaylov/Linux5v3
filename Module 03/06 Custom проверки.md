---
Практическая работа:
    Название: 'Шаблоны.'
    Действие: 'Модуль третий'
    Задача: ' Кастомные проверки'
---
# **Добавлени нод**

В данной практической работе мы добавим Кастомные проверки .

Этапы создания стенда:

- Добавление Кастомные проверки


Имена пользователей и пароли:
```
Для denian
login: student 
password: 111

login: root 
password: 111
```
```
Для Windows
login: Student 
password: password
```
### **Практическая работа**

### **Задача 1: Внешние проверки с использованием ExternalScripts **

1. Подключиться по SSH к Server

2. Получить текущий путь для внешних скриптов

```
zabbix_server --help | grep ExternalScripts
```

3. Настройка внешних скриптов

```
nano /etc/zabbix/zabbix_server.conf
```
4. Изменить следующе строки
```
...
Timeout=30
...
ExternalScripts=/etc/zabbix/externalscripts
...
```
5. Создание директории для внешних скриптов

```
mkdir /etc/zabbix/externalscripts
```
6. Создание первого скрипта 
Скрипт для получение среднего значение пинга
```
nano /etc/zabbix/externalscripts/ping_avg.sh
```
```
#!/bin/sh
ping -c"$1" "$2" | tail -n1 | cut -d'/' -f5
```
6.1. Добавление прав на скрипт
```
chmod +x /etc/zabbix/externalscripts/ping_avg.sh
```
6.2. Проверка работы скрипта
```
/etc/zabbix/externalscripts/ping_avg.sh 3 ya.ru
```

6.3. Настройка приминения первого скрипта
```
Cofiguration->Hosts->ya.ru или google.com
  Items
    Name: Ping AVG
    Type: External Check
    Key: ping_avg.sh[3,"{HOST.CONN}"]
    Type of information: Numeric (float)
    Units: ms
```

7. Создание второго скрипта 
Сервис speedtest

7.1. Установка speedtest на Server
```
apt install speedtest-cli
```
7.2. Проверка работы приложения
```
time speedtest-cli

speedtest-cli --csv-header

speedtest-cli --csv
```

7.3. Настройка внешнего скрипта
```
nano /etc/zabbix/externalscripts/speedtest.sh
```
```
#!/bin/sh

if [ "x$1" = xupload ]
then
        A="--no-download"
        F=8
elif [ "x$1" = xdownload ]
then
        A="--no-upload"
        F=7
else
        exit 1
fi

speedtest-cli --csv $A | cut -d',' -f $F
```
7.4. Выдача прав на скрипт
```
chmod +x /etc/zabbix/externalscripts/speedtest.sh
```
7.5. Проверка работы
```
/etc/zabbix/externalscripts/speedtest.sh upload
```

```
/etc/zabbix/externalscripts/speedtest.sh download
```

7.6. Настройка мониторинга
```
Cofiguration->Hosts->Server
  Items
    Name: speedtest download
    Type: External Check
    Key: speedtest.sh[download]
    Type of information: Numeric (float)
    Update interval: 30m

...
Cofiguration->Hosts->server
  Items
    Name: speedtest upload
    Type: External Check
    Key: speedtest.sh[upload]
    Type of information: Numeric (float)
    Update interval: 30m
 
...
```
Скрипт 3

Использование утилиты nmap

Установить на сервер
```
apt install nmap

```
Настройка
```
server# nano /etc/zabbix/externalscripts/detect_host_nmap.sh
```
```
#!/bin/sh
sudo /usr/bin/nmap -O $1 | grep -v 'Starting Nmap\|Host is up\|Nmap done'
```
```
Cofiguration->Hosts->gate
  Items
    Name: Detect host operating system by nmap
    Type: External Check
    Key: detect_host_nmap.sh["{HOST.CONN}"]
    Type of information: Text
```
Настройка мониторинга DHCP

Переключиться на gate

Проверить что возвращает скрипт
```
gate# dhcpd-pools -l /var/lib/dhcp/dhcpd.leases -c /etc/dhcp/dhcpd.conf
```
Создание скрипта
```
gate# nano /usr/local/bin/dhcp_stat.sh
```
```
#!/bin/sh

CMD='/usr/bin/dhcpd-pools -l /var/lib/dhcp/dhcpd.leases -c /etc/dhcp/dhcpd.conf -f c | grep 192.168.'
MAX=`eval $CMD | cut -d'"' -f8`
CUR=`eval $CMD | cut -d'"' -f10`

eval RES=\$$1

echo $RES
```
Проверка 
```
gate# /usr/local/bin/dhcp_stat.sh MAX

gate# /usr/local/bin/dhcp_stat.sh CUR
```
Переключиться на server

```
server:~$ ssh root@gate /usr/local/bin/dhcp_stat.sh CUR

server:~$ ssh root@gate /usr/local/bin/dhcp_stat.sh MAX
```
Создание скрипта для мониторинга
```
server# nano /etc/zabbix/externalscripts/dhcp_stat_ext.sh
```

```
#!/bin/sh
ssh root@$1 /usr/local/bin/dhcp_stat.sh $2
```
Проверка скрипта
```
server:~$ /etc/zabbix/externalscripts/dhcp_stat_ext.sh gate CUR
```
```
gate->Items
  Name: DHCP stat CUR
  Type: External check
  Key: dhcp_stat_ext.sh["{HOST.CONN}",CUR]
```

Создание проверок по средствам trapper

Создание элемента
```
server->Items
  Name: my item
    Type: Zabbix trapper
    Key:  my.item
    Allowed hosts: 127.0.0.1, 192.168.10.0/24
```
Установка 
```
# apt install zabbix-sender
```
```
$ zabbix_sender -z <ip server> -p 10051 -s server -k my.item -o 1
```

Перенастройка speedtest
```
server->Items
  Name: speedtest download trap
    Type: Zabbix trapper
    Key:  speedtest.download
    Type of information: Numeric (float) или Numeric (unsigned)
    Allowed hosts: 127.0.0.1
...
  Name: speedtest upload trap
    Type: Zabbix trapper
    Key:  speedtest.upload
    Type of information: Numeric (float) или Numeric (unsigned)
    Allowed hosts: 127.0.0.1
...
```
Настройка скрипта
```
server# nano /root/speedtest.sh
```
```
#!/bin/sh

### speedtest-cli ### result bits/s
MY_RES=`speedtest-cli --csv`
MY_DOWNLOAD=`echo $MY_RES | cut -d',' -f7`
MY_UPLOAD=`echo $MY_RES | cut -d',' -f8`

### speedtest ### result Bytes/s (use preprocess Custom multiplier)
#MY_RES=`speedtest -f csv`
#MY_DOWNLOAD=`echo $MY_RES | cut -d',' -f6`
#Y_UPLOAD=`echo $MY_RES | cut -d',' -f7`

zabbix_sender -z 127.0.0.1 -p 10051 -s server.corpX.un -k speedtest.download -o $MY_DOWNLOAD
zabbix_sender -z 127.0.0.1 -p 10051 -s server.corpX.un -k speedtest.upload -o $MY_UPLOAD
```
```
# crontab -l
...
X * * * * /root/speedtest.sh >/dev/null 2>&1
```

Создание проверок по средствам UserParameter

Переключаемся на gate

```
# nano /etc/zabbix/zabbix_agentd.d/my.linux.disk.discovery.conf
```
```
UserParameter=my.disks.discovery,/bin/lsblk -dJ | /bin/sed -e 's/blockdevices/data/' -e 's/name/{#NAME}/g' -e 's/type/{#TYPE}/g'
```
Проверяем работу нового параметра , переключаемся на zabbix

```
# zabbix_get -s ip gate -k my.disks.discovery | jq
```
Настройка DHCP на Gate
```
gate# nano /etc/zabbix/zabbix_agentd.d/dhcp_stat.conf
```

```
UserParameter=dhcp.stat[*],/usr/local/bin/dhcp_stat.sh $1
```
Тестирование 
```
server# zabbix_get -s gate -k dhcp.stat[CUR]
server# zabbix_get -s gate -k dhcp.stat[MAX]
```
Настройа мониторинга установленного ПО
Пеерключаемся на Server

```
server# nano /etc/zabbix/zabbix_agentd.conf.d/listinstalledsoft.conf
```
```
UserParameter=listinstalledsoft,ls /usr/share/applications | awk -F '.desktop' ' { print $1}' -
```

Вычисляемые элементы
```
ya.ru->Items
  Name: avg perf http
    Type: Calculated
    Key:  my.avg.perf.http
    Formula: avg(net.tcp.service.perf[https],5m)
    Type of information: Numeric (float)
```

```
gate->Items
  Name: DHCP stat CUR
    Type: Zabbix agent
    Key: dhcp.stat[CUR]

  Name: DHCP stat MAX
    Type: Zabbix agent
    Key: dhcp.stat[MAX]
    
  Name: DHCP stat CUR MAX percent
    Type: Calculated
    Key:  DHCP.stat.CUR.MAX.percent
    Formula: last(dhcp.stat[CUR])/last(dhcp.stat[MAX])*100
```

Задание 5. Создание зависимых элементов

Шаг 1. Создание мастер элемента

На Gate

```
apt install nginx
```
```
chmod o+r /var/log/nginx/access.log
```
```
Host: gate

  Items
    Name: HTTP Access Log
    Type: agent zabbix active
    Key: log[/var/log/nginx/access.log,"^.*",,,skip,\0,,,]
    Type of Information:	Log

Шаг 2. Создание дочерних элементов
Host: gate

  Items
    Name: Status Code
    Type: Dependent Item
    Key: HTTPStatusCode
    Type of Information:	Numeric (unsigned)
    Master item: HTTP Access Log
Preprocessing:
Regular Expression:
^(\S+) (\S+) (\S+) \[([\w:\/]+\s[+\-]\d{4})\] \"(\S+)\s?(\S+)?\s?(\S+)?\" (\d{3}|-) (\d+|-)\s?\"?([^\"]*)\"?\s?\"?([^\"]*)\"
Output: \8
Type : Numeric (unsigned)


  Items
    Name: Path
    Type: Dependent Item
    Key: HTTPPath
    Type of Information:	text
    Master item: HTTP Access Log
Preprocessing:
Regular Expression:
^(\S+) (\S+) (\S+) \[([\w:\/]+\s[+\-]\d{4})\] \"(\S+)\s?(\S+)?\s?(\S+)?\" (\d{3}|-) (\d+|-)\s?\"?([^\"]*)\"?\s?\"?([^\"]*)\"
Output: \6
Type : text
```

