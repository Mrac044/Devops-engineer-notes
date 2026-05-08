Чтобы начать хранить и извлекать данные из базы, нам нужно выполнить несколько действий:

- Установить зависимости, необходимые для работы с базой данных
- Настроить подключение к базе данных и дать к нему доступ из приложения
- Создать начальную структуру базы данных с нужными таблицами
- Переписать методы репозиториев так, чтобы они работали с данными через базу

В этом уроке мы проделаем все эти шаги на примере создания части CRUD для сущности `Car` с полями `manufacturer` (марка) и `model` (модель).

## Устанавливаем зависимости

Мы будем использовать базу данных PostgreSQL. Чтобы начать работать, вам потребуется установить библиотеку [psycopg](https://www.psycopg.org/docs/index.html). Более детальную работу с psycopg мы будем рассматривать дальше, но пока вы можете изучить нашу [статью](https://ru.hexlet.io/blog/posts/python-postgresql).

## Настраиваем подключение

Рассмотрим такой пример

```python
import psycopg2

conn = psycopg2.connect(DATABASE_URL)
```

В примере выше мы создаем соединение с базой данных. Соединение потребуется нам при создании репозитория, так как вся работа с базой будет сосредоточена там.

## Строим начальную структуру базы данных

В нашем случае база данных создается при старте приложения, поэтому ее инициализацию мы будем делать там же, во время старта. Для этого создадим файл с нужной схемой данных:

1. Добавляем файл _init.sql_:
    
    ```sql
    CREATE TABLE IF NOT EXISTS cars (
        id SERIAL PRIMARY KEY,
        manufacturer VARCHAR(255) NOT NULL,
        model VARCHAR(255) NOT NULL
    );
    ```
    
2. Загружаем схему в базу. Postgres позволяет подключиться к удаленной базе указав ссылку на нее после флага `-d`.
    

Для сборки приложения мы можем написать баш-скрипт `build.sh` вида:

```bash
#!/usr/bin/env bash

psql -a -d $DATABASE_URL -f init.sql
```

Дальше сделаем скрипт исполняемым (`chmod +x ./build.sh`) и добавим такую команду в Makefile проекта:

```Makefile
build:
    ./build.sh
```

Теперь мы можем указать эту команду на любом сервисе как команду сборки. Также мы отделяем сборку от кода, что дает больше свободы в конфигурации, и следует принципам [12 факторов](https://ru.hexlet.io/courses/production-basics/lessons/12-factors/theory_unit).

## Создаем репозиторий CarRepository

В репозитории нам понадобится соединение с базой данных, так как вся работа с базой будет сосредоточена тут. Соединение будет передаваться в конструктор при создании объекта репозитория.

```python
import psycopg2
from psycopg2.extras import DictCursor


class CarRepository:
    def __init__(self, conn):
        self.conn = conn

    def get_content(self):
        with self.conn.cursor(cursor_factory=DictCursor) as cur:
            cur.execute("SELECT * FROM cars")
            return [dict(row) for row in cur]

    def find(self, id):
        with self.conn.cursor(cursor_factory=DictCursor) as cur:
            cur.execute("SELECT * FROM cars WHERE id = %s", (id,))
            row = cur.fetchone()
            return dict(row) if row else None

    def get_by_term(self, search_term=""):
        with self.conn.cursor(cursor_factory=DictCursor) as cur:
            cur.execute(
                """
                    SELECT * FROM cars
                    WHERE manufacturer ILIKE %s OR model ILIKE %s
                """,
                (f"%{search_term}%", f"%{search_term}%"),
            )
            return cur.fetchall()

    def save(self, car):
        if "id" in car and car["id"]:
            self._update(car)
        else:
            self._create(car)

    def _update(self, car):
        with self.conn.cursor() as cur:
            cur.execute(
                "UPDATE cars SET manufacturer = %s, model = %s WHERE id = %s",
                (car["manufacturer"], car["model"], car["id"]),
            )
        self.conn.commit()

    def _create(self, car):
        with self.conn.cursor() as cur:
            cur.execute(
                "INSERT INTO cars (manufacturer, model) VALUES (%s, %s) RETURNING id",
                (car["manufacturer"], car["model"]),
            )
            id = cur.fetchone()[0]
            car["id"] = id
        self.conn.commit()
```

Принцип создания всех методов для работы с базой данных одинаковый:

- Описываем шаблон запроса
- Формируем стейтмент
- Делаем подстановки
- Выполняем запрос
- Собираем результат
- Возвращаем ответ

## Рассматриваем примеры операций

Как видно на примере ниже, правильная организация абстракций и разделение по слоям приводят к тому, что изменение внутренностей не оказывает особого влияния на строение приложения. Контроллеры работают с репозиторием через его методы и ничего не знают про его внутреннюю реализацию:

```python
from validator import validate

conn = psycopg2.connect(DATABASE_URL)
repo = CarRepository(conn)


@app.route("/cars")
def cars_index():
    term = request.args.get("term", "")
    if term:
        cars = repo.get_by_term(term)
    else:
        cars = repo.get_content()
    return render_template("cars/index.html", search=term, cars=cars)


@app.route("/cars/<int:id>")
def cars_show(id):
    car = repo.find(id)
    if car is None:
        abort(404)
    return render_template("cars/show.html", car=car)


@app.route("/cars/new")
def cars_new():
    return render_template("cars/new.html", car={}, errors={})


@app.route("/cars", methods=["POST"])
def cars_post():
    data = request.form.to_dict()

    errors = validate(data)

    if not errors:
        car = {"manufacturer": data["manufacturer"], "model": data["model"]}
        repo.save(car)
        flash("Car was added successfully", "success")
        return redirect(url_for("cars_index"))

    return render_template("cars/new.html", car=data, errors=errors), 422
```

Также в примере выше мы добавили фильтрацию в GET-форме по маршруту _/cars_. #Реальная_работа В реальных приложениях операции выборки, фильтрации, агрегации осуществляются внутри базы с помощью запросов. Современные базы данных выполняют такие операции гораздо быстрее и эффективнее, чем обработка средствами языка.

## Самостоятельная работа

1. Переделайте способ хранения пользователей на хранение в БД. Проверьте себя: в результате перехода от хранения в сессии до хранения в БД код приложения измениться не должен. Все что поменяется - код абстракции Репозиторий.
2. Уберите лишние контролеры и шаблоны. Оставьте только все, связанное с `users`

[Эталонный репозиторий](https://github.com/hexlet-components/python-flask-example/tree/database)

## Дополнительные материалы

- [Подключение к Postgres](https://ru.hexlet.io/courses/sql-basics/lessons/psql/theory_unit)
    
- [Psycopg](https://www.psycopg.org/docs/usage.html)