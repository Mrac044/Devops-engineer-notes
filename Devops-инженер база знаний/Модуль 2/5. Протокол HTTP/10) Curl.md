**Утилита curl** — мощным инструментом для выполнения HTTP-запросов. Эта утилита командной строки позволяет управлять различными параметрами запросов с помощью флагов

>*Одним из главных преимуществ* curl является то, что нам не нужно заранее устанавливать соединение и вручную формировать сырой запрос. Вместо этого мы можем просто определить необходимые параметры, и curl автоматически отправит все нужные заголовки.

⚡️ В состав некоторых операционных систем curl уже включена по умолчанию. Если у вас ее нет, то установите ее по нашей [инструкции](https://github.com/Hexlet/ru-instructions/blob/main/curl.md)

#Shell Самый простой способ использовать curl — это выполнить GET-запрос к какому-то сайту используя полный адрес:

```shell
curl http.hexlet.app/http-protocol/example

# Тело ответа
Done!
```

Его можно сохранить в файл, выполнив перенаправление:

```shell
curl http.hexlet.app/http-protocol/example > example.html
```

#Shell Если мы хотим в curl посмотреть заголовки ответа, то запрос станет таким:

```shell
# --head - запрос с методом HEAD
curl --head http.hexlet.app/http-protocol/example

HTTP/1.1 200 OK
Date: Tue, 22 Oct 2024 07:54:48 GMT
Content-Type: text/html; charset=ISO-8859-1
Connection: keep-alive
...
```

#Shell Если в curl нужно увидеть не только ответ, но и запрос, то подойдет флаг _-v_ или его полная версия _--verbose_. Он покажет все, включая тело ответа:

```shell
# Вывод сокращен
# -v, --verbose
curl -v http.hexlet.app/http-protocol/example
*   Trying 172.67.139.244:80...
* Connected to http.hexlet.app (172.67.139.244) port 80 (#0)
> GET /http-protocol/example HTTP/1.1
> Host: http.hexlet.app
> User-Agent: curl/7.81.0
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Date: Tue, 22 Oct 2024 07:59:39 GMT
< Content-Type: text/html; charset=ISO-8859-1
< Transfer-Encoding: chunked
< Connection: keep-alive
...

# Тело
Done!
```

#Shell При работе с curl мы можем указывать различные заголовки запроса, чтобы передать дополнительную информацию на сервер. Для этого используется флаг `-H` или `--header`.

Например, если мы хотим указать, что данные, которые мы отправляем, имеют формат JSON, мы можем использовать следующую команду:

```shell
# -H, --header
curl -H "Content-Type: application/json" https://http.hexlet.app/http-protocol/users
```

#Shell Когда в curl мы отправляем данные на сервер, например, при создании нового пользователя, мы можем использовать флаг `-d` для указания тела запроса:

```shell
# \ - нужен для указания многострочного кода в терминале
curl -X POST https://http.hexlet.app/http-protocol/users \
   -H "Content-Type: application/json" \
   -d '{ "firstName": "Sam", "lastName": "Johnson", "email": "samj@mail.com", "password": "secret" }'
```

## Самостоятельная работа

1. Установите curl в вашу систему. Сделать это можно по нашей [инструкции](https://github.com/Hexlet/ru-instructions/blob/main/curl.md)
    
2. Выполните шаги из урока
    
3. С помощью curl создайте новую задачу.
    
    Для этого вам понадобится отправить _POST_ запрос на адрес _[https://http.hexlet.app/http-protocol/tasks](https://http.hexlet.app/http-protocol/tasks)_ с телом в формате JSON. Пример тела запроса для создания задачи:
    
    ```
    {
    "title": "Some title",
    "description": "Some description",
    "status": "Done"
    }
    ```


```
curl -X POST https://http.hexlet.app/http-protocol/tasks \
-H "content-type: application/json" \
-d '{
"title": "Some title",
"description": "Some description",
"status": "Done"
}'
```

## Дополнительные материалы

- [Статья про Curl на Habr](https://habr.com/ru/articles/508592/)
    
- [Инструкция по установке Curl](https://github.com/Hexlet/ru-instructions/blob/main/curl.md)