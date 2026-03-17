Обычно HTTP API построен по следующим правилам:

- Данные передаются в формате JSON
- Для каждого набора данных используется свой URL

Это далеко не единственный способ организации HTTP API, но один из самых распространенных.

Для примера возьмем сервис [HTTP Server](https://http.hexlet.app/http-api-openapi), созданный специально для экспериментов с API. Он включает в себя ненастоящие данные, с которыми можно попрактиковаться.

В документации сервиса описываются **ресурсы** — сущности, информацию о которых мы можем получать по API. Ниже их неполный список:

|URL|Описание|
|---|---|
|/posts|Список постов|
|/comments|Список комментариев к постам|
|/users|Список пользователей|
|/tasks|Список задач|

Каждая ссылка, по которой мы получаем какие-то данные в HTTP API, называется **эндпоинтом**.

Для получения списка пользователей нужно загрузить [https://http.hexlet.app/http-api/users](https://http.hexlet.app/http-api/users). Ее можно открыть даже в браузере. В ответ вернется такой текст:

```json
{
    "users": [
        {
            "id": "1",
            "email": "max@hotmail.com",
            "firstName": "Allison",
            "lastName": "Bernier"
        },
        {
            "id": "2",
            "email": "Colt97@yahoo.com",
            "firstName": "Hudson",
            "lastName": "Schowalter"
        }
        {
            ...
        }
    ],
    "total": 10,
    "skip": 0,
    "limit": 30
}
```

Для получения списка пользователей нужно загрузить [https://http.hexlet.app/http-api/users](https://http.hexlet.app/http-api/users). Ее можно открыть даже в браузере. В ответ вернется такой текст:

```json
{
    "users": [
        {
            "id": "1",
            "email": "max@hotmail.com",
            "firstName": "Allison",
            "lastName": "Bernier"
        },
        {
            "id": "2",
            "email": "Colt97@yahoo.com",
            "firstName": "Hudson",
            "lastName": "Schowalter"
        }
        {
            ...
        }
    ],
    "total": 10,
    "skip": 0,
    "limit": 30
}
```

Формат, в котором данные передаются, называется **JSON**. Давайте остановимся на этом подробнее.

**Формат** — это способ описания данных. С ним можно работать двумя способами:

- упаковать данные — то есть сериализовать их
- извлечь данные — десериализовать их

==Задача сериализации и десериализации возникает тогда, когда нам нужно передать данные из программы наружу — например, другим программам.==

Lfля передачи данных и используются универсальные форматы, которые известны всем.

В случае HTTP API этот механизм работает так:

- Сервис, который предоставляет HTTP API, извлекает данные из хранилища, формирует JSON и отдает его наружу
- Затем этот JSON может прочитать любая программа с поддержкой JSON (а это большинство современных программ)

Пример выше может выглядеть и так:

```json
{"users":[{"id": "1","email": "max@hotmail.com","firstName": "Allison","lastName": "Bernier"},{"id": "2","email": "Colt97@yahoo.com","firstName": "Hudson","lastName": "Schowalter"}],"total": 10,"skip": 0,"limit": 100}
```

Разберемся, чем это отличается от передачи данных в HTML.

HTML — обычно служит для передачи данных в браузеры, так как это язык разметки, с помощью которого формируется текст для браузеров. Браузеры считывают HTML и отображают его в виде веб-страницы. HTML не подразумевает работу с данными, которые содержатся внутри него.

JSON — это не единственный формат данных. До него популярным форматом был XML, и сейчас он встречается довольно часто. Так выглядит XML-файл:

```xml
<?xml version="1.0"?>
<Recipe>
   <Name>Lime Jello Marshmallow Cottage Cheese Surprise</Name>
   <Description>
     My grandma's favorite (may she rest in peace).
   </Description>
   <Ingredients>
      <Ingredient>
         <Qty unit="box">1</Qty>
         <Item>lime gelatin</Item>
      </Ingredient>
      <Ingredient>
         <Qty unit="g">500</Qty>
         <Item>multicolored tiny marshmallows</Item>
      </Ingredient>
   </Ingredients>
   <Instructions>
      <Step>
         Prepare lime gelatin according to package instructions
      </Step>
      <!-- And so on... -->
   </Instructions>
</Recipe>
```

XML похож на HTML, но решает другую задачу. XML — это формат данных, как и JSON. Разница лишь в том, что XML не предназначен для вывода.

## Структура JSON

Данные в формате JSON хранятся внутри объектов. **Объект** — это часть данных, ограниченная фигурными скобками, внутри которых задаются ключи и их значения:

```json
{ "id": 3, "hasBranches": true, "name": "Hexlet", "country": "Finland" }
```

Ключи в JSON всегда обернуты кавычками. В качестве значений могут выступать числа, булевы значения, строки и `null`:

- `1`, `3`, `2.5`
- `true`, `false`
- `"one"`, `"two"`

Также значениями могут быть массивы:

```json
{ "courses": ["php", "ruby", "python"] }
```

Причем весь JSON может быть только массивом:

```json
["one", "two", "three"]
```

Объекты могут быть вложенными в другие объекты:

```json
{ "id": 3, "hasBranches": true, "name": "Hexlet", "address": { "country": "Finland", "city": "Helsinki" } }
```

А еще объекты можно вложить в массивы:

```json
{ "courses": [{ "id": 1, "name": "php" }, { "id": 2, "name": "javascript" }] }
```

## Метаданные

Если посмотреть на структуру ответа _/users_, то становится видно, что список пользователей передается как массив внутри объекта с дополнительными параметрами:

```json
{
    "users": [],
    "total": 100,
    "skip": 0,
    "limit": 30
}
```

Зачем так сделано? Почему бы сразу не отдавать массив пользователей?

```json
[{ ... }, { ... }]
```

Ответ здесь очень простой. Часто нужно передавать не только данные, но и **метаданные** — то есть данные о данных. Например, к метаданным относится общее количество пользователей.

## Пагинация

Иногда данных слишком много JSON с таким объемом данных получится огромным и тяжелым.

**Пагинация** — с ней данные отдаются не целиком, а небольшими наборами.

В API, с которым мы работаем, по умолчанию отдается 30 результатов. Это видно из возвращаемого JSON:

```json
{
    "users": [...],
    "total": 10,
    "skip": 0,
    "limit": 30
}
```

Как в таком случае получить вторые 30 человек? Нужно добавить параметр _skip_: [https://http.hexlet.app/http-api/users?skip=30](https://http.hexlet.app/http-api/users?skip=30). Так будет выглядеть JSON:

```json
{
    "users": [],
    "total": 10,
    "skip": 30,
    "limit": 30
}
```

## Ограничение данных

Представим, что нам нужны не все данные, а только их часть. Для этого в нашем HTTP API есть параметр запроса _select_: [https://http.hexlet.app/http-api/users?select=firstName,email](https://http.hexlet.app/http-api/users?select=firstName,email). Так он выглядит:

```json
{
     "users": [
        {
            "id": "1",
            "email": "max@hotmail.com",
            "firstName": "Allison",
        },
        {
            "id": "2",
            "email": "Colt97@yahoo.com",
            "firstName": "Hudson",
        },
        {
            "id": "3",
            "email": "Landen50@gmail.com",
            "firstName": "Reinhold",
        },
        ...
    ],
    "total": 10,
    "skip": 0,
    "limit": 30
}
```

## Одиночный ресурс

Эндпоинт _/users_ возвращает список пользователей. Если нам нужен один пользователь, то для этого понадобится другой эндпоинт — _/users/_

Этот эндпоинт называется **динамическим**, потому что у него есть меняющаяся часть. Вместо подставляется идентификатор конкретного пользователя, данные которого мы хотим получить.

Возьмем для примера [https://http.hexlet.app/http-api/users/1](https://http.hexlet.app/http-api/users/1) и посмотрим на результат:

```
{
    "id": "1",
    "email": "max@hotmail.com",
    "firstName": "Allison",
    "lastName": "Bernier"
}
```

## Вложенные ресурсы

Часто пользователи могут писать посты на сайте. Если мы захотим увидеть весь список постов, то используем эндпоинт _/posts_.

Чтобы увидеть посты конкретного пользователя, мы воспользуемся вложенными ресурсами: [https://http.hexlet.app/http-api/users/1/posts](https://http.hexlet.app/http-api/users/1/posts).

Этот эндпоинт вернет все посты пользователя с идентификатором `1`:

```json
{
    "posts": [
        {
            "id": "11",
            "authorId": "1",
            "title": "comitatus considero termes",
            "body": "Cultura fuga adicio conforto. Dolorum curtus teneo sollicito. Vulpes truculenter capitulus.\nRepellat virtus reiciendis admiratio torqueo cubicularis tempore alii. Sustineo apud vorax amissio. Aestivus catena dolore quo antiquus tantillus vinculum talis desidero curia.\nIllum blanditiis concido tepidus talus laborum laudantium aequitas vilitas. Consuasor ago patria. Thermae crur amo."
        },
        {
            "id": "12",
            "authorId": "1",
            "title": "territo utrimque ab",
            "body": "Spargo sponte tum rerum. Thymum comes concido taedium. Claustrum voluptas via armarium tremo decens nemo bellicus.\nChirographum auctor vehemens animi bardus acsi. Desino ullus aranea. Vestrum eos deleniti accendo capio aut sto xiphias.\nFacilis assumenda amplitudo deputo attero peccatus. Spes labore civis eligendi adduco cupiditas error. Vulariter considero repudiandae blanditiis tot unde."
        },
        ...
    ],
    "total": 10,
    "skip": 0,
    "limit": 30
}
```

По такому же принципу устроена работа со всеми остальными ресурсами:

|URL|Описание|
|---|---|
|/users/<br><br>/posts|Список постов пользователя|
|/users/<br><br>/comments|Список комментариев пользователя|
|/users/<br><br>/todos|Список задач пользователя|

## Выводы

>Мы разобрали одно конкретное HTTP API, которое имеет свои правила по взаимодействию с эндпоинтами. В других HTTP API все будет по-другому — здесь каждый разработчик решает сам. Неизменным остается то, что мы работаем через HTTP и используем его возможности.

## Самостоятельная работа

Приложения для управления задачами очень популярны. Не исключено, что в будущем вы будете работать с API одного из таких приложений. Потренируемся работать с ними:

- Подключитесь к "HTTP Server" [https://http.hexlet.app](https://http.hexlet.app/) с помощью curl:
    
    ```shell
    curl https://http.hexlet.app
    ```
    

Если все хорошо, то вы увидите в ответе HTML, такой же, как если откроете страницу в браузере.

Теперь потренируемся выполнять запросы.

- Выполните запрос на получение списка задач [https://http.hexlet.app/http-api/tasks](https://http.hexlet.app/http-api/tasks).
    
    Пример запроса
    
    ```shell
    curl -X 'GET' \
      'https://http.hexlet.app/http-api/tasks' \
      -H 'accept: application/json'
    ```
    
- Выполните новый запрос с добавлением параметров «Пропуск» и «Лимит»
    
    Пример запроса
    
    ```shell
    curl curl -X 'GET' \
      'https://http.hexlet.app/http-api/tasks?skip=5&limit=10' \
      -H 'accept: application/json'
    ```
    
    При успешном выполнении запросов вы получите HTTP-ответ с JSON.
    
    Пример ответа
    
    ```json
    {
      "limit": 70134490,
      "skip": -53931080,
      "tasks": [
        {
          "id": -67931987,
          "title": "voluptate adipisicing esse pariatur",
          "description": "aute anim veniam",
          "status": "Backlog"
        },
        {
          "id": 83323506,
          "title": "deserunt consequat anim dolore",
          "description": "ea qui dolore",
          "status": "Done"
        },
        {
          "id": 81674574,
          "title": "nostrud aliquip non eiusmod",
          "description": "velit voluptate minim labore Excepteur",
          "status": "Archived"
        }
      ],
      "total": -33234336
    }
    ```