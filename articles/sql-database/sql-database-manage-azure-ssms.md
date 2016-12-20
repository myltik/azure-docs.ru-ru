---
title: "Управление базой данных SQL с помощью SSMS | Документация Майкрософт"
description: "Узнайте, как использовать SQL Server Management Studio для управления серверами и базами данных Базы данных SQL."
services: sql-database
documentationcenter: .net
author: stevestein
manager: jhubbard
editor: tysonn
ms.assetid: da6f3608-5993-4134-a497-ff2811e9f31f
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/29/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b4dc2137ce42e3ed8e85d57dacc1acecc6de243e


---
# <a name="managing-azure-sql-database-using-sql-server-management-studio"></a>Управление базой данных SQL Azure с помощью SQL Server Management Studio
> [!div class="op_single_selector"]
> * [Портал Azure](sql-database-manage-portal.md)
> * [SSMS](sql-database-manage-azure-ssms.md)
> * [PowerShell](sql-database-manage-powershell.md)
> 
> 

SQL Server Management Studio (SSMS) можно использовать для администрирования серверов и баз данных Базы данных SQL Azure. В этом разделе описываются типичные задачи, выполняемые с помощью SSMS. Прежде чем начать, необходимо создать сервер и базу данных в Базе данных SQL Azure. Дополнительные сведения см. в статьях [Руководство по базам данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure](sql-database-get-started.md) и [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md).

Мы советуем использовать последнюю версию SSMS при работе с Базой данных SQL Azure. 

> [!IMPORTANT]
> Всегда используйте последнюю версию SSMS, так как она постоянно совершенствуется, чтобы обеспечить совместимость с последними обновлениями в Azure и Базе данных SQL. Чтобы скачать последнюю версию, перейдите на страницу [Скачивание SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="create-and-manage-azure-sql-databases"></a>Создание баз данных SQL и управление ими
После подключения к базе данных **master** вы можете создать базы данных на сервере и изменить или удалить существующие базы данных. Ниже приведены указания по выполнению ряда типичных задач управления базами данных в среде Management Studio. Для выполнения этих задач убедитесь, что вы подключены к базе данных **master** под основной учетной записью на уровне сервера, которую вы создали при настройке сервера.

Чтобы открыть окно запросов в среде Management Studio, откройте папку "Базы данных", разверните папку **Системные базы данных**, щелкните правой кнопкой мыши базу данных **master** и выберите **Создать запрос**.

* Используйте инструкцию **CREATE DATABASE**, чтобы создать базу данных. Дополнительные сведения см. в статье [CREATE DATABASE (база данных SQL Azure)](https://msdn.microsoft.com/library/dn268335.aspx). Инструкция, приведенная ниже, создает базу данных **myTestDB** и указывает, что это база данных выпуска "Стандартный" S0 с максимальным размером 250 ГБ (по умолчанию).
  
      CREATE DATABASE myTestDB
      (EDITION='Standard',
       SERVICE_OBJECTIVE='S0');

Нажмите кнопку **Выполнить** для выполнения запроса.

* Используйте инструкцию **ALTER DATABASE**, чтобы изменить имеющуюся базу данных, если, например, вы хотите изменить имя или выпуск базы данных. Дополнительные сведения см. в статье [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/ms174269.aspx). Приведенная ниже инструкция модифицирует базу данных, которую вы создали на предыдущем шаге, чтобы изменить выпуск на Standard S1.
  
      ALTER DATABASE myTestDB
      MODIFY
      (SERVICE_OBJECTIVE='S1');
* Используйте инструкцию **DROP DATABASE**, чтобы удалить имеющуюся базу данных. Дополнительные сведения см. в статье [DROP DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/ms178613.aspx). Приведенная ниже инструкция удаляет базу данных **myTestDB**, но не удаляйте ее сейчас, потому что она будет использоваться для создания учетных записей на следующем шаге.
  
      DROP DATABASE myTestBase;
* У главной базы данных есть представление **sys.databases** , которое можно использовать для просмотра сведений обо всех базах данных. Чтобы просмотреть все существующие базы данных, выполните следующую инструкцию:
  
      SELECT * FROM sys.databases;
* В базе данных SQL инструкция **USE** не поддерживается для переключения между базами данных. Вместо нее необходимо установить подключение непосредственно к целевой базе данных.

> [!NOTE]
> Многие инструкции Transact-SQL, позволяющие создавать и изменять базы данных, должны выполняться в своем собственном пакете и не могут быть сгруппированы с другими инструкциями Transact-SQL. Дополнительные сведения см. в описании конкретной инструкции.
> 
> 

## <a name="create-and-manage-logins"></a>Создание имен входа и управление ими
База данных **master** содержит сведения об именах для входа и о том, какие из них имеют разрешение на создание баз данных или других имен для входа. Управление учетными записями выполняется через подключение к базе данных **master** под основной учетной записью на уровне сервера, созданной в процессе настройки своего сервера. Вы можете использовать инструкции **CREATE LOGIN**, **ALTER LOGIN** или **DROP LOGIN**, чтобы выполнять запросы к базе данных master, которая управляет учетными записями по всему серверу. Дополнительные сведения см. в статье [Проверка подлинности и авторизация в базе данных SQL: предоставление доступа](http://msdn.microsoft.com/library/azure/ee336235.aspx). 

* Чтобы создать имя для входа на уровне сервера, используйте инструкцию **CREATE LOGIN**. Дополнительные сведения см. в статье [CREATE LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx). Приведенная ниже инструкция создает имя для входа **login1**. Замените **password1** на собственный пароль.
  
      CREATE LOGIN login1 WITH password='password1';
* Чтобы предоставить разрешения на уровне базы данных, используйте инструкцию **CREATE USER**. Все имена для входа должны быть созданы в базе данных **master**. Чтобы подключиться с именем для входа к другой базе данных, необходимо предоставить соответствующие разрешения на уровне базы данных с помощью инструкции **CREATE USER**. Дополнительные сведения см. в статье [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx). 
* Чтобы разрешить учетной записи "login1" подключение к базе данных **myTestDB**, выполните следующие действия:
  
  1. Чтобы обновить окно обозревателя объектов и отобразить созданную базу данных **myTestDB**, щелкните правой кнопкой мыши имя сервера в обозревателе объектов, а затем щелкните **Обновить**.  
     
     Если подключение закрыто, можно подключиться повторно, выбрав **Подключить обозреватель объектов** в меню "Файл".
  2. Щелкните правой кнопкой мыши базу данных **myTestDB** и выберите **Создать запрос**.
  3. Выполните следующую инструкцию в базе данных myTestDB, чтобы создать пользователя базы данных с именем **login1User**, соответствующим учетной записи уровня сервера **login1**.
     
         CREATE USER login1User FROM LOGIN login1;
* Воспользуйтесь хранимой процедурой **sp\_addrolemember**, чтобы предоставить учетной записи пользователя соответствующий уровень полномочий в базе данных. Дополнительные сведения см. в статье [Хранимая процедура sp_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx). Приведенная ниже инструкция предоставляет **login1User** разрешение только на чтение из базы данных, добавляя **login1User** к роли **db\_datareader**.
  
      exec sp_addrolemember 'db_datareader', 'login1User';    
* Используйте инструкцию **ALTER LOGIN**, чтобы изменить имеющуюся учетную запись, если, например, вы хотите изменить пароль для нее. Дополнительные сведения см. в статье [ALTER LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189828.aspx). Инструкция **ALTER LOGIN** должна выполняться в базе данных **master**. Переключитесь в окно запроса, подключенного к этой базе данных. Приведенная ниже инструкция изменяет имя для входа **login1**, сбрасывая пароль. Замените **newPassword** на собственный пароль, а старый пароль **oldPassword** — на текущий пароль для учетной записи.
  
      ALTER LOGIN login1
      WITH PASSWORD = 'newPassword'
      OLD_PASSWORD = 'oldPassword';
* Используйте инструкцию **DROP LOGIN**, чтобы удалить имеющуюся учетную запись. При удалении учетной записи на уровне сервера удаляются и все связанные учетные записи пользователей баз данных. Дополнительные сведения см. в статье [DROP DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/ms178613.aspx). Инструкция **DROP LOGIN** должна выполняться для базы данных **master**. Приведенная ниже инструкция удаляет имя для входа **login1**.
  
      DROP LOGIN login1;
* В базе данных master предусмотрено представление **sys.sql\_logins**, которое можно использовать для просмотра учетных записей. Чтобы просмотреть все существующие учетные записи, выполните следующую инструкцию:
  
      SELECT * FROM sys.sql_logins;

## <a name="monitor-sql-database-using-dynamic-management-views"></a>Мониторинг базы данных SQL с помощью динамических представлений управления
База данных SQL поддерживает несколько динамических представлений управления, которые можно использовать для мониторинга отдельных баз данных. Ниже приведен ряд примеров типов отслеживаемых данных, которые можно получить с помощью этих представлений. Подробные сведения и дополнительные примеры см. в статье [Мониторинг базы данных SQL Azure с помощью динамических административных представлений](https://msdn.microsoft.com/library/azure/ff394114.aspx).

* Запрос динамического представления управления требует наличия разрешений **VIEW DATABASE STATE** . Чтобы предоставить разрешение **VIEW DATABASE STATE** для определенного пользователя базы данных, подключитесь к базе данных и выполните с ней следующую инструкцию.
  
      GRANT VIEW DATABASE STATE TO login1User;
* Рассчитайте размер базы данных с помощью представления **sys.dm\_db\_partition\_stats**. Представление **sys.dm\_db\_partition\_stats** возвращает сведения о странице и числе строк для каждого раздела в базе данных, позволяя рассчитать размер базы данных. Следующий запрос возвращает размер базы данных в мегабайтах:
  
      SELECT SUM(reserved_page_count)*8.0/1024
      FROM sys.dm_db_partition_stats;   
* Используйте представления **sys.dm\_exec\_connections** и **sys.dm\_exec\_sessions**, чтобы получить сведения о текущих пользовательских подключениях и внутренних задачах, связанных с базой данных. Следующий запрос возвращает сведения о текущем подключении:
  
      SELECT
          e.connection_id,
          s.session_id,
          s.login_name,
          s.last_request_end_time,
          s.cpu_time
      FROM
          sys.dm_exec_sessions s
          INNER JOIN sys.dm_exec_connections e
            ON s.session_id = e.session_id;
* Используйте представление **sys.dm\_exec\_query\_stats**, чтобы получить объединенную статистику производительности для кэшированных планов запросов. Следующий запрос возвращает сведения о пяти первых запросах, упорядоченных по среднему времени ЦП.
  
      SELECT TOP 5 query_stats.query_hash AS "Query Hash",
          SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
          MIN(query_stats.statement_text) AS "Statement Text"
      FROM
          (SELECT QS.*,
          SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
          ((CASE statement_end_offset
              WHEN -1 THEN DATALENGTH(ST.text)
              ELSE QS.statement_end_offset END
                  - QS.statement_start_offset)/2) + 1) AS statement_text
           FROM sys.dm_exec_query_stats AS QS
           CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
      GROUP BY query_stats.query_hash
      ORDER BY 2 DESC;




<!--HONumber=Nov16_HO3-->


