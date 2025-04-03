---
Практическая работа:
    Название: 'Шаблоны.'
    Действие: 'Модуль третий'
    Задача: ' Шаблоны Linux'
---
# **Добавлени нод**

В данной практической работе мы добавим шаблоны Linux .

Этапы создания стенда:

- Добавление шаблонов Linux


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

### **Задача 1: Настройка подключения агента **
1. открыть веб инетерфейс Zabbix


Применение  шаблона “Linux by Zabbix agent”

```
Host: Gate
  Template:
    Linux by Zabbix agent
 ```

Подождать 5 минут. Обновить экран.

Проверка

```
Monitoring->Hosts->Latest data
  Gate
  Tag: Gate
  Zoom: 1h
```
