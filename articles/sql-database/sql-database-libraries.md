---
title: Библиотеки подключений для базы данных SQL | Документация Майкрософт
description: В этой статье содержатся ссылки для скачивания модулей, которые обеспечивают подключение к SQL Server и базе данных SQL с использованием разных языков программирования на стороне клиента.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: cb9bc6dc21d043bfa2d5b278eec4d91494263515
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647533"
---
# <a name="connectivity-libraries-and-frameworks-for-sql-server"></a>Библиотеки подключений и платформы для SQL Server

Ознакомьтесь с нашими [руководствами по началу работы](http://aka.ms/sqldev), чтобы быстро приступить к программированию на таких языках, как C#, Java, Node.js, PHP и Python. Затем создайте приложение с помощью SQL Server в Linux или Windows либо Docker в macOS.

В следующей таблице перечислены библиотеки подключений или *драйверы*, которые клиентские приложения могут применять с помощью различных языков для подключения и использования сервера SQL Server, работающего локально или в облаке. Эти библиотеки и драйверы можно использовать в среде Linux, Windows или Docker, а также при подключении к базе данных SQL Azure и хранилищу данных SQL Azure. 

| Язык | платформа | Дополнительные ресурсы | Загрузка | Начало работы |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET для SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Загрузить](https://www.microsoft.com/net/download/) | [Начало работы](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC Driver для SQL Server](http://msdn.microsoft.com/library/mt484311.aspx) | [Загрузить](https://go.microsoft.com/fwlink/?linkid=852460) |  [Начало работы](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Драйвер PHP SQL для SQL Server](http://msdn.microsoft.com/library/dn865013.aspx) | Операционная система: <br/> \* [Windows](https://www.microsoft.com/download/details.aspx?id=55642) <br/> \* [Linux](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) <br/> \* [macOS](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) |  [Начало работы](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu)
| Node.js | Windows, Linux, macOS | [Драйвер Node.js для SQL Server](http://msdn.microsoft.com/library/mt652093.aspx) | [Установка](https://msdn.microsoft.com/library/mt652094.aspx) |  [Начало работы](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Драйвер Python SQL](http://msdn.microsoft.com/library/mt652092.aspx) | Варианты установки: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](http://msdn.microsoft.com/library/mt763257.aspx) |  [Начало работы](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Драйвер Ruby для SQL Server](http://msdn.microsoft.com/library/mt691981.aspx) | [Установка](https://msdn.microsoft.com/library/mt711041.aspx) | [Начало работы](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Microsoft ODBC Driver for SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Загрузить](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

В следующей таблице перечислены примеры платформ объектно-реляционного сопоставления и веб-платформ, которые клиентские приложения могут использовать с сервером SQL Server, работающим локально или в облаке. Эти платформы можно использовать в Linux, Windows или Docker, а также при подключении к базе данных SQL и хранилищу данных SQL. 

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
- [Драйверы SQL Server](http://msdn.microsoft.com/library/mt654049.aspx), которые используются для подключения из клиентских приложений
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

