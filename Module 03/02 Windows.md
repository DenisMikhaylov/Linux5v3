Настройка базововго мониторинга Windows

Добавить узел Windows в мониторинг 


Применение к нему шаблона “Windows by Zabbix agent”

```
Host: Gate
  Agent 
    IP: <ip windows>
  Template:
    Windows by Zabbix agent
  Host Group:
    My Host
  Tag:
    Host: Windows
```

Подождать 5 минут. Обновить экран.

Проверка

```
Monitoring->Hosts->Latest data
  Windows
  Tag: Windows
  Zoom: 1h
```
