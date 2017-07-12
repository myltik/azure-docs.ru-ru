---
title: "Подключение к базе данных Azure для PostgreSQL с помощью языка Go | Документация Майкрософт"
description: "В этом кратком руководстве представлен пример кода на языке программирования Go, который можно использовать для подключения к базе данных Azure для PostgreSQL и запроса данных из нее."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: go
ms.topic: hero-article
ms.date: 06/29/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: f3e7e57993b5253a895640854672da431c00854b
ms.contentlocale: ru-ru
ms.lasthandoff: 07/04/2017

---

<a id="azure-database-for-postgresql-use-go-language-to-connect-and-query-data" class="xliff"></a>

# База данных Azure для PostgreSQL: подключение и запросы данных с помощью языка Go
В этом кратком руководстве описывается, как подключиться к базе данных Azure для PostgreSQL с помощью кода на языке [Go](https://golang.org/). Здесь также показано, как использовать инструкции SQL для запроса, вставки, обновления и удаления данных в базе данных. В этой статье предполагается, что у вас уже есть опыт разработки на Go, и вы только начали работу с базой данных Azure для PostgreSQL.

<a id="prerequisites" class="xliff"></a>

## Предварительные требования
В качестве отправной точки в этом кратком руководстве используются ресурсы, созданные в соответствии со следующими материалами:
- [Создание базы данных с помощью портала](quickstart-create-server-database-portal.md)
- [Создание базы данных с помощью Azure CLI](quickstart-create-server-database-azure-cli.md)

<a id="install-go-and-pq" class="xliff"></a>

## Установка Go и pq
- Скачайте и установите Go согласно [инструкциям по установке](https://golang.org/doc/install) с учетом вашей платформы.
- Создайте папку для проекта, например C:\Postgresql\. С помощью командной строки перейдите в папку проекта, например, введите `cd C:\Postgres\`.
- В этом же каталоге скачайте [драйвер Pure Go Postgres (pq)](https://github.com/lib/pq) в папку проекта с помощью команды `go get github.com/lib/pq`.

<a id="build-and-run-go-code" class="xliff"></a>

## Сборка и выполнение кода Go 
- Сохраните код в текстовый файл с расширением *.go, а затем сохраните его в папку проекта, например здесь: `C:\postgres\read.go`.
- Чтобы запустить код, перейдите в папку проекта `cd postgres`, введите команду `go run read.go` для компиляции приложения и запустите его.
- Чтобы создать код в собственном приложении, введите `go build read.go`, а затем выполните read.exe для запуска приложения.

<a id="get-connection-information" class="xliff"></a>

## Получение сведений о подключении
Получите сведения, необходимые для подключения к базе данных Azure.для PostgreSQL. Вам потребуется полное имя сервера и учетные данные для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева на портале Azure щелкните **Все ресурсы** и выполните поиск по имени созданного сервера, например **mypgserver-20170401**.
3. Щелкните имя сервера **mypgserver-20170401**.
4. Выберите страницу **обзора** сервера. Запишите значения **имени сервера** и **имени для входа администратора сервера**.
 ![База данных Azure для PostgreSQL. Учетные данные администратора сервера для входа](./media/connect-go/1-connection-string.png)
5. Если вы забыли данные для входа на сервер, перейдите на страницу **Обзор**, где указано имя администратора сервера. При необходимости сбросьте пароль.

<a id="connect-and-create-a-table" class="xliff"></a>

## Подключение и создание таблицы
Используйте приведенный ниже код для подключения и создайте таблицу с помощью инструкции SQL **CREATE TABLE**. Добавьте строки в таблицу, применив инструкцию SQL **INSERT INTO**.

Код импортирует три пакета: [пакет sql](https://golang.org/pkg/database/sql/), [пакет pq](http://godoc.org/github.com/lib/pq) как драйвер для обмена данными с сервером Postgres и [пакет fmt](https://golang.org/pkg/fmt/) для вывода входных и выходных данных в командной строке.

Код вызывает метод [sql.Open()](http://godoc.org/github.com/lib/pq#Open) для подключения к базе данных Azure для PostgreSQL, а затем проверяет подключение с помощью метода [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). [Дескриптор базы данных](https://golang.org/pkg/database/sql/#DB) используется для всех компонентов. Он содержит пул подключений к серверу базы данных. Этот код несколько раз вызывает метод [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) для выполнения нескольких команд SQL. При каждом запуске пользовательский метод checkError() проверяет наличие ошибок и инициирует аварийный выход в случае их обнаружения.


Замените значения параметров `HOST`, `DATABASE`, `USER` и `PASSWORD` своими значениями. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Drop previous table of same name if one exists.
    _, err = db.Exec("DROP TABLE IF EXISTS inventory;")
    checkError(err)
    fmt.Println("Finished dropping table (if existed)")

    // Create table.
    _, err = db.Exec("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
    checkError(err)
    fmt.Println("Finished creating table")

    // Insert some data into table.
    sql_statement := "INSERT INTO inventory (name, quantity) VALUES ($1, $2);"
    _, err = db.Exec(sql_statement, "banana", 150)
    checkError(err)
    _, err = db.Exec(sql_statement, "orange", 154)
    checkError(err)
    _, err = db.Exec(sql_statement, "apple", 100)
    checkError(err)
    fmt.Println("Inserted 3 rows of data")
}
```

<a id="read-data" class="xliff"></a>

## Считывание данных
Используйте указанный ниже код с инструкцией SQL **SELECT** для подключения и чтения данных. 

Код импортирует три пакета: [пакет sql](https://golang.org/pkg/database/sql/), [пакет pq](http://godoc.org/github.com/lib/pq) как драйвер для обмена данными с сервером Postgres и [пакет fmt](https://golang.org/pkg/fmt/) для вывода входных и выходных данных в командной строке.

Код вызывает метод [sql.Open()](http://godoc.org/github.com/lib/pq#Open) для подключения к базе данных Azure для PostgreSQL, а затем проверяет подключение с помощью метода [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). [Дескриптор базы данных](https://golang.org/pkg/database/sql/#DB) используется для всех компонентов. Он содержит пул подключений к серверу базы данных. Запрос выбора выполняется с помощью метода [db.Query()](https://golang.org/pkg/database/sql/#DB.Query), а итоговые строки сохраняются в переменной типа [rows](https://golang.org/pkg/database/sql/#Rows). Код считывает из столбцов значения данных в текущей строке с помощью метода [rows.Scan()](https://golang.org/pkg/database/sql/#Rows.Scan), а затем запускает цикл по строкам, используя итератор [сrows.Next()](https://golang.org/pkg/database/sql/#Rows.Next), пока строк больше не останется. Значения столбцов для каждой строки выводятся на консоль. При каждом запуске пользовательский метод checkError() проверяет наличие ошибок и инициирует аварийный выход в случае их обнаружения.

Замените значения параметров `HOST`, `DATABASE`, `USER` и `PASSWORD` своими значениями. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Read rows from table.
    var id int
    var name string
    var quantity int

    sql_statement := "SELECT * from inventory;"
    rows, err := db.Query(sql_statement)
    checkError(err)

    for rows.Next() {
        switch err := rows.Scan(&id, &name, &quantity); err {
        case sql.ErrNoRows:
            fmt.Println("No rows were returned")
        case nil:
            fmt.Printf("Data row = (%d, %s, %d)\n", id, name, quantity)
        default:
            checkError(err)
        }
    }
}
```

<a id="update-data" class="xliff"></a>

## Обновление данных
Используйте указанный ниже код с инструкцией SQL **UPDATE** для подключения и обновления данных.

Код импортирует три пакета: [пакет sql](https://golang.org/pkg/database/sql/), [пакет pq](http://godoc.org/github.com/lib/pq) как драйвер для обмена данными с сервером Postgres и [пакет fmt](https://golang.org/pkg/fmt/) для вывода входных и выходных данных в командной строке.

Код вызывает метод [sql.Open()](http://godoc.org/github.com/lib/pq#Open) для подключения к базе данных Azure для PostgreSQL, а затем проверяет подключение с помощью метода [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). [Дескриптор базы данных](https://golang.org/pkg/database/sql/#DB) используется для всех компонентов. Он содержит пул подключений к серверу базы данных. Этот код вызывает метод [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) для выполнения инструкции SQL, которая обновляет таблицу. Пользовательский метод checkError() применяется для проверки наличия ошибок и аварийного выхода при их обнаружении.

Замените значения параметров `HOST`, `DATABASE`, `USER` и `PASSWORD` своими значениями. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Modify some data in table.
    sql_statement := "UPDATE inventory SET quantity = $2 WHERE name = $1;"
    _, err = db.Exec(sql_statement, "banana", 200)
    checkError(err)
    fmt.Println("Updated 1 row of data")
}
```

<a id="delete-data" class="xliff"></a>

## Удаление данных
Используйте указанный ниже код с инструкцией SQL **DELETE** для подключения и чтения данных. 

Код импортирует три пакета: [пакет sql](https://golang.org/pkg/database/sql/), [пакет pq](http://godoc.org/github.com/lib/pq) как драйвер для обмена данными с сервером Postgres и [пакет fmt](https://golang.org/pkg/fmt/) для вывода входных и выходных данных в командной строке.

Код вызывает метод [sql.Open()](http://godoc.org/github.com/lib/pq#Open) для подключения к базе данных Azure для PostgreSQL, а затем проверяет подключение с помощью метода [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). [Дескриптор базы данных](https://golang.org/pkg/database/sql/#DB) используется для всех компонентов. Он содержит пул подключений к серверу базы данных. Этот код вызывает метод [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) для выполнения инструкции SQL, которая обновляет таблицу. Пользовательский метод checkError() применяется для проверки наличия ошибок и аварийного выхода при их обнаружении.

Замените значения параметров `HOST`, `DATABASE`, `USER` и `PASSWORD` своими значениями. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Delete some data from table.
    sql_statement := "DELETE FROM inventory WHERE name = $1;"
    _, err = db.Exec(sql_statement, "orange")
    checkError(err)
    fmt.Println("Deleted 1 row of data")
}
```

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия
> [!div class="nextstepaction"]
> [Перенос базы данных с помощью экспорта и импорта](./howto-migrate-using-export-and-import.md)

