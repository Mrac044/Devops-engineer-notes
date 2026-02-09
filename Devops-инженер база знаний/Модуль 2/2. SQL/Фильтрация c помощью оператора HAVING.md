Для примера представим, что нам нужно вывести суммарное время, проведенное на платформе каждым пользователем:

```
SELECT
  user_id,
  SUM(spent_minutes)
FROM course_reviews
GROUP BY user_id
ORDER BY user_id;
```

|user_id|sum|
|---|---|
|1|69|
|2|60|
|3|9|
|4|68|
|9|77|
|16|27|

[View on DB Fiddle](https://www.db-fiddle.com/f/jS4o1UbtrLKTPo3ce328b7/8)
Подобные условия невозможно задать с помощью `WHERE`, потому что они применяются к выборке до момента группировки. У нас немного другой случай — мы хотим задать условие на результат агрегатной функции после проведения группировки.

## Ключевое слово `HAVING`

#SQL-команда С помощью **`HAVING`** опробуем найти пользователей, которые потратили менее 30 минут в онлайн-школе. Для этого напишем такой запрос:

```sql
SELECT
    user_id,
    SUM(spent_minutes)
FROM course_reviews
GROUP BY user_id
HAVING SUM(spent_minutes) < 30
ORDER BY user_id;
```

|user_id|sum|
|---|---|
|3|9|
|16|27|
|23|4|
|67|27|
|75|27|
|78|13|

[View on DB Fiddle](https://www.db-fiddle.com/f/w4TJXkf68vdaR1YccWAYHt/0)

## Чем `HAVING` отличается от `WHERE`

#SQL-команда Давайте дополним наш запрос — укажем, что искать пользователей мы будем только среди первых 40 человек. Для этого добавим условие `user_id <= 40`:

```sql
SELECT
    user_id,
    SUM(spent_minutes)
FROM course_reviews
WHERE user_id <= 40
GROUP BY user_id
HAVING SUM(spent_minutes) < 30
ORDER BY user_id;
```

|user_id|sum|
|---|---|
|3|9|
|16|27|
|23|4|

[View on DB Fiddle](https://www.db-fiddle.com/f/w4TJXkf68vdaR1YccWAYHt/1)
Условия в `HAVING` проверяются после группировки, поэтому они задаются после предложения `GROUP BY`.

В этой ситуации мы могли бы добавить условие на `user_id` и в `HAVING` и получили бы тот же результат:

```sql
SELECT
    user_id,
    SUM(spent_minutes)
FROM course_reviews
GROUP BY user_id
HAVING SUM(spent_minutes) < 30 AND user_id <= 40
ORDER BY user_id;
```

|user_id|sum|
|---|---|
|3|9|
|16|27|
|23|4|
==Такой запрос будет выполняться дольше, особенно если таблицы будут большими. Лучше сперва исключить ненужных пользователей, сгруппировать уже отфильтрованные данные и затем посчитать агрегатные функции.==
[View on DB Fiddle](https://www.db-fiddle.com/f/w4TJXkf68vdaR1YccWAYHt/2)


## Дополнительные материалы

- [HAVING](https://www.postgresql.org/docs/current/queries-table-expressions.html#QUERIES-GROUP)
    

Завершено

20 / 25

Ключевое слово HAVINGЧем HAVING отличается от WHEREВыводыДополнительные материалы

