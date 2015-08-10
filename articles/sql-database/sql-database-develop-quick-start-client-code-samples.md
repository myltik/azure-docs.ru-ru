<properties 
	pageTitle="Простые примеры кода клиентских приложений для работы с базой данных SQL | Microsoft Azure" 
	description="В статье приведены ссылки на образцы кода и драйверы для Node.js в ОС Linux, Python в Mac OS, Java в Windows и много других полезных инструментов для клиентов базы данных SQL Azure."
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/24/2015" 
	ms.author="genemi"/>


# Простые примеры кода клиентских приложений для работы с Базой данных SQL


В этом разделе представлены ссылки на примеры кода для быстрого запуска, которые можно использовать для подключения к Базе данных SQL Azure.


- Краткие примеры подключения и запросов.
- Примеры повторного подключения: выполняются подключения и запросы, но, если возникшая ошибка классифицируется как [*временный сбой*](sql-database-develop-error-messages.md#bkmk_connection_errors) (например, истечение срока ожидания подключения), автоматически выполняется повторное подключение.


Примеры включают:


- различные языки программирования;
- ОС Windows, Linux и Mac OS в качестве сред для выполнения клиентского приложения;
- Ссылки на скачивание для необходимых драйверов подключения.
- простые примеры кода;
- Более длинные примеры, которые включают обработку временных сбоев в виде логики автоматического повтора.
- примеры кода, которые преобразуют реляционные результирующие наборы в объектно-ориентированный формат.


> [AZURE.NOTE]Подготавливаются примеры кода для других языков, ссылки на которые будут добавлены в раздел позднее.


## Клиенты в Linux


Этот раздел содержит ссылки на разделы с примерами кода для клиентских приложений, которые выполняются в системе Linux.


| Язык | Краткий пример | Пример повторной попытки | Относится к объекту |
| :-- | :-- | :-- | :-- |
| Node.js | [Tedious](sql-database-develop-nodejs-simple-linux.md) | . | . |
| Python | [FreeTDS, pymssql](sql-database-develop-python-simple-unbutu-linux.md) | . | . |
| Ruby | [FreeTDS, TinyTDS](sql-database-develop-ruby-simple-linux.md) | . | . |


## Клиенты в Mac OS


В этом разделе содержатся ссылки на разделы с примерами кода для клиентских приложений, которые предназначены для Mac OS.


| Язык | Краткий пример | Пример повторной попытки | Относится к объекту |
| :-- | :-- | :-- | :-- |
| Python | [pymssql](sql-database-develop-python-simple-mac-osx.md) | . | . |
| Ruby | [Homebrew<br/>FreeTDS, TinyTDS](sql-database-develop-ruby-simple-mac-osx.md) | . | . |


## Клиенты в Windows


Этот раздел содержит ссылки на разделы с примерами кода для клиентских приложений, которые выполняются в системе Windows.


| Язык | Краткий пример | Пример повторной попытки | Относится к объекту |
| :-- | :-- | :-- | :-- |
| C# | [ADO.NET](sql-database-develop-dotnet-simple.md) | [Пользовательский ADO.NET](sql-database-develop-csharp-retry-windows.md)<br/><br/>[ADO.NET с Enterprise Library](http://msdn.microsoft.com/library/azure/dn961167.aspx) | [ADO.NET Entity Framework](http://msdn.microsoft.com/library/azure/ff951633.aspx) |
| C++ | [Драйвер ODBC](http://msdn.microsoft.com/library/azure/hh974312.aspx) | . | . |
| Java | [Java. JDBC, JDK. Insert, Transaction, Select.](sql-database-develop-java-simple-windows.md)<br/><br/>[Java. Eclipse](sql-data-java-how-to-use-sql-database.md)<br/><br/>[Java. JDBC](http://msdn.microsoft.com/library/azure/gg715284.aspx) | . | . |
| Node.js | [msnodesql](sql-database-develop-nodejs-simple-windows.md) | . | . |
| PHP | [ODBC](sql-database-develop-php-simple-windows.md) | [Пользовательский ODBC](sql-database-develop-php-retry-windows.md) | . |
| Python | [pymssql](sql-database-develop-python-simple-windows.md) | . | . |


## См. также


- [Файлы пакетов SDK и средств для различных языков и платформ](http://azure.microsoft.com/downloads/#cmd-line-tools)
- [Библиотеки подключений для базы данных SQL и SQL Server](sql-database-libraries.md)
- [Список числовых кодов временных ошибок](sql-database-develop-error-messages.md#bkmk_connection_errors)<br/>& nbsp;
- [Разработка для базы данных SQL Azure: разделы практического руководства](http://msdn.microsoft.com/library/azure/ee621787.aspx)
- [Подключение к базе данных SQL: ссылки, рекомендации и советы по разработке](sql-database-connect-central-recommendations.md)
- [Создание первой базы данных SQL Azure](sql-database-get-started.md)

<!---HONumber=July15_HO5-->