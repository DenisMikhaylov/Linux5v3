---
Практическая работа:
    Название: 'Шаблоны.'
    Действие: 'Модуль третий'
    Задача: ' Создание шаблона'
---
# **Разработка своего шаблона**

В данной практической работе мы разработаем свой первый шаблон.

Этапы создания стенда:

- Создание шаблона для SSH


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

### **Задача 1: Создание шаблона**

1. Открыть веб интерфейс Zabbix

2. Создаем шаблон 

```
  Templates->Create template
  Templates Name: Template App SSH Port Service SSH Port Service
  Groups: My Template
      Items
          Name: SSH service is running {HOST.NAME}
          Key: net.tcp.service[ssh,,{$SSH_PORT}]
          Update interval: 30s
      Macros
        {$SSH_PORT}=22
```

3. Добавляем шаблон на линукс машины

4. Проверяем
