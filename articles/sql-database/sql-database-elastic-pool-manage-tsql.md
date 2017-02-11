---
title: "Создание базы данных SQL Azure или ее перемещение в пул эластичных БД с помощью T-SQL | Документация Майкрософт"
description: "С помощью языка T-SQL можно создать базу данных SQL Azure в пуле эластичных БД либо переместить базу данных в пул или обратно."
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 4e288e17-bc3e-4255-9fbe-0a2ac0dbd7dd
ms.service: sql-database
ms.custom: sharded databases pool; how to
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 05/27/2016
ms.author: srinia
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 17ec1ad8218824048179e32ae5059b6a6d77a367


---
# <a name="monitor-and-manage-an-elastic-database-pool-with-transact-sql"></a>Мониторинг пула эластичных баз данных и управление им с помощью Transact-SQL
> [!div class="op_single_selector"]
> * [Портал Azure](sql-database-elastic-pool-manage-portal.md)
> * [PowerShell](sql-database-elastic-pool-manage-powershell.md)
> * [C#](sql-database-elastic-pool-manage-csharp.md)
> * [T-SQL](sql-database-elastic-pool-manage-tsql.md)
>  

Для создания и перемещения баз данных в эластичные пулы и обратно используйте команды [Create Database (база данных SQL Azure)](https://msdn.microsoft.com/library/dn268335.aspx) и [Alter Database (база данных SQL Azure)](https://msdn.microsoft.com/library/mt574871.aspx). Прежде чем использовать эти команды, следует создать пул эластичных БД. Эти команды влияют только на базы данных. С помощью команд T-SQL нельзя внести изменения при создании новых пулов и настройке свойств пула (таких как минимальное и максимальное количество eDTU).

## <a name="create-a-new-database-in-an-elastic-pool"></a>Создание новой базы данных внутри пула эластичных БД
Используйте команду CREATE DATABASE с параметром SERVICE_OBJECTIVE.   

    CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
    -- Create a database named db1 in a pool named S3M100.

Все базы данных в пуле эластичных БД наследуют уровень служб пула эластичных БД ("Базовый", "Стандартный" или "Премиум"). 

## <a name="move-a-database-between-elastic-pools"></a>Перемещение базы данных между пулами эластичных БД
Используйте команду ALTER DATABASE с инструкцией MODIFY и параметром SERVICE\_OBJECTIVE, для которого задано значение ELASTIC\_POOL. Для параметра name задайте имя целевого пула.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
    -- Move the database named db1 to a pool named P1M125  

## <a name="move-a-database-into-an-elastic-pool"></a>Перемещение базы данных в пул эластичных БД
Используйте команду ALTER DATABASE с инструкцией MODIFY и параметром SERVICE\_OBJECTIVE, для которого задано значение ELASTIC_POOL. Для параметра name задайте имя целевого пула.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
    -- Move the database named db1 to a pool named S3100.

## <a name="move-a-database-out-of-an-elastic-pool"></a>Перемещение базы данных из пула эластичных БД
Используйте команду ALTER DATABASE и задайте для параметра SERVICE_OBJECTIVE уровень производительности (S0, S1 и т. д.).

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
    -- Changes the database into a stand-alone database with the service objective S1.

## <a name="list-databases-in-an-elastic-pool"></a>Получение списка баз данных в пуле эластичных БД
Чтобы получить список всех баз данных в пуле эластичных БД, используйте представление [sys.database\_service\_objectives](https://msdn.microsoft.com/library/mt712619). Войдите в базу данных master, чтобы отправить запрос на представление.

    SELECT d.name, slo.*  
    FROM sys.databases d 
    JOIN sys.database_service_objectives slo  
    ON d.database_id = slo.database_id
    WHERE elastic_pool_name = 'MyElasticPool'; 

## <a name="get-resource-usage-data-for-a-pool"></a>Получение данных об использовании ресурсов в пуле
Проанализировать статистику использования ресурсов в пуле эластичных БД на логическом сервере можно в [представлении sys.elastic\_pool \_resource\_stats](https://msdn.microsoft.com/library/mt280062.aspx). Войдите в базу данных master, чтобы отправить запрос на представление.

    SELECT * FROM sys.elastic_pool_resource_stats 
    WHERE elastic_pool_name = 'MyElasticPool'
    ORDER BY end_time DESC;

## <a name="get-resource-usage-for-an-elastic-database"></a>Получение данных об использовании ресурсов в эластичной базе данных
Проанализировать статистику использования ресурсов базы данных в эластичном пуле можно в [представлении sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) или [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx). Этот процесс аналогичен отправке запроса на использование ресурсов для любой отдельной базы данных.

## <a name="next-steps"></a>Дальнейшие действия
После создания эластичного пула вы можете управлять эластичными базами данных в пуле путем создания заданий обработки эластичных БД. Эластичные задания позволяют легко и быстро выполнять сценарии T-SQL в любом количестве баз данных в пуле. Дополнительные сведения можно узнать в статье [Обзор службы заданий эластичной базы данных](sql-database-elastic-jobs-overview.md). 

Ознакомьтесь с разделом [Масштабирование базы данных SQL Azure](sql-database-elastic-scale-introduction.md). В нем описывается использование инструментов эластичной базы данных для масштабирования, перемещения данных, выполнения запросов и создания транзакций.




<!--HONumber=Nov16_HO3-->


