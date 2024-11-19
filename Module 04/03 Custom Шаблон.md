Простые шаблоны

Создаем шаблон 

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

Добавляем шаблон на линукс машины

Проверяем
