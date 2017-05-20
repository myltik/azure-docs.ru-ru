---
title: "Библиотеки подключений для базы данных Azure для PostgreSQL | Документация Майкрософт"
description: "Приводится список всех библиотек и драйверов, с помощью которых клиентские программы могут подключиться к базе данных Azure для PostgreSQL."
keywords: "облако Azure postgresql postgres"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql - database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6bb3f393a8e9533b86919e9a1ebe3490ab838714
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Библиотеки подключений для базы данных Azure для PostgreSQL
В этом разделе приведены все библиотеки и драйверы, с помощью которых клиентские программы могут подключиться к базе данных Azure для PostgreSQL.

## <a name="client-interfaces"></a>Интерфейсы клиента
Большинство языковых клиентских библиотек для подключения к серверу PostgreSQL является внешними проектами и распространяется независимо друг от друга. Они поддерживаются на платформах Windows, Linux и Mac. Ниже перечислены некоторые популярные клиентские драйверы.
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
- Обзор службы содержится в статье [Что такое база данных Azure для PostgreSQL](overview.md).
- Дополнительные сведения о серверах см. в статье [Azure Database for PostgreSQL Servers](concepts-servers.md) (Серверы базы данных Azure для PostgreSQL).
- Сведения о создании первого сервера PostgreSQL см. в статье [Create an Azure Database for PostgreSQL in the Azure portal](quickstart-create-server-database-portal.md) (Создание базы данных Azure для PostgreSQL на портале Azure).

