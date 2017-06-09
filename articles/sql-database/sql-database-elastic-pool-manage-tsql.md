---
title: "T-SQL: управление пулом эластичных баз данных SQL Azure | Документация Майкрософт"
description: "Использование T-SQL для управления пулом эластичных баз данных SQL Azure."
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 4e288e17-bc3e-4255-9fbe-0a2ac0dbd7dd
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 05/27/2016
ms.author: srinia
ms.translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: a3acd5f4ec63061254b550737ae9fb4d39b343c6
ms.contentlocale: ru-ru
ms.lasthandoff: 03/10/2017


---
# <a name="monitor-and-manage-an-elastic-pool-with-transact-sql"></a>Мониторинг пула эластичных баз данных и управление им с помощью Transact-SQL
В этой статье показано, как с помощью Transact-SQL администрировать масштабируемые [эластичные пулы](sql-database-elastic-pool.md).  Для создания эластичных пулов Azure и управления ими можно также использовать [портал Azure](https://portal.azure.com/), [PowerShell](sql-database-elastic-pool-manage-powershell.md), REST API или [C#](sql-database-elastic-pool-manage-csharp.md). Вы также можете использовать [Transact-SQL](sql-database-elastic-pool-manage-tsql.md), чтобы создавать новые базы данных в эластичных пулах и перемещать базы данных в пулы и обратно.


Для создания и перемещения баз данных в эластичные пулы и обратно используйте команды [Create Database (база данных SQL Azure)](https://msdn.microsoft.com/library/dn268335.aspx) и [Alter Database (база данных SQL Azure)](https://msdn.microsoft.com/library/mt574871.aspx). Прежде чем использовать эти команды, следует создать пул эластичных БД. Эти команды влияют только на базы данных. С помощью команд T-SQL нельзя внести изменения при создании новых пулов и настройке свойств пула (таких как минимальное и максимальное количество eDTU).

## <a name="create-a-pooled-database-in-an-elastic-pool"></a>Создание базы данных внутри эластичного пула
Используйте команду CREATE DATABASE с параметром SERVICE_OBJECTIVE.   

    CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
    -- Create a database named db1 in an elastic named S3M100.

Все базы данных в пуле эластичных БД наследуют уровень служб пула эластичных БД ("Базовый", "Стандартный" или "Премиум"). 

## <a name="move-a-database-between-elastic-pools"></a>Перемещение базы данных между пулами эластичных БД
Используйте команду ALTER DATABASE с инструкцией MODIFY и параметром SERVICE\_OBJECTIVE, для которого задано значение ELASTIC\_POOL. Присвойте имя целевому пулу.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
    -- Move the database named db1 to an elastic named P1M125  

## <a name="move-a-database-into-an-elastic-pool"></a>Перемещение базы данных в пул эластичных БД
Используйте команду ALTER DATABASE с инструкцией MODIFY и параметром SERVICE\_OBJECTIVE, для которого задано значение ELASTIC_POOL. Присвойте имя целевому пулу.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
    -- Move the database named db1 to an elastic named S3100.

## <a name="move-a-database-out-of-an-elastic-pool"></a>Перемещение базы данных из пула эластичных БД
Используйте команду ALTER DATABASE и задайте для параметра SERVICE_OBJECTIVE уровень производительности (например, S0 или S1).

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
    -- Changes the database into a stand-alone database with the service objective S1.

## <a name="list-databases-in-an-elastic-pool"></a>Получение списка баз данных в пуле эластичных БД
Чтобы получить список всех баз данных в пуле эластичных БД, используйте представление [sys.database\_service\_objectives](https://msdn.microsoft.com/library/mt712619). Войдите в базу данных master, чтобы отправить запрос на представление.

    SELECT d.name, slo.*  
    FROM sys.databases d 
    JOIN sys.database_service_objectives slo  
    ON d.database_id = slo.database_id
    WHERE elastic_pool_name = 'MyElasticPool'; 

## <a name="get-resource-usage-data-for-an-elastic-pool"></a>Получение данных об использовании ресурсов в эластичном пуле
Проанализировать статистику использования ресурсов в пуле эластичных БД на логическом сервере можно в [представлении sys.elastic\_pool \_resource\_stats](https://msdn.microsoft.com/library/mt280062.aspx). Войдите в базу данных master, чтобы отправить запрос на представление.

    SELECT * FROM sys.elastic_pool_resource_stats 
    WHERE elastic_pool_name = 'MyElasticPool'
    ORDER BY end_time DESC;

## <a name="get-resource-usage-for-a-pooled-database"></a>Получение данных об использовании ресурсов в базе данных, которая находится в пуле
Проанализировать статистику использования ресурсов базы данных в эластичном пуле можно в [представлении sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) или [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx). Этот процесс аналогичен отправке запроса на использование ресурсов для любой отдельной базы данных.

## <a name="next-steps"></a>Дальнейшие действия
После создания эластичного пула вы можете управлять его эластичными базами данных путем создания эластичных заданий. Эластичные задания позволяют легко и быстро выполнять сценарии T-SQL в любом количестве баз данных в пуле. Дополнительные сведения можно узнать в статье [Обзор службы заданий эластичной базы данных](sql-database-elastic-jobs-overview.md). 

Ознакомьтесь с инструкциями по [масштабированию базы данных SQL Azure](sql-database-elastic-scale-introduction.md). Вы узнаете, как использовать средства эластичной базы данных для масштабирования, перемещения данных, выполнения запросов и создания транзакций.


