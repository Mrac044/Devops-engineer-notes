__Psql__ -- это интерактивная консольная утилита, которая позволяет взаимодействовать с базой данных через командную строку.

#Shell Чтобы подключиться к базе данных с помощью psql, необходимо выполнить команду:

```shell
psql -h server_address -U username -d dbname
```
*Где _server_address_ - адрес сервера СУБД, username - это ваше имя пользователя, а dbname - название базы данных*

#Shell После выполнения этой команды вам будет предложено ввести пароль.
```shell
psql -h 65.108.223.44 -d coursesdb -U student
Password for user student:
# вводим пароль student
```

#Shell Посмотрим, какие таблицы есть в этой БД:

```sql
psql (14.11 (Ubuntu 14.11-0ubuntu0.22.04.1), server 12.17)
Type "help" for help.

coursesdb=> \dt
             List of relations
 Schema |      Name      | Type  |  Owner
--------+----------------+-------+----------
 public | course_members | table | postgres
 public | course_reviews | table | postgres
 public | courses        | table | postgres
 public | topics         | table | postgres
 public | users          | table | postgres
(5 rows)

coursesdb=>
```

#Shell `\dt` - это специальная команда в _psql_. С ее помощью мы вывели список всех таблиц в этой базе данных. Список всех доступных команд можно посмотреть с помощью справки #Shell `\?`.

## Работа с SQL
#Shell Сделаем запрос с помощью _psql_:

```sql
coursesdb=> SELECT id, first_name, last_name FROM users ORDER BY first_name ASC LIMIT 5;
 id | first_name | last_name
----+------------+-----------
 16 | Abe        | Funk
  8 | Abigale    | Turner
 34 | Alejandrin | Nicolas
 13 | Alfreda    | Hermann
 33 | Alfredo    | Sipes
(5 rows)

coursesdb=>
```

==Если запрос можно вводить не целиком, а построчно, то утилита будет ожидать ввода точки с запятой для завершения построения запроса.==

```sql
coursesdb=> SELECT
coursesdb->     id,
coursesdb->     first_name,
coursesdb->     last_name
coursesdb-> FROM users
coursesdb-> ORDER BY first_name
coursesdb-> LIMIT 5;
 id | first_name | last_name
----+------------+-----------
 16 | Abe        | Funk
  8 | Abigale    | Turner
 34 | Alejandrin | Nicolas
 13 | Alfreda    | Hermann
 33 | Alfredo    | Sipes
(5 rows)

coursesdb=>
```

## Клиент DBeaver
DBeaver -- бесплатный универсальный клиент для работы с различными СУБД. Его основные преимущества -- распространенный, бесплатный и доступен на множестве платформ. Установить его можно по [инструкции](https://github.com/Hexlet/ru-instructions/blob/main/dbeaver.md)

