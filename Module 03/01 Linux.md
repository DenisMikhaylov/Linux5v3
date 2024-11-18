Настройка базововго мониторинга Linux

Добавить узел gate в мониторинг 


Применение к нему шаблона “Linux by Zabbix agent”

```
Host: Gate
  Agent 
    IP: <ip gate>
  Template:
    Linux by Zabbix agent
  Host Group:
    My Host
  Tag:
    Host: Gate
```

Подождать 5 минут. Обновить экран.

Проверка

```
Monitoring->Hosts->Latest data
  Gate
  Tag: Gate
  Zoom: 1h
```
