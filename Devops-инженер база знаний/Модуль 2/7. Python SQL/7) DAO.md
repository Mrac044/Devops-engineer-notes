Data Access Object или просто DAO - служит для упрощения работы с запросами к БД в коде

Концепция DAO очень простая, она сводится к созданию сущности под каждую таблицу в базе данных. Далее реализуются методы или функции, которые сохраняют, удаляют или ищут сущности в этой таблице. В случае пользователей, наш класс DAO может выглядеть так:

```python
# models.py
from dataclasses import dataclass
from typing import Optional


# так как класс нам нужен лишь для хранения данных, то используем датакласс
@dataclass
class User:
    username: str
    phone: str
    id: Optional[int] = None


# db.py
import psycopg2
from psycopg2.extras import DictCursor


def get_connection():
    return psycopg2.connect(
        dbname="your_database",
        user="your_username",
        password="your_password",
        host="your_host",
        cursor_factory=DictCursor,
    )


def commit(conn):
    conn.commit()


def save_user(conn, user):
    with conn.cursor() as cur:
        if user.id is None:
            cur.execute(
                "INSERT INTO users (username, phone) VALUES (%s, %s) RETURNING id;",
                (user.username, user.phone),
            )
            user.id = cur.fetchone()["id"]
        else:
            cur.execute(
                "UPDATE users SET username = %s, phone = %s WHERE id = %s;",
                (user.username, user.phone, user.id),
            )
    return user


def find_user(conn, user_id):
    with conn.cursor() as cur:
        cur.execute("SELECT * FROM users WHERE id = %s;", (user_id,))
        result = cur.fetchone()
        if result:
            return User(**result)
    return None
```

И пример использования:

```python
from models import User
import db

conn = db.get_connection()


user = User(username="John Doe", phone="1234567890")
user.id  # None

new_user = db.save_user(conn, user)
# делаем коммит после каждого изменения
db.commit(conn)
new_user.id  # тут уже выводится какой-то id

found_user = db.find_user(conn, 42)
db.commit(conn)
found_user  # здесь выводится найденный user

# закрываем соединение
conn.close()
```

## Самостоятельная работа

Выполните шаги из урока на своем компьютере

Перепишите код из урока, используя DAO для таблицы пользователей. Чтобы это сделать, создайте класс User, который будет представлять пользователя

Добавьте в еще одну функцию, которая сможет удалять пользователей из таблицы по их идентификатору

## Дополнительные материалы

- [Паттерн DAO](https://ru.wikipedia.org/wiki/Data_Access_Object)