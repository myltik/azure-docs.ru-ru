---
title: "Библиотеки подключений для базы данных SQL | Документация Майкрософт"
description: "Ссылки для загрузки модулей, которые разрешают соединение с SQL Server и базы данных SQL с различными языками программирования клиента."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: genemi
ms.assetid: 13d899d3-cf46-4e4d-8919-cf4b41ca836d
ms.service: sql-database
ms.custom: develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: genemi
ms.openlocfilehash: 012acd2b53fc9205511530d3cc30803dceef88a0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="connectivity-libraries-and-frameworks-for-sql-server"></a>Библиотек подключений и платформы для SQL Server

Извлечение нашей [получить руководства по работы](http://aka.ms/sqldev) быстро приступить к языки программирования типа C#, Java, Node.js, PHP и Python. Выполните сборку приложения с помощью SQL Server в Linux или Windows или Docker на macOS.

В следующей таблице перечислены библиотек подключений или *драйверы* , клиентские приложения можно использовать из множества языков для подключения и использовать SQL Server, работающий локально или в облаке. Можно использовать их в Linux, Windows или Docker и использовать их для подключения к базе данных SQL Azure и хранилище данных SQL Azure. 

| Язык | платформа | Дополнительные ресурсы | Загрузка | Начало работы |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET для SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Загрузить](https://www.microsoft.com/net/download/) | [Начало работы](https://www.microsoft.com/en-us/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Драйвер Microsoft JDBC для SQL Server](http://msdn.microsoft.com/library/mt484311.aspx) | [Загрузить](https://go.microsoft.com/fwlink/?linkid=852460) |  [Начало работы](https://www.microsoft.com/en-us/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Драйвера PHP SQL для SQL Server](http://msdn.microsoft.com/library/dn865013.aspx) | Операционная система: <br/> \* [Windows](https://www.microsoft.com/download/details.aspx?id=20098) <br/> \* [Linux](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) <br/> \* [macOS](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) |  [Начало работы](https://www.microsoft.com/en-us/sql-server/developer-get-started/php/ubuntu)
| Node.js | Windows, Linux, macOS | [Драйвер node.js для SQL Server](http://msdn.microsoft.com/library/mt652093.aspx) | [Установка](https://msdn.microsoft.com/library/mt652094.aspx) |  [Начало работы](https://www.microsoft.com/en-us/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Драйвер Python SQL](http://msdn.microsoft.com/library/mt652092.aspx) | Варианты установки: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](http://msdn.microsoft.com/library/mt763257.aspx) |  [Начало работы](https://www.microsoft.com/en-us/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Драйвер Ruby для SQL Server](http://msdn.microsoft.com/library/mt691981.aspx) | [Установка](https://msdn.microsoft.com/library/mt711041.aspx) | [Начало работы](https://www.microsoft.com/en-us/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Драйвер Microsoft ODBC для SQL Server](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) | [Загрузить](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) |  

В следующей таблице перечислены примеры платформ объектно реляционного сопоставления (ORM) и веб-платформ, клиентские приложения могут использовать с SQL Server, работающий локально или в облаке. Можно использовать платформы в Linux, Windows или Docker и использовать их для подключения к базе данных SQL и хранилищем данных SQL. 

| Язык | платформа | Платформы ORM |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](http://hibernate.org/orm)|
| PHP | Windows, Linux | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](http://rubyonrails.org/) |
||||

## <a name="related-links"></a>Связанные ссылки
- [Драйверы для SQL Server](http://msdn.microsoft.com/library/mt654049.aspx) , которые используются для подключения из клиентских приложений
- Подключение к базе данных SQL:
    - [Подключение к базе данных SQL с помощью .NET (C#)](sql-database-connect-query-dotnet.md)
    - [Подключение к Базе данных SQL с помощью PHP](sql-database-connect-query-php.md)
    - [Подключение к базе данных SQL с помощью Node.js](sql-database-connect-query-nodejs.md)
    - [Подключение к Базе данных SQL с помощью Java](sql-database-connect-query-java.md)
    - [Подключение к базе данных SQL с помощью Python](sql-database-connect-query-python.md)
    - [Подключение к базе данных SQL с помощью Ruby](sql-database-connect-query-ruby.md)
- Примеры кода логики повторов:
    - [Step 4: Connect resiliently to SQL with ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n] (Шаг 4. Выполнение устойчивого подключения к SQL с помощью ADO.NET)
    - [Step 4: Connect resiliently to SQL with PHP][step-4-connect-resiliently-to-sql-with-php-p42h] (Шаг 4. Выполнение устойчивого подключения к SQL с помощью PHP)


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

