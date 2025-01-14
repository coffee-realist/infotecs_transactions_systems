Cистема обработки транзакций платёжной системы
======================================================================
Краткое описание
----------------
Этот проект представляет собой систему обработки транзакций для платёжной системы, реализованную в виде HTTP сервера с REST API на языке Go. Он включает в себя три основных метода: **Send**, **GetLast**, и **GetBalance**.
Проект реализует базовые функции платёжной системы с использованием SQLite в качестве базы данных. В проекте используется Docker для контейнеризации приложения. В качестве серверного фреймворка применяется стандартный HTTP сервер на Go. В проекте обеспечены все требования безопасности, персистентности данных и корректной работы с базой данных.

Структура проекта
-----------------

    infotecs_transactions_system/
    ├── cmd/
    │   └── main.go               # Главный файл, точка входа в приложение
    ├── internal/
    │   ├── database/
    │   │   └── database.go       # Функции для работы с базой данных
    │   ├── handlers/
    │   │   ├── balance.go        # Обработчик для получения баланса
    │   │   ├── send.go           # Обработчик для отправки средств
    │   │   └── transactions.go   # Обработчик для получения N последних транзакций
    │   └── services/
    │       └── wallet_service.go  # Инициализация и генерация кошельков
    ├── migrations/               # Миграции для базы данных
    │   └── 000001_create_tables.up.sql
    │   └── 000001_create_tables.down.sql
    ├── Dockerfile                # Dockerfile для сборки контейнера
    ├── docker-compose.yml        # Конфигурация Docker Compose
    ├── database.db               # База данных SQLite
    ├── go.mod                    # Файлы зависимостей модулей Go
    ├── go.sum                    
    ├── .dockerignore             # Игнорируемые Docker'ом файлы       
    ├── .gitignore                # Игнорируемые Git'ом файлы   
    └── README.md                 # Документация проекта


Инициализация проекта
---------------------

Для начала работы с проектом выполните следующие шаги:

1.  Клонируйте репозиторий на свой компьютер:

git clone [https://github.com/coffee-realist/infotecs_transactions_systems](https://github.com/coffee-realist/infotecs_transactions_systems)

3.  Перейдите в директорию проекта:

cd infotecs\_transactions\_system

5.  Убедитесь, что у вас установлен Go (обязательно) и Docker (опционально: для запуска проекта через Docker).
6.  Запустите установку зависимостей для модулей Go:

go mod tidy


Миграции в БД
--------

Миграции для базы данных хранятся в папке **migrations/**. В проекте используется библиотека **golang-migrate** для применения миграций. При первом запуске приложения автоматически создается база данных и применяется первая миграция, если она не была выполнена.

Запуск без Docker
-----------------

Если вы хотите запустить приложение без Docker, выполните следующие шаги:

1.  Убедитесь, что у вас установлен Go.
2.  Запустите приложение запуском файла main.go или с помощью команды:

go run cmd/main.go


3.   Приложение будет доступно по адресу [http://localhost:8080](http://localhost:8080).

Запуск через Docker
-------------------

Для того чтобы запустить проект в Docker, выполните следующие шаги:

1.  Убедитесь, что у вас установлен Docker и Docker Compose.
2.  Перейдите в директорию проекта.
3.  Если в корневой папке отсутствует файл **database.db**, то создайте пустой файл с таким именем (при запуске через Docker необходимо наличие уже созданной ранее БД или же пустого файла с именем БД - database.db).
4.  Запустите контейнер с приложением:

docker-compose up --build

5.  После этого сервер будет доступен по адресу [http://localhost:8080](http://localhost:8080).


API
---

### 1\. POST /api/send

Этот метод отправляет средства с одного кошелька на другой. В теле запроса должен быть передан JSON-объект с полями:

    {
        "from": "e240d825d255af751f5f55af8d9671beabdf2236c0a3b4e2639b3e182d994c88",
        "to": "d8f3c7d85256d94d5569a3b61f7d2b10720a6b5f53cfce08597d8a410c4d7bfa",
        "amount": 3.50
    }


В случае успешной транзакции возвращается 200 OK, в случае недостаточного баланса – ошибка 400.

### 2\. GET /api/transactions?count=N

Этот метод возвращает N последних транзакций. Пример:

    GET /api/transactions?count=5


Ответ возвращает массив JSON с последними транзакциями.

### 3\. GET /api/wallet/{address}/balance

Этот метод возвращает баланс указанного кошелька. Пример:

    GET /api/wallet/e240d825d255af751f5f55af8d9671beabdf2236c0a3b4e2639b3e182d994c88/balance


Ответ возвращает JSON с балансом кошелька.

Требования
----------

*   Go 1.22.0 или выше.
*   Docker (для запуска через Docker Compose).
