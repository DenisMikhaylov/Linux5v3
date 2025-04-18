---
Практическая работа:
    Название: 'Создание среды для практических работ.'
    Действие: 'Модуль первый'
    Задача: 'Настройка DNS'
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


### **Задача 1: Установка DNS**


1. Подключаемся по ssh к серверу Gate под студентом

2. Получите права супер пользователя.

```
student@debian:~$ su -
```
```
< Введите пароль пользователя student >
```

3. Установка приложения
   
```
apt install bind9 -y
```

4.Проверка сервиса применяем только при перенастройки интерфейсов

```
systemctl status bind9
```

5. Настройка сервера перенаправляющего запросы на DNS cервер провайдера

```
nano /etc/bind/named.conf.options
```
```
        forwarders {
                8.8.8.8;
        };
       dnssec-validation no;
       listen-on { 192.168.10.1; 127.0.0.1; };
       listen-on-v6 { ::; };
       

```

6. Проверка конфигурации

```
named-checkconf -z
```
7. Рестарт службы DNS

```
systemctl restart bind9
```
8. Тестирование пересылки

```
nslookup -1=A ya.ru 127.0.0.1
```
9. Настройки DNS сервера
```
nano /etc/resolv.conf
```
10.Заменяем
```
nameserver 127.0.0.1
```

11. Настройка Мастер сервера

```
nano /etc/bind/named.conf.local
```
```
zone "corp.local" {
        type master;
        file "/etc/bind/corp.local";
};
```
12. Создание файла zone
```
nano /etc/bind/corp.local
```
```
$TTL    3h
@    IN  SOA gate   root.gate  20250401 1d 12h  1w  3h
         NS  gate
         A   192.168.10.1
         MX  10  gate
gate     A   192.168.10.1
server   A   192.168.10.10


```

13. Проверка DNS zano

```
# named-checkconf -z
# named-checkzone corp.ru /etc/bind/corp.ru
```
14. Перезапуск слыжбы
```
systemctl restart bind9
```

