---
Практическая работа:
    Название: 'Создание среды для практических работ.'
    Действие: 'Модуль первый'
    Задача: ' Настройка Debian'
---
# **Создание сервисов и сети предприятия**

В данной практической работе мы создадим стенд в рамках, которого будем выполнять наш курс .

Этапы создания стенда:

- Импорт виртуальных машин

- Настройка виртуальных машин и сети

- Развертывания сервисов

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

### **Задача 1: Настройка Виртуальной машины Debian**

Настройка server

Debian предназначен для мониторинга

Настройка системы виртуализации например в Oracle VM VirtualBox

Настрояка сетвых интерфейсов

1. На панели инструментов нажмите кнопку "Настроить".

2. В открывшемся окне слева выберите "Сеть".

3. С правой стороны перейдите на вкладки и настроить выбор согласно списку снизу.:
```
Адаптер 1 - Виртуальный адаптер хоста 
```
4. На каждом адаптере нажмите на надпись "Дополнительно".

5. Нажать на кнопку с двумя синими стрелочками и перегенерировать MAC-адрес

6. Нажмите кнопку "OK".
   
8. На панели инструментов нажмите кнопку "Запустить".

9. Войдите в систему под учетной записью указанной сверху.

10. Запустите терминул.

11.Получите права супер пользователя.
```
student@debian:~$ su -
```
```
< Введите пароль пользователя student >
```

12. Изменить имя компьютера
```
hostnamectl set-hostname  debian.corp.local
```
14. Откройте в текстовом редакторе файл /etc/hosts.

15. Замените вторую строку на
```
127.0.1.1 debian debian.corp.local
```

16. Перезапустить сетевые адаптеры
```
systemctl restart networking
```
17. Проверка IP адресации
```
ip a
ip r
```
18. Проверка связи
```
ping ya.ru
```


Подключаемся по ssh к debian под студентом

1. Получите права супер пользователя.

```
student@debian:~$ su -
```
```
< Введите пароль пользователя student >
```
