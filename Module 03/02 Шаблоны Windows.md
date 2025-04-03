---
Практическая работа:
    Название: 'Шаблоны.'
    Действие: 'Модуль третий'
    Задача: ' Шаблоны Windows'
---
# **Добавлени нод**

В данной практической работе мы добавим шаблоны windows .

Этапы создания стенда:

- Добавление шаблонов Windows


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

### **Задача 1: Добавление шаблонов Windows **

1. открыть веб инетерфейс Zabbix

Применение шаблона “Windows by Zabbix agent”

```
Host: Gate
  
  Template:
    Windows by Zabbix agent

```

Подождать 5 минут. Обновить экран.

Проверка

```
Monitoring->Hosts->Latest data
  Windows
  Tag: Windows
  Zoom: 1h
```
