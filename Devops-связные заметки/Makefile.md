
**Makefile** - используется с помощью утилиты *make*, служит для автоматизации, преимущественно, сборки приложения. Содержит в себе инструкции и цели, которые можно запускать по отдельности.

==Задачи в мейкфайле выполняются последовательно и только в случае успеха предыдущей==
## Синтаксис
```
# Makefile
цель1: # имя цели, поддерживается kebab-case и snake_case
	команда1 # для отступа используется табуляция, это важная деталь 
	команда2 # команды будут выполняться последовательно и только в случае успеха предыдущей
```

- **Символ @** можно использовать чтобы команды, выполняемые в мейкфайле не выводились в терминал (по умолчанию выводятся и команды и их стандартные вывод)

- Запуск конкретных целей происходит с помощью одной команды `make setup`, `make start`, `make test` или через пробел `make setup start test` 


## Пример:
```
# Makefile
up: # разворачивание и запуск
	cp -n .env.example .env
	touch database/database.sqlite
	composer install
	npm install
	php artisan key:generate
	php artisan migrate --seed
	heroku local -f Procfile.dev # запуск проекта
```

## Пример с несколькими целями:
```
# Makefile
env-prepare: # создать .env-файл для секретов
	cp -n .env.example .env

sqlite-prepare: # подготовить локальную БД
	touch database/database.sqlite

install: # установить зависимости
	composer install
	npm install

key: # сгенерировать ключи
	php artisan key:generate

db-prepare: # загрузить данные в БД
	php artisan migrate --seed

start: # запустить приложение
	heroku local -f Procfile.dev
```

## Синтаксис позволяет ссылаться на цели, описанные ниже
```
# Makefile
setup: env-prepare sqlite-prepare install key db-prepare # можно ссылаться на цели, описанные ниже

env-prepare:
	cp -n .env.example .env

sqlite-prepare:
	touch database/database.sqlite

install:
	composer install
	npm install

key:
	php artisan key:generate

db-prepare:
	php artisan migrate --seed

start:
	heroku local -f Procfile.dev
```

## Продвинутое использование
### Фальшивая цель

Использование `make` в проекте однажды может привести к появлению ошибки `make: <имя-цели> is up to date.`, хотя всё написано правильно. Зачастую, её появление связано с наличием каталога или файла, совпадающего с именем цели. Например:

```
# Makefile
test: # цель в мейкфайле
	php artisan test
```

```
# Bash
$ ls
Makefile
test # в файловой системе находится каталог с именем, как у цели в мейкфайле

$ make test # попытка запустить тесты
make: `test` is up to date.
```

Изначально `make` предназначалась для сборок из исходного кода. Поэтому она ищет каталог или файл с указанным именем, и пытается собрать из него проект. Чтобы изменить это поведение, необходимо в конце мейкфайла добавить `.PHONY` указатель на цель:

```
# Makefile
test:
	php artisan test

.PHONY: test
```

```
# Bash
$ make test
✓ All tests passed!
```

### Последовательный запуск команд и игнорирование ошибок

Самый простой (_но не единственный_) способ «заглушить» ошибку — это сделать логическое ИЛИ прямо в мейкфайле:

```
# Makefile
env-prepare:
	cp -n .env.example .env || true # теперь любой исход выполнения команды будет считаться успешным
```

Добавлять такие хаки стоит с осторожностью, чтобы не «выстрелить себе в ногу» в более сложных случаях.

### Переменные

Часто в команды в мейкхафлах нужно передать какой-то параметр, тогда переменные можно прописать прямо внутри мейкфайла и передавать их при вызове

```
# Makefile
say:
	echo "Hello, $(HELLO)!"
```

```Bash
$ make say HELLO=World
echo "Hello, World!"
Hello, World!

$ make say HELLO=Kitty
echo "Hello, Kitty!"
Hello, Kitty!
```

Переменные могут быть необязательными и содержать значение по умолчанию. Обычно их объявляют в начале мейкфайла.

```
# Makefile
HELLO?=World # знак вопроса указывает, что переменная опциональна. Значение после присвоения можно не указывать.

say:
	echo "Hello, $(HELLO)!"
```

```bash
# Bash
$ make say
echo "Hello, World!"
Hello, World!

$ make say HELLO=Kitty
echo "Hello, Kitty!"
Hello, Kitty!
```

Некоторые переменные в _Makefile_ имеют названия отличные от системных. Например, `$PWD` называется `$CURDIR` в [мейкфайле](https://github.com/hexlet-basics/hexlet_basics/blob/3f4635bf629e2676efe547c9a01c22a2573eaebd/Makefile#L35-L39):

```
# Makefile
project-env-generate:
	docker run --rm -e RUNNER_PLAYBOOK=ansible/development.yml \
		-v $(CURDIR)/ansible/development:/runner/inventory \ # $(CURDIR) - то же самое, что $PWD в терминале
		-v $(CURDIR):/runner/project \
		ansible/ansible-runner
```
### Дополнительные материалы

- [Руководство по современному Make](https://ru.makefile.site/) — «выжимка» из документации на русском языке;
- [Утилита make: полезный универсальный инструмент программиста](https://www.youtube.com/watch?v=pK9mF5aK05Q) — видео-версия данного гайда.