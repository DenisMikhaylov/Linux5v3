Настройка мониторинга без агента

Добавление без агентового узла 

```
Host name: google.com
  interface agent: DNS google.com
  New group: my ext
  Tag:
    host goggle
    my: my
```

```
Host name: ya.ru
  interface agent: DNS ya.ru
  New group: my ext
  Tag:
    host ya
    my: my
```

Добавление агентового узла
```
Host name: Gate
  interface agent: ip <Ip gate>
  New group: my int
  Tag:
    host Gate
    my: my
```
```
Host name: Windows
  interface agent: ip <Ip windows>
  New group: my int
  Tag:
    host Windows
    my: my
```
