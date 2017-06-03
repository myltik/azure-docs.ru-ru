---
title: "Подключение к базе данных SQL Azure с помощью Java | Документация Майкрософт"
description: "В этой статье представлен пример кода Java, который можно использовать для подключения и выполнения запросов к базе данных SQL Azure."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start connect, mvc
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 05/23/2017
ms.author: andrela
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 63ab7345b70d456d9d1ad23255d9ca7e777e161d
ms.contentlocale: ru-ru
ms.lasthandoff: 05/31/2017


---
# <a name="azure-sql-database-use-java-to-connect-and-query-data"></a>База данных SQL Azure: подключение и запрос данных с помощью Java

В этом кратком руководстве показано, как, используя [Java](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server), подключиться к базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос, вставку, обновление и удаление данных в базе данных на платформах Mac OS, Ubuntu Linux и Windows.

## <a name="prerequisites"></a>Предварительные требования

Начальной точкой в руководстве являются ресурсы, созданные в одном из этих кратких руководств:

- [Создание базы данных с помощью портала](sql-database-get-started-portal.md)
- [Создание базы данных SQL Azure и отправка к ней запросов с помощью Azure CLI](sql-database-get-started-cli.md)
- [Создание базы данных с помощью PowerShell](sql-database-get-started-powershell.md)

## <a name="install-java-software"></a>Установка программного обеспечения Java

В этом разделе предполагается, что у вас уже есть опыт разработки на Java и вы только начали работу с Базой данных SQL Azure. Если вы новичок в разработке на Java, перейдите на страницу [создания приложения с помощью SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/), выберите язык **Java** и операционную систему.

### <a name="mac-os"></a>**Mac OS**
Откройте терминал и перейдите в каталог, в котором вы планируете создать проект Java. Введите следующие команды для установки **brew** и **Maven**. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Откройте терминал и перейдите в каталог, в котором вы планируете создать проект Java. Введите следующие команды для установки **Maven**.

```bash
sudo apt-get install maven
```

### <a name="windows"></a>**Windows**
Установите [Maven](https://maven.apache.org/download.cgi) с помощью официального установщика. Maven можно использовать для управления зависимостями, выполнения сборки, тестирования и запуска проекта Java. 

## <a name="get-connection-information"></a>Получение сведений о подключении

Получите сведения о подключении, необходимые для подключения к базе данных SQL Azure. Вам понадобится следующее: полное имя сервера, имя базы данных и сведения для входа.

1. Войдите на [портал Azure](https://portal.azure.com/).
2. В меню слева выберите **Базы данных SQL** и на странице **Базы данных SQL** щелкните имя своей базы данных. 
3. На странице **Обзор** базы данных просмотрите полное имя сервера, как показано на рисунке ниже. Вы можете навести указатель мыши на имя сервера, чтобы отобразился пункт **Щелкните, чтобы скопировать**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Если вы забыли данные для входа на сервер, перейдите на соответствующую страницу, чтобы просмотреть имя администратора сервера и при необходимости сбросить пароль.
5. Щелкните **Показать строки подключения к базам данных**.

6. Просмотрите полную строку подключения **JDBC**.

    ![Строка подключения JDBC](./media/sql-database-connect-query-jdbc/jdbc-connection-string.png)    

### <a name="create-maven-project"></a>**Создание проекта Maven**
В терминале создайте новый проект Maven. 
```bash
mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
```

Добавьте **Microsoft JDBC Driver для SQL Server** к зависимостям, указанным в файле ***pom.xml***. 

```xml
<dependency>
    <groupId>com.microsoft.sqlserver</groupId>
    <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
</dependency>
```

## <a name="select-data"></a>Выбор данных

Используйте следующий код, чтобы запросить 20 наиболее популярных продуктов по категории, используя класс [connection](https://docs.microsoft.com/sql/connect/jdbc/working-with-a-connection) в инструкции Transact-SQL [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql). Замените параметры hostHame, dbName, user и password значениями, указанными при создании базы данных с использованием образца данных AdventureWorksLT. 

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
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
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
                }
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

## <a name="insert-data"></a>Добавление данных

Используйте указанный ниже код, чтобы вставить новый продукт в таблицу SalesLT.Product указанной базы данных, используя класс [Prepared Statements](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql) и инструкцию Transact-SQL [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Замените параметры hostHame, dbName, user и password значениями, указанными при создании базы данных с использованием образца данных AdventureWorksLT. 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Insert data example:");
                System.out.println("=========================================");

                // Prepared statement to insert data
                String insertSql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, " 
                    + " StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

                java.util.Date date = new java.util.Date();
                java.sql.Timestamp sqlTimeStamp = new java.sql.Timestamp(date.getTime());

                try (PreparedStatement prep = connection.prepareStatement(insertSql)) {
                        prep.setString(1, "BrandNewProduct");
                        prep.setInt(2, 200989);
                        prep.setString(3, "Blue");
                        prep.setDouble(4, 75);
                        prep.setDouble(5, 80);
                        prep.setTimestamp(6, sqlTimeStamp);

                        int count = prep.executeUpdate();
                        System.out.println("Inserted: " + count + " row(s)");
                }
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```
## <a name="update-data"></a>Обновление данных

Используйте указанный ниже код, чтобы обновить данные добавленного нового продукта в базе данных SQL Azure, используя класс [Prepared Statements](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql) и инструкцию Transact-SQL [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql). Замените параметры hostHame, dbName, user и password значениями, указанными при создании базы данных с использованием образца данных AdventureWorksLT. 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Update data example:");
                System.out.println("=========================================");

                // Prepared statement to update data
                String updateSql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?";

                try (PreparedStatement prep = connection.prepareStatement(updateSql)) {
                        prep.setString(1, "500");
                        prep.setString(2, "BrandNewProduct");

                        int count = prep.executeUpdate();
                        System.out.println("Updated: " + count + " row(s)")
                }
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```



## <a name="delete-data"></a>Удаление данных

Используйте указанный код, чтобы удалить добавленный ранее новый продукт с помощью класса [Prepared Statements](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql) и инструкции Transact-SQL [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql). Замените параметры hostHame, dbName, user и password значениями, указанными при создании базы данных с использованием образца данных AdventureWorksLT. 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Delete data example:");
                System.out.println("=========================================");

                // Prepared statement to delete data
                String deleteSql = "DELETE SalesLT.Product WHERE Name = ?";

                try (PreparedStatement prep = connection.prepareStatement(deleteSql)) {
                        prep.setString(1, "BrandNewProduct");

                        int count = prep.executeUpdate();
                        System.out.println("Deleted: " + count + " row(s)");
                }
        }        
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия
- [Проектирование первой базы данных SQL Azure](sql-database-design-first-database.md)
- [Microsoft JDBC Driver для SQL Server](https://github.com/microsoft/mssql-jdbc)
- [Сообщите о проблеме или задайте вопросы](https://github.com/microsoft/mssql-jdbc/issues)


