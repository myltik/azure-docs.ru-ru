---
title: Использование Java для создания запросов к базе данных SQL Azure | Документация Майкрософт
description: В этой статье показано, как использовать Java для создания программы, которая подключается к базе данных SQL Azure, и создавать к ней запросы с помощью инструкций Transact-SQL.
services: sql-database
author: ajlam
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: java
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: andrela
ms.openlocfilehash: e19006d177777b18e1665ec5039f9c28f7e54df5
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34363678"
---
# <a name="use-java-to-query-an-azure-sql-database"></a>Использование Java для создания запросов к базе данных SQL Azure

В этом кратком руководстве показано, как использовать [Java](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) для подключения к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос данных.

## <a name="prerequisites"></a>предварительным требованиям

Ниже указаны требования для работы с этим кратким руководством.

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- [Правило брандмауэра уровня сервера](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) для общедоступного IP-адреса компьютера, на котором выполняются действия из этого краткого руководства.

- Убедитесь, что установлен компонент Java и связанное программное обеспечение для вашей операционной системы.

    - **Mac OS.** Установите Homebrew и Java, а затем Maven. Ознакомьтесь с шагами 1.2 и 1.3 в [этом руководстве](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).
    - **Ubuntu.** Установите комплект разработчика Java и Maven. Ознакомьтесь с шагами 1.2, 1.3 и 1.4 в [этом руководстве](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).
    - **Windows.** Установите комплект разработчика Java и Maven. Ознакомьтесь с шагами 1.2 и 1.3 в [этом руководстве](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).    

## <a name="sql-server-connection-information"></a>Сведения о подключении SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-maven-project-and-dependencies"></a>**Создание проекта Maven и зависимостей**
1. В терминале создайте проект Maven с именем **sqltest**. 

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

2. При появлении запроса введите **Y**.
3. Перейдите в каталог **sqltest** и откройте ***pom.xml*** с помощью предпочитаемого текстового редактора.  Добавьте **Microsoft JDBC Driver для SQL Server** к зависимостям проекта, используя следующий код:

   ```xml
   <dependency>
       <groupId>com.microsoft.sqlserver</groupId>
       <artifactId>mssql-jdbc</artifactId>
       <version>6.4.0.jre8</version>
   </dependency>
   ```

4. В файле ***pom.xml*** добавьте в проект указанные ниже свойства.  Если у вас нет раздела свойств, его можно добавить после зависимостей.

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

5. Сохраните и закройте файл ***pom.xml***.

## <a name="insert-code-to-query-sql-database"></a>Вставка кода для отправки запроса к базе данных SQL

1. В проекте Maven уже должен быть файл с именем ***App.java***, расположенный в папке: \sqltest\src\main\java\com\sqlsamples\App.java.

2. Откройте файл, замените содержимое следующим кодом и добавьте соответствующие значения для сервера, базы данных, пользователя и пароля.

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.DriverManager;

   public class App {

    public static void main(String[] args) {
    
        // Connect to database
           String hostName = "your_server.database.windows.net";
           String dbName = "your_database";
           String user = "your_username";
           String password = "your_password";
           String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
           Connection connection = null;

           try {
                   connection = DriverManager.getConnection(url);
                   String schema = connection.getSchema();
                   System.out.println("Successful connection - Schema: " + schema);

                   System.out.println("Query data example:");
                   System.out.println("=========================================");

                   // Create and execute a SELECT SQL statement.
                   String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName " 
                       + "FROM [SalesLT].[ProductCategory] pc "  
                       + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";
                
                   try (Statement statement = connection.createStatement();
                       ResultSet resultSet = statement.executeQuery(selectSql)) {

                           // Print results from select statement
                           System.out.println("Top 20 categories:");
                           while (resultSet.next())
                           {
                               System.out.println(resultSet.getString(1) + " "
                                   + resultSet.getString(2));
                           }
                    connection.close();
                   }                   
           }
           catch (Exception e) {
                   e.printStackTrace();
           }
       }
   }
   ```

## <a name="run-the-code"></a>Выполнение кода

1. В командной строке выполните следующие команды:

   ```bash
   mvn package
   mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

2. Убедитесь, что возвращены первые 20 строк, а затем закройте окно приложения.


## <a name="next-steps"></a>Дополнительная информация
- [Проектирование первой базы данных SQL Azure](sql-database-design-first-database.md)
- [Microsoft JDBC Driver для SQL Server](https://github.com/microsoft/mssql-jdbc)
- [Сообщите о проблеме или задайте вопросы](https://github.com/microsoft/mssql-jdbc/issues)

