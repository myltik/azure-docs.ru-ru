---
title: Использование Go для создания запросов к базе данных SQL Azure | Документация Майкрософт
description: В этой статье показано, как использовать Go для создания программы, которая подключается к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL отправлять к ней запросы и изменять данные.
services: sql-database
author: David-Engel
manager: craigg
ms.reviewer: MightyPen
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: go
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: v-daveng
ms.openlocfilehash: b10d3e469ffac22a2b0994708f50125d21496fef
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="use-go-to-query-an-azure-sql-database"></a>Использование Go для создания запросов к базе данных SQL Azure

В этом кратком руководстве показано, как использовать [Go](https://godoc.org/github.com/denisenkom/go-mssqldb) для подключения к базе данных SQL Azure. Также демонстрируется применение инструкций Transact-SQL для запроса и изменения данных.

## <a name="prerequisites"></a>предварительным требованиям

Ниже указаны требования для работы с этим кратким руководством.

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- [Правило брандмауэра уровня сервера](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) для общедоступного IP-адреса компьютера, на котором выполняются действия из этого краткого руководства.

- Убедитесь, что установлен компонент Go и связанное программное обеспечение для вашей операционной системы.

    - **MacOS**. Установите Homebrew и GoLang. Ознакомьтесь с [шагом 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/mac/).
    - **Ubuntu**. Установите GoLang. Ознакомьтесь с [шагом 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/ubuntu/).
    - **Windows**. Установите GoLang. Ознакомьтесь с [шагом 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/windows/).    

## <a name="sql-server-connection-information"></a>Сведения о подключении SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-go-project-and-dependencies"></a>Создание проекта Go и зависимостей

1. Из терминала создайте папку проекта **SqlServerSample**. 

   ```bash
   mkdir SqlServerSample
   ```

2. Перейдите в папку **SqlServerSample**, затем получите и установите драйвер SQL Server для Go.

   ```bash
   cd SqlServerSample
   go get github.com/denisenkom/go-mssqldb
   go install github.com/denisenkom/go-mssqldb
   ```

## <a name="create-sample-data"></a>Создание примера данных

1. С помощью предпочитаемого текстового редактора создайте файл **CreateTestData.sql** в папке **SqlServerSample**. Скопируйте и вставьте в него следующий код T-SQL. Этот код создает схему, таблицу и вставляет в нее несколько строк.

   ```sql
   CREATE SCHEMA TestSchema;
   GO

   CREATE TABLE TestSchema.Employees (
     Id       INT IDENTITY(1,1) NOT NULL PRIMARY KEY,
     Name     NVARCHAR(50),
     Location NVARCHAR(50)
   );
   GO

   INSERT INTO TestSchema.Employees (Name, Location) VALUES
     (N'Jared',  N'Australia'),
     (N'Nikita', N'India'),
     (N'Tom',    N'Germany');
   GO

   SELECT * FROM TestSchema.Employees;
   GO
   ```

2. Подключитесь к базе данных с помощью программы sqlcmd и запустите сценарий SQL для создания схемы, таблицы и вставки нескольких строк. Замените соответствующие значения сервера, базы данных, имени пользователя и пароля.

   ```bash
   sqlcmd -S your_server.database.windows.net -U your_username -P your_password -d your_database -i ./CreateTestData.sql
   ```

## <a name="insert-code-to-query-sql-database"></a>Вставка кода для отправки запроса к базе данных SQL

1. Создайте файл **sample.go** в папке **SqlServerSample**.

2. Откройте его и замените имеющееся содержимое следующим кодом. Добавьте соответствующие значения сервера, базы данных, имени пользователя и пароля. Этот пример использует контекстные методы GoLang, чтобы убедиться в наличии активного подключения к серверу базы данных.

   ```go
   package main

   import (
       _ "github.com/denisenkom/go-mssqldb"
       "database/sql"
       "context"
       "log"
       "fmt"
   )

   var db *sql.DB

   var server = "your_server.database.windows.net"
   var port = 1433
   var user = "your_username"
   var password = "your_password"
   var database = "your_database"

   func main() {
       // Build connection string
       connString := fmt.Sprintf("server=%s;user id=%s;password=%s;port=%d;database=%s;",
           server, user, password, port, database)

       var err error

       // Create connection pool
       db, err = sql.Open("sqlserver", connString)
       if err != nil {
           log.Fatal("Error creating connection pool:", err.Error())
       }
       fmt.Printf("Connected!\n")

       // Create employee
       createId, err := CreateEmployee("Jake", "United States")
       fmt.Printf("Inserted ID: %d successfully.\n", createId)

       // Read employees
       count, err := ReadEmployees()
       fmt.Printf("Read %d rows successfully.\n", count)

       // Update from database
       updateId, err := UpdateEmployee("Jake", "Poland")
       fmt.Printf("Updated row with ID: %d successfully.\n", updateId)

       // Delete from database
       rows, err := DeleteEmployee("Jake")
       fmt.Printf("Deleted %d rows successfully.\n", rows)
   }

   func CreateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()
       var err error

       if db == nil {
           log.Fatal("What?")
       }

       // Check if database is alive.
       err = db.PingContext(ctx)
       if err != nil {
           log.Fatal("Error pinging database: " + err.Error())
       }

       tsql := fmt.Sprintf("INSERT INTO TestSchema.Employees (Name, Location) VALUES (@Name,@Location);")

       // Execute non-query with named parameters
       result, err := db.ExecContext(
           ctx,
           tsql,
           sql.Named("Location", location),
           sql.Named("Name", name))

       if err != nil {
           log.Fatal("Error inserting new row: " + err.Error())
           return -1, err
       }

       return result.LastInsertId()
   }

   func ReadEmployees() (int, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           log.Fatal("Error pinging database: " + err.Error())
       }

       tsql := fmt.Sprintf("SELECT Id, Name, Location FROM TestSchema.Employees;")

       // Execute query
       rows, err := db.QueryContext(ctx, tsql)
       if err != nil {
           log.Fatal("Error reading rows: " + err.Error())
           return -1, err
       }

       defer rows.Close()

       var count int = 0

       // Iterate through the result set.
       for rows.Next() {
           var name, location string
           var id int

           // Get values from row.
           err := rows.Scan(&id, &name, &location)
           if err != nil {
               log.Fatal("Error reading rows: " + err.Error())
               return -1, err
           }

           fmt.Printf("ID: %d, Name: %s, Location: %s\n", id, name, location)
           count++
       }

       return count, nil
   }

   // Update an employee's information
   func UpdateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           log.Fatal("Error pinging database: " + err.Error())
       }

       tsql := fmt.Sprintf("UPDATE TestSchema.Employees SET Location = @Location WHERE Name= @Name")

       // Execute non-query with named parameters
       result, err := db.ExecContext(
           ctx,
           tsql,
           sql.Named("Location", location),
           sql.Named("Name", name))
       if err != nil {
           log.Fatal("Error updating row: " + err.Error())
           return -1, err
       }

       return result.LastInsertId()
   }

   // Delete an employee from database
   func DeleteEmployee(name string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           log.Fatal("Error pinging database: " + err.Error())
       }

       tsql := fmt.Sprintf("DELETE FROM TestSchema.Employees WHERE Name=@Name;")

       // Execute non-query with named parameters
       result, err := db.ExecContext(ctx, tsql, sql.Named("Name", name))
       if err != nil {
           fmt.Println("Error deleting row: " + err.Error())
           return -1, err
       }

       return result.RowsAffected()
   }
   ```

## <a name="run-the-code"></a>Выполнение кода

1. В командной строке выполните следующие команды:

   ```bash
   go run sample.go
   ```

2. Проверьте выходные данные.

   ```text
   Connected!
   Inserted ID: 4 successfully.
   ID: 1, Name: Jared, Location: Australia
   ID: 2, Name: Nikita, Location: India
   ID: 3, Name: Tom, Location: Germany
   ID: 4, Name: Jake, Location: United States
   Read 4 rows successfully.
   Updated row with ID: 4 successfully.
   Deleted 1 rows successfully.
   ```

## <a name="next-steps"></a>Дополнительная информация

- [Проектирование первой базы данных SQL Azure](sql-database-design-first-database.md)
- [Драйвер Go для Microsoft SQL Server](https://github.com/denisenkom/go-mssqldb)
- [Сообщите о проблемах или задайте вопросы](https://github.com/denisenkom/go-mssqldb/issues)

