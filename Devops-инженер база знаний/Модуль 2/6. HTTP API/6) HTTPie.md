**утилита командной строки HTTPie** - это мощный инструмент для работы с HTTP-запросами, который создан с акцентом на удобство использования человеком. По сравнению с curl, который часто применяется в скриптах и автоматизации, HTTPie предлагает более интуитивно понятный синтаксис и читабельный вывод. Одним из главных преимуществ HTTPie является его способность удобно работать с JSON данными.

#HTTP-запрос GET-запрос в HTTPie:

```http
http https://http.hexlet.app/http-api/example

HTTP/1.1 200 OK
CF-RAY: 8d692f33af78d365-FRA
Cache-Control: private, max-age=0
Connection: keep-alive
Content-Encoding: gzip
Content-Type: text/html; charset=ISO-8859-1

# Тело
Done!
...
```

#HTTP-запрос Если нам нужно увидеть только тело ответа, можно использовать опцию `-b`

```shell
http -b http.hexlet.app/http-api/example

Done!
```

#HTTP-запрос Если нужно увидеть не только ответ, но и запрос, то понадобится флаг `-v` или `--verbose`. Он покажет все взаимодействие — и запрос и ответ:

```http
http -v https://http.hexlet.app/http-api/example

# Запрос
GET /http-api/example HTTP/1.1
Accept: */*
Accept-Encoding: gzip, deflate
Connection: keep-alive
Host: http.hexlet.app
User-Agent: HTTPie/3.2.1


# Ответ
HTTP/1.1 200 OK
CF-RAY: 8d6965117c4ed619-CDG
Cache-Control: private, max-age=0
Connection: keep-alive
Content-Encoding: gzip
Content-Type: text/html; charset=ISO-8859-1
...

## Тело ответа
Done!
```

### Параметры строки запроса

#HTTP-запрос мы можем передать параметры в виде пар ключ-значение, используя синтаксис `param==value`. HTTPie автоматически распарсит их и сформирует строку запроса:

```http
# Используем опцию -v чтобы увидеть запрос
http -v https://http.hexlet.app/http-api/users skip==3 limit==1

# Из параметров автоматически сформировалась строка запроса
GET /http-api/users?skip=3&limit=1 HTTP/1.1
Accept: */*
Accept-Encoding: gzip, deflate
Connection: keep-alive
Host: http.hexlet.app
User-Agent: HTTPie/3.2.1

# Ответ
...
```

### HTTP метод

Если нужно использовать другой метод, его имя указывается непосредственно перед аргументом URL:

```http
http HEAD http.hexlet.app/http-api/example
```

### Отправка заголовков

HTTPie сам по умолчанию устанавливает некоторые заголовки, ориентируясь на параметры при вызове утилиты:

```http
http -v https://http.hexlet.app/http-api/example

Accept: */*
Accept-Encoding: gzip, deflate
User-Agent: HTTPie/<version>
Host: <taken-from-URL>
```

#HTTP-запрос Чтобы передать собственный заголовок в запросе, используется синтаксис `Header:Value`

```http
http -v https://http.hexlet.app/http-api/example X-Custom-Header:value X-Foo:Bar

GET /http-api/example HTTP/1.1
Accept: */*
Accept-Encoding: gzip, deflate
Connection: keep-alive
Host: http.hexlet.app
User-Agent: HTTPie/3.2.1
## Наши собственные заголовки
X-Custom-Header: value
X-Foo: Bar

# Ответ
...
```

## Указание тела в запросе

#HTTP-запрос Мы можем указать поля с данными в виде пар "ключ-значение" при помощи синтаксиса `key=value`.

```shell
# Можем не указывать метод запроса
http -v https://http.hexlet.app/http-api/users \
   firstName=John \
   lastName=Doe \
   email=john@mail.com \
   password=secret

# Запрос
# Мы передаем тело, поэтому автоматически был выбран метод POST
POST /http-api/users HTTP/1.1
Accept: application/json, */*;q=0.5
Accept-Encoding: gzip, deflate
Connection: keep-alive
Content-Length: 88
Content-Type: application/json
Host: http.hexlet.app
User-Agent: HTTPie/3.2.1

{
    "email": "john@mail.com",
    "firstName": "John",
    "lastName": "Doe",
    "password": "secret"
}
```

==HTTPie автоматически установит тип контента (заголовок _Content-Type_) _application/json_==

#HTTP-запрос Если нам понадобится отправить эти данные в виде формы, нужно будет использовать опцию `-f` или `--form`. Данные будут сериализованы как форма, а тип контента автоматически будет установлен _application/x-www-form-urlencoded_

```shell
http -v -f https://http.hexlet.app/http-api/users \
   firstName=John \
   lastName=Doe \
   email=john@mail.com \
   password=secret

# Запрос
POST /http-api/users HTTP/1.1
Accept: */*
Accept-Encoding: gzip, deflate
Connection: keep-alive
Content-Length: 65
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: http.hexlet.app
User-Agent: HTTPie/3.2.1

firstName=John&lastName=Doe&email=john%40mail.com&password=secret
```

#HTTP-запрос При необходимости можно передать и "сырое" тело. Это делается при помощи опции `--raw`. Данные будут переданы на сервер как есть, без какого-либо преобразования:

```shell
http -v --raw 'Hello!' https://http.hexlet.app/http-api/echo

POST /http-api/echo HTTP/1.1
...

# Тело запроса
Hello!
```

