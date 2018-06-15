---
title: Соединитель Spark с базой данных SQL Azure и SQL Server | Документация Майкрософт
description: Узнайте, как использовать соединитель Spark для базы данных SQL Azure и SQL Server
services: sql-database
author: allenwux
manager: craigg
ms.service: sql-database
ms.custom: ''
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: xiwu
ms.openlocfilehash: a422f65097466e4bbe5740c449d3ccf88701802b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650168"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Ускорение анализа больших данных в режиме реального времени с помощью базы данных SQL Azure и SQL Server

Соединитель Spark для базы данных SQL Azure и SQL Server разрешает использовать базы данных SQL, включая базы данных SQL Azure и SQL Server, в качестве источника входных данных или приемника выходных данных для заданий Spark. Это позволяет использовать данные о транзакциях в реальном времени при анализе больших данных и сохранять результаты нерегламентированных запросов или отчетов. По сравнению со встроенным соединителем JDBC этот соединитель обеспечивает возможность массовой вставки данных в базы данных SQL. Он может в 10-20 раз превосходить по производительности вставку по строкам. Соединитель Spark для базы данных SQL Azure и SQL Server также поддерживает аутентификацию AAD. Это позволяет безопасно подключаться к базе данных SQL Azure из Azure Databricks, используя учетную запись AAD. Она предоставляет аналогичные интерфейсы со встроенным соединителем JDBC. Имеющиеся задания Spark можно легко перенести, чтобы использовать этот соединитель.

## <a name="download"></a>Загрузка
Чтобы приступить к работе, скачайте соединитель Spark для базы данных SQL из репозитория [azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) на сайте GitHub.

## <a name="official-supported-versions"></a>Официальные поддерживаемые версии

| Компонент                            |Version (версия)                  |
| :----------------------------------- | :---------------------- |
| Apache Spark                         |2.0.2 или более поздние версии           |
| Scala                                |2.10 или более поздние версии            |
| Microsoft JDBC Driver для SQL Server |6.2 или более поздние версии             |
| Microsoft SQL Server                 |SQL Server 2008 или более поздняя версия |
| Базы данных SQL Azure                   |Поддерживаются                |

Соединитель Spark для базы данных SQL Azure и SQL Server использует Microsoft JDBC Driver для SQL Server, чтобы перемещать данные между рабочими узлами Spark и базами данных SQL:
 
Поток данных выглядит следующим образом:
1. Главный узел Spark подключается к SQL Server или базе данных Azure SQL и передает данные из определенной таблицы или использует специальный SQL-запрос.
2. Главный узел Spark распределяет данные в рабочие узлы для преобразования. 
3. Рабочий узел подключается к SQL Server или базе данных Azure SQL и записывает данные в базу данных. Пользователь может использовать вставку по строкам или массовую вставку.

На схеме ниже показан поток данных.

   ![архитектура](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Создание соединителя Spark для базы данных SQL
Сейчас в проекте соединителя используется maven. Чтобы создать соединитель без зависимостей, выполните следующую команду:

- mvn clean package;
- скачать последние версии JAR-файла из папки release;
- включить JAR-файл базы данных SQL Spark.

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Подключение Spark к базе данных SQL с помощью соединителя
Подключиться к базе данных Azure SQL или SQL Server можно из заданий Spark, а также читать или записывать данные. Кроме того, можно выполнить запрос DML или DDL в базе данных Azure SQL или базе данных SQL Server.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Чтение данных из базы данных SQL Azure или SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients"
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Чтение данных из базы данных SQL Azure или SQL Server с помощью определенного запроса SQL
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDb(config)
collection.show()
```

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Запись данных в базу данных SQL Azure или SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
 
// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients"
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Выполнение запроса DML или DDL в базе данных SQL Azure или SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City= 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.SqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>Подключение Spark к базе данных SQL Azure с использованием аутентификации AAD
К базе данных SQL Azure можно подключиться с использованием аутентификации Azure Active Directory. Используйте аутентификацию AAD для централизованного управления удостоверениями пользователей базы данных и как альтернативу аутентификации SQL Server.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Подключение с использованием режима аутентификации ActiveDirectoryPassword
#### <a name="setup-requirement"></a>Настройка требований
При использовании режима аутентификации ActiveDirectoryPassword необходимо скачать [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) и его зависимости, а затем включить их в путь сборки Java.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username ",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>Подключение с помощью маркера доступа
#### <a name="setup-requirement"></a>Настройка требований
При использовании режима аутентификации на основе маркера доступа необходимо скачать [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) и его зависимости, а затем включить их в путь сборки Java.

Дополнительные сведения о том, как получить маркер доступа для базы данных SQL Azure, см. в [этой статье](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token ",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Запись данных в базу данных SQL Azure или SQL Server с помощью массовой вставки
Традиционный соединитель JDBC записывает данные в базу данных SQL Azure или SQL Server с помощью вставки по строкам. Соединитель Spark для базы данных SQL можно использовать для записи данных в базу данных SQL с помощью массовой вставки. Это значительно улучшает производительность записи при загрузке больших наборов данных или загрузке данных в таблицы, в которых используется индекс хранилища столбцов.

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/** 
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "databaseName"      -> "zeqisql",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Дополнительная информация
Если это еще не сделано, скачайте соединитель для базы данных SQL Azure и SQL Server из [репозитория GitHub azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) и изучите дополнительные ресурсы в репозитории:

-   [Примеры записных книжек Azure Databricks](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks).
- [Пример сценариев (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Дополнительные сведения см. также в [руководстве по SQL, таблицам и наборам данных Apache Spark](http://spark.apache.org/docs/latest/sql-programming-guide.html) и в [документации по Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

