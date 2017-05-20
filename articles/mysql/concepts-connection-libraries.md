---
title: "Библиотеки подключений для базы данных Azure для MySQL | Документация Майкрософт"
description: "В этой статье приведены все библиотеки и драйверы, с помощью которых клиентские программы могут подключиться к базе данных Azure для MySQL."
services: mysql
author: wutao
ms.author: wutao
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e3f04c20dbd0329149fdd7de56338f5baaedf5ce
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---

# <a name="connection-libraries-for-azure-database-for-mysql"></a>Библиотеки подключений для базы данных Azure для MySQL
В этом разделе приведены все библиотеки и драйверы, с помощью которых клиентские программы могут подключиться к базе данных Azure для MySQL.

## <a name="client-interfaces"></a>Интерфейсы клиента
MySQL предлагает возможность подключения к базе данных с помощью стандартного драйвера для использования MySQL с приложениями и инструментами, совместимыми с отраслевыми стандартами ODBC и JDBC. Любая система, которая работает с ODBC или JDBC, может использовать MySQL.

| **Язык** | **Платформа** | **Дополнительный ресурс** | **Загрузить** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [Собственный драйвер MySQL для PHP — mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Загрузить](http://php.net/downloads.php) |
| ODBC | Платформы Windows, Linux, Mac OS X и Unix | [Руководство разработчика MySQL Connector/ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Загрузить](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Руководство разработчика MySQL Connector/Net](https://dev.mysql.com/doc/connector-net/en/) | [Загрузить](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Не зависит от платформы | [Руководство разработчика MySQL Connector/J 5.1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Загрузить](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Загрузить](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Руководство разработчика MySQL Connector/Python](https://dev.mysql.com/doc/connector-python/en/) | [Загрузить](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Руководство разработчика MySQL Connector/C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Загрузить](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Руководство разработчика MySQL Connector/C](https://dev.mysql.com/doc/connector-c/en/) | [Загрузить](https://dev.mysql.com/downloads/connector/c/)


## <a name="next-steps"></a>Дальнейшие действия
[What is Azure Database for MySQL? Service Introduction (Что такое база данных Azure для MySQL? Общие сведения о службе)](./overview.md)
[Основные понятия работы с сервером в базе данных Azure для MySQL](./concepts-servers.md)

