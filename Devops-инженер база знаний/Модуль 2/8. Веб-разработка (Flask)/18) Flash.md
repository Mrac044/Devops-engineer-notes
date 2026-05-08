вы могли видеть сообщение о результатах выполнения любого действия: аутентификация, регистрация или вступление в курс

В веб-разработке такое сообщение называют Flash. В этом уроке мы научимся работать с ним.

## Одноразовые сообщения

**Flash** обычно используется после перенаправления для индикации успешности или неудачи предыдущего действия.

Этот механизм работает благодаря кукам и встроен во Flask.

Чтобы работать с Flash, сначала подключим необходимый модуль:

```python
from flask import flash
```

Теперь можем использовать Flash:

```python
from flask import Flask, flash, url_for, redirect, get_flashed_messages, render_template

app = Flask(__name__)
# устанавливаем секретный ключ для работы с сессиями
app.secret_key = "secret_key"


@app.post("/foo")
def foo_post():
    # Добавление флеш-сообщения.
    # Оно станет доступным только на следующий HTTP-запрос.
    # 'success' — тип флеш-сообщения. Используется при выводе для форматирования.
    # Например, можно ввести тип success и отображать его зеленым цветом. На Хекслете такого много.
    flash("This is a message", "success")
    return redirect("/bar")


@app.get("/bar")
def bar_index():
    # Извлечение flash-сообщений, которые установлены на предыдущем запросе
    messages = get_flashed_messages(with_categories=True)
    print(messages)  # => [('success', 'This is a message')]
    return render_template(
        "bar.html",
        messages=messages,
    )
```
```html
<!-- templates/bar.html -->
{% if messages %}
  <ul class=flashes>
  {% for category, message in messages %}
    <li class="{{ category }}">{{ message }}</li>
  {% endfor %}
  </ul>
{% endif %}
```

Функция `flash()` добавляет сообщение в общее хранилище сообщений. Функция `get_flashed_messages()` извлекает все сообщения из этого хранилища. Дальше Flash-сообщения передаются в шаблон и там выводятся.

==!! После извлечения хранилище обнуляется. При следующем запросе `get_flashed_messages()` вернет пустой массив.==

Во многих фреймворках типы Flash-сообщений стандартизированы. В основном используют следующие:

- _success_ — для удачно завершившихся действий
- _warning_ — для предупреждений
- _error_ — в тех ситуациях, когда произошла ошибка

Можно добавить сразу много Flash-сообщений, поэтому `get_flashed_messages()` возвращает следующую структуру:

```python
flash("first message", "success")
flash("second message", "success")
flash("first message", "error")
flash("another message", "error")

get_flashed_messages()
# [
#   ('success', 'first message'),
#   ('success', 'second message'),
#   ('error', 'first message'),
#   ('error', 'another message'),
# ]
```

Под капотом у флеш-сообщений используются сессии, которые рассматриваются в курсе чуть позже. Благодаря этому механизму сайт запоминает конкретного пользователя и его сообщение.

## Самостоятельная работа

1. Добавьте реализацию вывода Flash-сообщений при создании пользователя, используя примеры из теории
![[Pasted image 20260505004203.png]]