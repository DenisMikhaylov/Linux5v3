---
Практическая работа:
    Название: 'Шаблоны.'
    Действие: 'Модуль третий'
    Задача: ' Дополнительное элементы'
---
# **Добавлени нод**

В данной практической работе мы добавим Дополнительное элементы.

Этапы создания стенда:

- Добавление дополнительное элементы


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

### **Задача 1: Настройка дополнительное элементы **

1. открыть веб инетерфейс Zabbix

Монитринг через Zabbix Agent

```
Host: Server
  Items 
    Name: Использование процессора
    Type: Zabbix agent
    Key: system.cpu.util[,,avg1]
    Type of information: Numeric (float)
```
```
Host: Server
  Items 
    Name: Использование оперативной памяти
    Type: Zabbix agent
    Key: vm.memory.size[used]
    Type of information: Numeric (float)
```
```
Host: Server
  Items 
    Name: Свободное место на диске
    Type: Zabbix agent
    Key: vfs.fs.size[/,free]
    Type of information: Numeric (float)
```
```
Host: Server
  Items 
    Name: Net ip status
    Type: Zabbix agent
    Key: net.if.status[eth0]
    Type of information: Numeric (float)
```

Использование фильтров:

1.	Перейдите в раздел  Хосты.
2.	С помощью опций фильтрации нужно сузить список элементов по таким критериям, как имя, ключ или статус.


Визуализация данных

```
Monitoring->Hosts->Latest data
  Tag: my-my
  Zoom: 1h
```
