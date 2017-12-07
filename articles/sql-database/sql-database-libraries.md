---
title: "Библиотеки подключений для базы данных SQL | Документация Майкрософт"
description: "Ссылки для скачивания модулей, которые обеспечивают подключение к SQL Server и базам данных SQL с использованием разных языков программирования клиента."
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
ms.openlocfilehash: 44c4491e7c198eec43a31480ef3df748626b7b75
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2017
---
# <a name="connectivity-libraries-and-frameworks-for-microsoft-sql-server"></a>Библиотеки подключений и платформы для Microsoft SQL Server

Ознакомьтесь с нашими [руководствами по началу работы](http://aka.ms/sqldev), чтобы быстро приступить к программированию на таких языках, как C#, Java, Node.js, PHP и Python, и создать приложение с помощью SQL Server в Linux или Windows либо Docker в macOS.

В следующей таблице перечислены библиотеки подключений или *драйверы*, которые клиентские приложения могут использовать посредством различных языков, чтобы подключаться и использовать сервер Microsoft SQL Server, работающий локально или в облаке, в среде Linux, Windows или Docker, а также подключаться и использовать базу данных SQL Azure и хранилище данных SQL Azure. 

| Язык | Платформа | Дополнительные ресурсы | Загрузить | Начало работы |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET для SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Загрузить](https://www.microsoft.com/net/download/) | [Приступая к работе](https://www.microsoft.com/en-us/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC Driver для SQL Server](http://msdn.microsoft.com/library/mt484311.aspx) | [Загрузить](https://go.microsoft.com/fwlink/?linkid=852460) |  [Приступая к работе](https://www.microsoft.com/en-us/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [PHP SQL Driver для SQL Server](http://msdn.microsoft.com/library/dn865013.aspx) | Операционная система: <br/> \* [Windows](https://www.microsoft.com/download/details.aspx?id=20098) <br/> \* [Linux](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) <br/> \* [macOS](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) |  [Приступая к работе](https://www.microsoft.com/en-us/sql-server/developer-get-started/php/ubuntu)
| Node.js | Windows, Linux, macOS | [Node.js Driver для SQL Server](http://msdn.microsoft.com/library/mt652093.aspx) | [Установка](https://msdn.microsoft.com/library/mt652094.aspx) |  [Приступая к работе](https://www.microsoft.com/en-us/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Python SQL Driver](http://msdn.microsoft.com/library/mt652092.aspx) | Варианты установки: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](http://msdn.microsoft.com/library/mt763257.aspx) |  [Приступая к работе](https://www.microsoft.com/en-us/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ruby Driver для SQL Server](http://msdn.microsoft.com/library/mt691981.aspx) | [Установка](https://msdn.microsoft.com/library/mt711041.aspx) | [Приступая к работе](https://www.microsoft.com/en-us/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Драйвер Microsoft ODBC для SQL Server](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) | [Загрузить](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) |  

В следующей таблице приведено несколько примеров платформ объектно-реляционного отображения данных (ORM) и веб-платформ, которые клиентские приложения могут использовать для работы с сервером Microsoft SQL Server, работающим локально или в облаке, в среде Linux, Windows или Docker, а также для работы с базой данных SQL Azure и хранилищем данных SQL Azure. 

| Язык | Платформа | Платформы ORM |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/en-us/ef)<br>[Entity Framework Core](https://docs.microsoft.com/en-us/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](http://hibernate.org/orm)|
| PHP | Windows, Linux | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](http://rubyonrails.org/) |
||||

## <a name="related-links"></a>Связанные ссылки
- [Драйверы SQL Server Driver](http://msdn.microsoft.com/library/mt654049.aspx) для подключения из клиентских приложений.
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

