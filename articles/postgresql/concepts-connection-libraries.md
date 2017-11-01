---
title: "Библиотеки подключений для базы данных Azure для PostgreSQL | Документация Майкрософт"
description: "В этой статье описывается несколько библиотек и драйверов, которые разработчики могут использовать при написании кода приложений для подключения и запроса базы данных Azure для PostgreSQL."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 7d6f13336b3913299433e25c69a41f8c94f598b2
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2017
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Библиотеки подключений для базы данных Azure для PostgreSQL
В этом разделе перечисляются библиотеки и драйверы, которые разработчики могут использовать при программировании приложений для подключения к базе данных Azure для PostgreSQL и выполнения запросов к ней.

## <a name="client-interfaces"></a>Интерфейсы клиента
Большинство языковых клиентских библиотек для подключения к серверу PostgreSQL является внешними проектами и распространяется независимо друг от друга. Они поддерживаются на платформах Windows, Linux и Mac. В таблице ниже перечислены некоторые популярные клиентские драйверы.

| **Язык** | **Интерфейс клиента** | **Дополнительные сведения** | **Загрузить** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Интерфейс API базы данных, совместимый с версией 2.0 | [Загрузить](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://php.net/manual/en/book.pgsql.php) | Расширение базы данных | [Установка](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Пакет pg npm](https://www.npmjs.com/package/pg) | Клиент чистого JavaScript без блокировки | [Установка](https://www.npmjs.com/package/pg) |
| Java | [JDBC](http://jdbc.postgresql.org/) | Драйвер JDBC типа 4 | [Загрузить](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Пакет pg](https://deveiate.org/code/pg/) | Интерфейс Ruby | [Загрузить](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Пакет pq](https://godoc.org/github.com/lib/pq) | Драйвер postgres для чистого Go | [Установка](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](http://www.npgsql.org/) | Поставщик данных ADO.NET | [Загрузить](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Драйвер ODBC | [Загрузить](http://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Основной интерфейс для языка C | Включено |
| C++ | [libpqxx](http://pqxx.org/) | Новый стиль интерфейса для языка C++ | [Загрузить](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с этими краткими руководствами по подключению к базе данных Azure для PostgreSQL и выполнению запросов к ней, выбрав язык по своему усмотрению.

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
