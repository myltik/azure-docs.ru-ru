---
title: "Неподдерживаемые функции Transact-SQL в базе данных SQL Azure | Документация Майкрософт"
description: "Инструкции Transact-SQL, которые не полностью поддерживаются в базе данных SQL Azure"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: c05abd9e-28a7-4c97-9bdf-bc60d08fc92e
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/28/2016
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 3f9077733725174f1eed61d37d544e4f36822f6e
ms.openlocfilehash: d935571ccd18bc15baa000fb8c07fed11b66ba6c


---
# <a name="azure-sql-database-transact-sql-differences"></a>Отличия Transact-SQL базы данных SQL Azure   
Большинство функций Transact-SQL, используемых приложениями, поддерживаются как в Microsoft SQL Server, так и в базе данных SQL Azure. Например, основные компоненты SQL, такие как типы данных, операторы, строковые, арифметические, логические функции, функции работы с курсорами и т. д., работают так же, как и на сервере SQL Server.

## <a name="why-some-transact-sql-is-not-supported"></a>Причины отсутствия поддержки некоторых функций Transact-SQL
База данных SQL Azure предназначена для изоляции функции от любых зависимостей в базе данных master и операционной системе. Поэтому многие действия на уровне сервера не подходят для базы данных SQL. Инструкции Transact-SQL, как правило, недоступны, если они используются для настройки параметров серверного уровня, компонентов операционной системы, а также для задания конфигурации файловой системы. Функции, недоступные в пользовательской базе данных, всегда можно заменить соответствующими возможностями, доступными в базе данных SQL или другой службе (компоненте) Azure. 

Например, группы доступности AlwaysOn можно заменить активной георепликацией. По этой причине в базе данных SQL не поддерживаются все инструкции Transact-SQL, связанные с группами доступности, и динамические административные представления, связанные с AlwaysOn.  

Список поддерживаемых и неподдерживаемых в базе данных SQL функций см. в статье [Отличия Transact-SQL базы данных SQL Azure](sql-database-features.md).

Синтаксис, устаревший в SQL Server, не поддерживается в базе данных SQL.

## <a name="transact-sql-syntax-partially-supported-in-sql-database"></a>Синтаксис Transact-SQL, который частично поддерживается в базе данных SQL
База данных SQL поддерживает некоторые, но не все аргументы, имеющиеся в соответствующих инструкциях Transact-SQL в SQL Server 2016. Например, инструкция `CREATE PROCEDURE` доступна, однако все параметры `CREATE PROCEDURE` недоступны. Описание полного синтаксиса в этой статье будет излишним и может запутать пользователей. Подробные сведения о поддерживаемых параметрах каждой инструкции см. в разделах, посвященных синтаксису, ссылки на которые приведены ниже.

- Базы данных: [CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER DATABASE](https://msdn.microsoft.com/library/ms174269.aspx).   
- Функции: [CREATE](https://msdn.microsoft.com/library/ms186755.aspx)/[ALTER FUNCTION](https://msdn.microsoft.com/library/ms186967.aspx).   
- Входы: [CREATE](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx).   
- Хранимые процедуры: [CREATE](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx).   
- Таблицы: [CREATE](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx).   
- Типы (пользовательские): [CREATE TYPE](https://msdn.microsoft.com/library/ms175007.aspx)   
- Пользователи: [CREATE](https://msdn.microsoft.com/library/ms173463.aspx)/[ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx).   
- Представления: [CREATE](https://msdn.microsoft.com/library/ms187956.aspx)/[ALTER VIEW](https://msdn.microsoft.com/library/ms173846.aspx).   

## <a name="transact-sql-syntax-not-supported-in-sql-database"></a>Синтаксис Transact-SQL, не поддерживаемый в базе данных SQL   
Помимо неподдерживаемых инструкций Transact-SQL, описанных в статье с [общими сведениями о базе данных SQL Azure, ее функциях, а также с рекомендациями и указаниями по работе с ней](sql-database-features.md), не поддерживаются также следующие инструкции и группы инструкций.
- Параметры сортировки системных объектов
- Связанные с подключением: инструкции ENDPOINT, `ORIGINAL_DB_NAME`. База данных SQL не поддерживает аутентификацию Windows, но поддерживает аналогичную аутентификацию Azure Active Directory. Для некоторых типов аутентификации требуется последняя версия SSMS. Дополнительные сведения см. в статье [Подключение к базе данных SQL или хранилищу данных SQL c использованием проверки подлинности Azure Active Directory](sql-database-aad-authentication.md).
- Межбазовые запросы с помощью имен из трех или четырех частей. (Межбазовые запросы только для чтения поддерживаются с помощью [запроса к эластичной базе данных](sql-database-elastic-query-overview.md).)
- Межбазовые цепочки владения, параметр `TRUSTWORTHY`.
- `DATABASEPROPERTY` (вместо него следует использовать `DATABASEPROPERTYEX`).
- `EXECUTE AS LOGIN` (вместо него следует использовать EXECUTE AS USER).
- Шифрование не поддерживается, за исключением расширенного управления ключами.
- Отправка событий: события, уведомления о событиях, уведомления о запросах
- Синтаксис, связанный с размещением файла базы данных и его размером, и файлы базы данных, которые автоматически управляются Microsoft Azure.
- Синтаксис, связанный с обеспечением высокой доступности, для управления которым используется учетная запись Microsoft Azure. Например, синтаксис, используемый для резервного копирования, восстановления, AlwaysOn, зеркального отображения базы данных, доставки журналов и режимов восстановления.
- Синтаксис, который зависит от средства чтения журнала, недоступного в базе данных SQL: принудительная репликация, запись измененных данных. База данных SQL может выступать подписчиком принудительной репликации.
- Синтаксис, который зависит от агента SQL Server или базы данных MSDB: оповещения, операторы, центральные серверы управления. Вместо этого используйте скрипты, например Azure PowerShell.
- Функции: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`.
- Глобальные временные таблицы
- Синтаксис, относящийся к параметрам сервера, связанного с оборудованием: память, рабочие потоки, фиксирование потоков на ЦП, флаги трассировки и т. д. Вместо них используйте уровни обслуживания.
- `HAS_DBACCESS`
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE`, `BULK INSERT` и четырехкомпонентные имена.
-  [Интеграция SQL Server со средой CLR](http://msdn.microsoft.com/library/ms254963.aspx)
- Семантический поиск
- Учетные данные сервера. Вместо них используйте учетные данные базы данных.
- Элементы серверного уровня: роли сервера, `IS_SRVROLEMEMBER`, `sys.login_token`. Элементы `GRANT`, `REVOKE` и `DENY` в разрешениях серверного уровня недоступны, хотя некоторые заменяются разрешениями уровня базы данных. Некоторые полезные динамические административные представления серверного уровня имеют эквивалентные динамические административные представления уровня базы данных.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Параметры `sp_configure` и инструкция `RECONFIGURE`. Некоторые параметры доступны в инструкции [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- Аудит SQL Server. Вместо него используйте аудита Базы данных SQL.
- Трассировка SQL Server
- Флаги трассировки. Некоторые элементы флагов трассировки были перемещены в режимы совместимости.
- Отладка Transact-SQL
- Триггеры: уровня сервера или триггеры входа
- Инструкция `USE`. Чтобы изменить контекст базы данных на другую базу данных, необходимо создать подключение к новой базе данных.

## <a name="full-transact-sql-reference"></a>Полный справочник по Transact-SQL
Дополнительные сведения о грамматике языка Transact-SQL, его использовании и примеры см. в [справочнике по Transact-SQL (компонент Database Engine)](https://msdn.microsoft.com/library/bb510741.aspx) в электронной документации по SQL Server. 

### <a name="about-the-applies-to-tags"></a>Сведения о тегах "Относится к"
Справочник по Transact-SQL включает в себя разделы, относящиеся к версиям SQL Server с 2008 по текущую. Под заголовком раздела есть панель значков, на которой указаны четыре платформы SQL Server и их применимость. Например, группы доступности появились в SQL Server 2012. Статья [CREATE AVAILABILTY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) указывает, что эта инструкция применяется в **SQL Server (начиная с версии 2012)**. Инструкция не применяется в SQL Server 2008, SQL Server 2008 R2, Базе данных SQL Azure, хранилище данных SQL Azure или Parallel Data Warehouse.

В некоторых случаях функция или инструкция, рассматриваемая в разделе, может использоваться в продукте, однако по-разному поддерживаться в разных продуктах. Эти различия отмечаются по мере изложения материала.



<!--HONumber=Nov16_HO5-->


