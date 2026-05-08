**Мидлвара** - это функции, которые обрабатывают запросы и ответы в веб-приложениях. Они выполняются между получением запроса от клиента и отправкой ответа. Они могут выполнять различные задачи, такие как проверка прав доступа, логирование или преобразование данных в ответе

## Оборачивание

Представим, что у нас есть функция, которая прибавляет 5 к аргументу:

```python
def add_five(num):
    return num + 5
```

Например, мы хотим прибавлять 5 к удвоенному значению аргумента. Чтобы решить эту задачу, мы можем написать новую функцию, которая будет внутри себя использовать уже существующую функцию:

```python
def double_add_five(x):
    return add_five(x * 2)
```

Точно так же мы можем расширить и вторую функцию:

```python
def calculate(x):
    return double_add_five(x - 10) - 3
```

В общем случае расширение функции выглядит так:

```python
def next_method(args):
    # preprocessing
    result = prev_method(updated_args)
    # afterprocessing
    return new_result
```

Главное условие — интерфейсы, то есть сигнатуры, обеих функций должны совпадать. Код, использующий новую функцию, не заметит, что он обернут и его не потребуется переписывать. Этот подход также называется декорированием.

По похожей идее работают мидлвары. Они расширяют функционал обработчиков запросов, оборачивая их в подобные методы. При этом оригинальный обработчик остается неизменным.

## Использование мидлвар

Для добавления мидлвар используются декораторы `@before_request` или `@after_request`. Декоратор `@before_request` используется для указания кода, который должен быть выполнен до запроса, а `@after_request` — после.

Рассмотрим пример кода с подключением мидлвары:

```python
from flask import Flask, request

app = Flask(__name__)


@app.before_request
def log_path():
    print(f"Request path: {request.path}")


@app.route("/")
def home():
    return "Hello from middleware!"


@app.after_request
def log_response(response):
    print("Response has been sent")
    return response
```

==Во Flask в мидлваре `@after_request` обязательно нужно вернуть объект `response`.==

![[Pasted image 20260402233247.png]]

Flask по умолчанию выполняет все `before` мидлвары по порядку, как они заданы в коде. Но если какая-то из них возвращает значение, то на ней закончится вызов, и вернется `response`.

Если же ни одна из `before` мидлвар не совершила `return`, то выполнение дойдет до обработчика маршрута.

Затем, в конце, выполнится `after` мидлвара.

```python
from flask import Flask, request

app = Flask(__name__)


@app.before_request
def log_path():
    print(f"Request path: {request.path}")


@app.after_request
def add_custom_header(response):
    response.headers["X-Custom-Header"] = "value"
    return response


@app.route("/")
def home():
    return "Welcome to Hexlet!"
```

В примере выше мы подключили мидлвары, одна из которых выводит в консоль сообщение, а другая добавляет кастомный заголовок _X-Custom-Header_ в каждый ответ.

Запустим сервер и сделаем запрос:

```sh
curl --head localhost:5000

HTTP/1.1 200 OK
Server: Werkzeug/3.0.6 Python/3.13.0rc2
Content-Type: text/html; charset=utf-8
Content-Length: 18
X-Custom-Header: value # Наш установленный заголовок
Connection: close
```

При этом в консоли, где запущено приложение, мы можем увидеть вывод:

```sh
Request path: /
```

## Обработчики

Самое интересное в мидлварах, что обработчики конкретных маршрутов — это тоже мидлвары. Но в отличие от мидлвар, которые выполняются для всех запросов, обработчики привязаны к конкретному маршруту.

```python
@app.before_request
def middleware():
    print("1. First middleware")


@app.route("/")
def home():
    print("2. Route handler")
    return "Hello"


@app.after_request
def middleware3(response):
    print("3. After middleware")
    return response
```

## Терминальная мидлвара

`return` в мидлваре после возврата движение по цепочке прекратится, все оставшиеся мидлвары пропускаются и происходит отправка ответа:

```python
from flask import Flask, request

app = Flask(__name__)


@app.before_request
def check_id():
    if request.endpoint == "resource":
        id = request.args.get("id")
        if not id:
            # в случае запроса на /resouce сработает условие мидлвары
            return 'Bad Request: Missing "id" parameter', 400
    return None  # иначе возвращаем None, чтобы продолжить цепочку


@app.before_request
def log_path():
    print(f"Request path: {request.path}")


@app.route("/")
def home():
    return "Hello from Hexlet"


@app.route("/resource")
def resource():
    id = request.args.get("id")
    return f"Resource with id: {id}"
```

У такого поведения, когда есть цепочка функций и любая из них в процессе обработки может принять решение остановки цепочки и возврата ответа, есть имя. Такие цепочки называют [chain responsibility](https://ru.wikipedia.org/wiki/%D0%A6%D0%B5%D0%BF%D0%BE%D1%87%D0%BA%D0%B0_%D0%BE%D0%B1%D1%8F%D0%B7%D0%B0%D0%BD%D0%BD%D0%BE%D1%81%D1%82%D0%B5%D0%B9), и это тоже паттерн

Помимо этого существует мидлвары выполнямые после отправки ответа - `teardown_request()`. Эта мидлвара выполнится даже в случае, если во время выполнения программы сгенерируется и не будет обработано исключение.

```python
from flask import Flask

app = Flask(__name__)


@app.teardown_request
def run_always(exception):
    print("This will always run")


@app.route("/")
def home():
    raise Exception("Something went wrong")
```

```sh
curl -Is localhost:5000/
HTTP/1.1 500 INTERNAL SERVER ERROR
Server: Werkzeug/3.0.6 Python/3.13.0rc2
Date: Fri, 21 Feb 2025 14:40:19 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 13849
Connection: close
```

В консоли, где запущено приложение, мы можем увидеть, что сообщение из мидлвары все равно выведется, и лишь затем будет стектрейс ошибки:

```sh
This will always run

Traceback (most recent call last):
  line 1498, in __call__
    return self.wsgi_app(environ, start_response)
```

## Дополнительные материалы

- [before_request](https://flask.palletsprojects.com/en/stable/api/#flask.Flask.before_request)
    
- [after_request](https://flask.palletsprojects.com/en/stable/api/#flask.Flask.after_request)