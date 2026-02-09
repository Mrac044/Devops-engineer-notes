**SQL (Structured Query Language)** — это язык, который помогает управлять данными в СУБД.

SQL позволяет:

- Управлять данными внутри таблицы — добавлять их, редактировать, выбирать.
- Менять структуру самих таблиц и баз данных.

У SQL есть собственный стандарт, который реализуют на конкретные базы данных, поэтому все реляционные БД работают, примерно одинаково

## Создание базы данных

Чтобы начать работать с базой данных, ее нужно создать. Чтобы это сделать, нужно установить СУБД на специальный компьютер — **сервер**.

Сама по себе СУБД может не иметь графического интерфейса, в таком случае мы используем **запросы** на языке SQL.

#SQL-команда Создание БД:

```sql
CREATE DATABASE hexlet_db;
```

## Как создать таблицу
- #SQL-команда Создание таблицы:

```sql
CREATE TABLE users (
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(255),
    created_at TIMESTAMP
);
```

SQL — это **декларативный язык программирования**. Он описывает, что нужно получить, не объясняя, как именно нужно прийти к такому результату (не нужно писать алгоритм решения задачи).

- #SQL-команда Добавление записей в таблицу:

```sql
INSERT INTO users
(first_name,  last_name, email, created_at)
VALUES ('Lucienne', 'Feil',  'Trevion53@yahoo.com', '2022-06-14 18:31:05.296');

INSERT INTO users
(first_name,  last_name, email, created_at)
VALUES ('Ramiro', 'Wolf',  'Baylee52@yahoo.com', '2022-06-14 02:04:13.104');

INSERT INTO users
(first_name,  last_name, email, created_at)
VALUES ('Maureen', 'Romaguera',  'Casimer_Cronin@yahoo.com', '2022-06-14 02:28:26.058');
```

Вот такая таблица у нас получится:

|first_name|last_name|email|created_at|
|---|---|---|---|
|Lucienne|Feil|[Trevion53@yahoo.com](mailto:Trevion53@yahoo.com)|2022-06-14 18:31<br><br>.296|
|Ramiro|Wolf|[Baylee52@yahoo.com](mailto:Baylee52@yahoo.com)|2022-06-14 02:04<br><br>.104|
|Maureen|Romaguera|[Casimer_Cronin@yahoo.com](mailto:Casimer_Cronin@yahoo.com)|2022-06-14 02:28<br><br>.058|
- #SQL-команда Напишем запрос на редактирование записи:

```
UPDATE users SET email = 'Casimer_Cronin@gmail.com'
WHERE last_name = 'Romaguera';
```

- #SQL-команда Пример извлечения информации из таблицы:

```sql
SELECT * FROM users;
```

Этот запрос вернет все строки в таблице:

|first_name|last_name|email|created_at|
|---|---|---|---|
|Lucienne|Feil|[Trevion53@yahoo.com](mailto:Trevion53@yahoo.com)|2022-06-14 18:31<br><br>.296|
|Ramiro|Wolf|[Baylee52@yahoo.com](mailto:Baylee52@yahoo.com)|2022-06-14 02:04<br><br>.104|
|Maureen|Romaguera|[Casimer_Cronin@gmail.com](mailto:Casimer_Cronin@gmail.com)|2022-06-14 02:28<br><br>.058|
## Как удалить запись
- #SQL-команда Давайте удалим нашего пользователя из таблицы:

```sql
DELETE FROM users WHERE last_name = 'Romaguera';
```

И снова выведем всю таблицу:

```sql
SELECT * FROM users;
```

Получилась такая таблица:

|first_name|last_name|email|created_at|
|---|---|---|---|
|Lucienne|Feil|[Trevion53@yahoo.com](mailto:Trevion53@yahoo.com)|2022-06-14 18:31<br><br>.296|
|Ramiro|Wolf|[Baylee52@yahoo.com](mailto:Baylee52@yahoo.com)|2022-06-14 02:04<br><br>.104|
## Как выбрать запись
- #SQL-команда Например, выберем пользователей с именем Ramiro:

```sql
SELECT * FROM users WHERE first_name = 'Ramiro';
```

|first_name|last_name|email|created_at|
|---|---|---|---|
|Ramiro|Wolf|[Baylee52@yahoo.com](mailto:Baylee52@yahoo.com)|2022-06-14T02:04<br><br>.104Z|
## Удаление таблиц
- #SQL-команда Чтобы удалить таблицу, выполняется запрос `DROP`:

```sql
DROP TABLE users;
```
*Будьте внимательны, так как удаление таблицы приводит к безвозвратной потере данных.*

## Комментарии в запросах

В SQL существует два основных вида комментариев: 

- Однострочные (начинаются с двух дефисов --)
#SQL-команда Пример запроса с комментарием
```sql
-- Работаем с таблицей пользователей
SELECT * FROM users; -- Получаем всех пользователей
```

- Многострочные (между символами `/*` и `*/`)
#SQL-команда Пример запроса с многострочным комментарием
```sql
/*
Добавление нового пользователя
Данный запрос добавляет информацию о пользователе в таблицу пользователей
*/
INSERT INTO users
    (first_name, last_name, email, created_at)
VALUES ('Lucienne', 'Feil', 'Trevion53@yahoo.com', '2022-06-14 18:31:05.296');
```
