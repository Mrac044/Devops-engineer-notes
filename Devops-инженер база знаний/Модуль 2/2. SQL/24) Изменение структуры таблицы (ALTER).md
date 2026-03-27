Если посмотреть официальную документацию, то общая запись этого запроса занимает больше трех экранов монитора. Но ни один человек не помнит всех возможностей этой команды. Более того, большинство из них редко выполняются. Единственное, что нужно знать -- возможности этой команды -- общие принципы ее работы. А детали всегда можно будет найти в документации.

**`ALTER TABLE`** - отвечает за изменение таблицы базы данных.

## Запрос ALTER TABLE

Запрос `ALTER TABLE` используют, чтобы изменять структуру столбца таблицы базы данных. Он включает четыре операции:

- Добавление колонки
- Переименование колонки
- Удаление колонки
- Обновление колонки

### Добавление колонки

С помощью `ALTER` можно добавить колонку в уже имеющуюся таблицу:

![[Pasted image 20260207234312.png]]

Делается это таким образом:

```sql
CREATE TABLE users (
    id BIGINT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    username VARCHAR(50),
    email VARCHAR(255) UNIQUE NOT NULL,
    created_at TIMESTAMP
);

INSERT INTO users (created_at, email, username) VALUES ('2022-06-14 18:31:05.296', 'Trevion53@yahoo.com', 'Duncan3');
INSERT INTO users (created_at, email, username) VALUES ('2022-06-14 02:04:13.104', 'Baylee52@yahoo.com', 'Michaela11');
INSERT INTO users (created_at, email, username) VALUES ('2022-06-14 02:28:26.058', 'Casimer_Cronin@yahoo.com', 'Margarete_Hegmann6');

-- в таблицу "users"
-- добавить колонку с именем "birthday" и типом "date"
ALTER TABLE users ADD COLUMN birthday DATE;
```
 #SQL-команда простом варианте `ALTER TABLE` она выглядит так: `<имя колонки> <тип>`.
[View on DB Fiddle](https://www.db-fiddle.com/f/pLcXFSKjZnoK1tAX6c6Xnf/0)

### Переименование колонки

#SQL-команда Чтобы переименовать колонку, нужно сделать следующий запрос:

```sql
-- добавляем колонку, которой еще нет.
ALTER TABLE users ADD COLUMN name VARCHAR(255);
-- в таблице "users"
-- изменить колонку "name":
-- поменять имя с "name" на "first_name"
ALTER TABLE users RENAME COLUMN name TO first_name;
```

[View on DB Fiddle](https://www.db-fiddle.com/f/pLcXFSKjZnoK1tAX6c6Xnf/2)

### Удаление колонки

#SQL-команда Следующий запрос удаляет колонку:

```sql
ALTER TABLE users ADD COLUMN age DATE;
-- в таблице "users"
-- удалить колонку с именем "age"
ALTER TABLE users DROP COLUMN age;
```

[View on DB Fiddle](https://www.db-fiddle.com/f/w4xTqQ2fNm8izDidvgar8v/2)

### Обновление колонки

#SQL-команда Команда по изменению параметров колонки наиболее сложная. 

Практически у каждого элемента, который поддается обновлению, есть собственный синтаксис для этого обновления. Вот несколько базовых примеров:

```sql
CREATE TABLE courses (
    id bigint PRIMARY KEY,
    name varchar(255) NOT NULL,
    body text,
    created_at timestamp
);

-- Установка ограничения уникальности в таблице courses для колонки name
ALTER TABLE courses
ADD UNIQUE (name);

-- Изменение типа данных в таблице courses для колонки created_at
-- и снятие ограничения NOT NULL в таблице courses для колонки name
ALTER TABLE courses
ALTER COLUMN created_at SET DATA TYPE DATE,
ALTER COLUMN name DROP NOT NULL;

-- Установка ограничения NOT NULL в таблицу courses для колонки name
ALTER TABLE courses
ALTER COLUMN name SET NOT NULL;
```
Наиболее распространенные команды:

- `ADD` -- добавление ограничения: например, ключа или уникальности
- `SET` -- установка значения: например, типа данных
- `DROP` -- удаление ограничения
[View on DB Fiddle](https://www.db-fiddle.com/f/w4xTqQ2fNm8izDidvgar8v/4)

==В рамках одного обновления можно группировать операции, но существует ряд исключений. Например, группировке не поддается операция `RENAME` -- ее нужно выполнять отдельным запросом, иначе СУБД завершит запрос с ошибкой.==

## Дополнительные материалы

- [Официальная документация](https://postgrespro.ru/docs/postgresql/15/ddl-alter)
    
- [Рефакторинг баз данных. Эволюционное проектирование](https://www.ozon.ru/context/detail/id/3261793/)
