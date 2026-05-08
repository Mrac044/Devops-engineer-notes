## Request и response

И запрос и ответ во Flask представлены двумя объектами - `request` и `response`, обратиться к которым можно внутри каждого обработчика маршрута:

```python
from flask import request


@app.route("/")
def hello_world():
    return "Hello, World!"
```

Запрос уже выполнили со стороны клиента, и он не может поменяться:

```python
from flask import request


@app.route("/")
def hello_world():
    print(request.headers)  # Выводит все заголовки
    return "Hello, World!"
```

```sh
curl locahost:5000/
Hello, World!

# в терминале запущенного приложения
Host: localhost:5000
User-Agent: curl/7.81.0
Accept: */*

127.0.0.1 - - "GET / HTTP/1.1" 200 -
```

Для ответа же используется объект `response`.

```python
from flask import render_template


@app.route("/json/")
def json():
    return {"json": 42}  # Возвращает тип application/json


@app.route("/html/")
def html():
    return "<h1>Hello, world!</h1>"  # Возвращает тип text/html
```

Если вернуть вместе с телом в виде кортежа-пары еще и число, то клиент получит соответствующий код ответа:

```python
@app.route("/not_found")
def not_found():
    return "Oops!", 404
```

```sh
curl -IS localhost:5000/not_found

HTTP/1.1 404 NOT FOUND
Server: Werkzeug/3.0.6 Python/3.13.0rc2
Content-Type: text/html; charset=utf-8
Content-Length: 5
Connection: close
```

что обработчики ошибок получают в качестве аргумента объект ошибки `error`. Вот так можно было бы оформить «Страницу 404»:

```python
# вместо пути указываем код ошибки
@app.errorhandler(404)
def not_found(error):
    return "Oops!", 404
```

функцию `make_response()` Она принимает тело ответа и возвращает объект ответа - `flask.Response`. Объект ответа можно модифицировать разными способами и затем вернуть из обработчика:

```python
from flask import make_response


@app.route("/hello")
def hello():
    # создаем объект response
    response = make_response("Hello, World!")
    # Устанавливаем заголовок
    response.headers["X-MyHeader"] = "Thats my header!"
    # Меняем тип ответа
    response.mimetype = "text/plain"
    # Задаем статус
    response.status_code = 201
    # Устанавливаем cookie
    response.set_cookie("super-cookie", "42")
    return response
```

В примере выше мы создали объект ответа с телом `'Hello, World!'`, затем установили в нем наш заголовок, изменили тип ответа, задали куку и статус ответа. Наконец, собранный ответ вернули из обработчика.

```sh
curl -i localhost:5000/hello

HTTP/1.1 201 CREATED
Server: Werkzeug/3.0.6 Python/3.13.0rc2
Content-Type: text/plain; charset=utf-8
Content-Length: 13
X-MyHeader: Thats my header!
Set-Cookie: super-cookie=42; Path=/
Connection: close

Hello, World!
```

## Параметры запроса

Параметры запроса, или еще их называют `query string` — это такие параметры используются для различных вспомогательных целей. Например, параметр `page`, обозначает страницу просматриваемого списка.

Обработчик в такой ситуации всегда один и тот же, а данные показываются разные:

```sh
curl 'localhost:8000/users?page=4&per=3'

GET /users
```

Параметры извлекаются из объекта запроса. По атрибуту `request.args` возвращается объект, который похож на словарь, несколькими способами:

- `request.args` – извлекает все параметры
- `request.args.get(name, default=None, type=None)` – извлекает значение конкретного параметра в формате строки. Если параметр не найден, приложение продолжает работу, а метод вернет `default`. С помощью `type` можно привести параметр к определенному типу. Если привести к указанному типу невозможно, возникнет исключение `ValueError`

```python
@app.route("/users/")
def get_users():
    print(request.args)  # => {'page': 12, 'per': 5}
    page = request.args.get("page", 1)
    per = request.args.get("per", 10, type=int)
    # Обработка
    prev_page = (page - 1) * per
    current_page = page * per
    users_at_page = users[prev_page:current_page]
    return users_at_page
```

```sh
# равноценно запросу GET localhost/users?page=12&per=5 в браузере
curl -G -d page=12 -d per=5 localhost/users
```

==Пейджинг — механизм, который позволяет итерироваться по большим коллекциям небольшими порциями. Часто встречается в Интернете, например, в результатах запросов поисковых систем. Пейджинг с точки зрения пользователя выглядит как параметры запроса: _page_ определяет текущую страницу, а _per_ — количество элементов на страницу. Имена могут быть и другими, но обычно их называют, как показано выше. Запрос c _page_, равным _1_, аналогичен запросу без указания _page_.==

Количество обработчиков и маршрутов, которые можно добавить, ничем не ограничено. При этом микрофреймворки не задают никакой структуры. Если кода становится много, то разделять его по файлам придется самостоятельно.

## Самостоятельная работа

1. Измените в _example.py_ следующий обработчик:

```python
@app.post('/users')
def users():
    return 'Users', 302
```

2. Выполните POST-запрос на него с помощью _curl_. Убедитесь, что произошел редирект:

```sh
# Подставьте ваши параметры
# --head – покажет заголовки
# -X POST – отправит POST запрос
curl --head -X POST localhost:8000/users
```

[Эталонное приложение](https://github.com/hexlet-components/python-flask-example)

## Дополнительные материалы

- [Request](https://flask.palletsprojects.com/quickstart/#accessing-request-data)
    
- [Response](https://flask.palletsprojects.com/quickstart/#about-responses)