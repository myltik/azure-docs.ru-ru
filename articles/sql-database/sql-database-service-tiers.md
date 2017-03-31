---
title: "Уровни служб. Производительность базы данных SQL | Документация Майкрософт"
description: "Сравнение уровней служб базы данных SQL."
keywords: "параметры базы данных, производительность базы данных"
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
wms.date: 03/06/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: ab637f9910523cc8d8967dd1507dbcfad9f7ae88
ms.lasthandoff: 03/28/2017


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>Параметры базы данных SQL и производительность: возможности разных уровней служб

[База данных SQL Azure](sql-database-technical-overview.md) предлагает четыре уровня служб: **Базовый**, **Стандартный**, **Премиум** и **Premium RS**. Каждый из них обеспечивает несколько уровней производительности для разных рабочих нагрузок. Чем выше уровень производительности, тем больше он предоставляет ресурсов, предназначенных для повышения пропускной способности. Вы можете динамически изменять уровни служб и уровни производительности без простоев в работе. По условиям SLA, все уровни службы — "Базовый", "Стандартный" и "Премиум" — включают такие преимущества, как бесперебойная работа в течение 99,99 %, гибкие способы обеспечения непрерывности бизнес-процессов, функции безопасности и почасовая тарификация. Уровень Premium RS обеспечивает такие же уровни производительности, функции безопасности и возможности обеспечения непрерывности бизнеса, как и уровень "Премиум", но с меньшими значениями в соглашении об уровне обслуживания.

> [!IMPORTANT]
> Базы данных уровня Premium RS работают с меньшим числом избыточных копий, чем базы данных уровня "Премиум" или "Стандартный". Таким образом в случае сбоя службы может потребоваться восстановить базу данных из резервной копии с задержкой до 5 минут.
>

Вы можете создавать отдельные базы данных с выделенными ресурсами на уровне служб с выбранным [уровнем производительности](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels). Можно также создавать базы данных в [эластичном пуле](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus), где ресурсы совместно используются несколькими базами данных. Ресурсы, доступные для отдельных баз данных, выражаются в единицах транзакций базы данных (DTU), а ресурсы для эластичных пулов — в единицах транзакций эластичной базы данных (eDTU). Дополнительные сведения о единицах DTU см. в статье [Общие сведения об обычных единицах передачи данных (DTU) и единицах передачи данных в эластичной базе данных (eDTU)](sql-database-what-is-a-dtu.md). 

## <a name="choosing-a-service-tier"></a>Выбор уровня службы
В следующей таблице приведены примеры уровней службы, которые лучше всего подходят для рабочих нагрузок различных приложений.

| Уровень служб | Целевые рабочие нагрузки |
| :--- | --- |
| **базовая;** | Лучше всего подходит для небольшой базы данных, для которой в определенный момент времени обычно выполняется одна активная операция. В качестве примера можно привести базы данных, используемые для разработки или тестирования, или небольшие редко используемые приложения. |
| **Стандартный** |Оптимальный вариант для облачных приложений с низкими или средними требованиями к производительности операций ввода-вывода, поддерживающий несколько параллельных запросов. В качестве примера можно привести рабочую группу или веб-приложения. |
| **Премиальный** | Разработан для большого объема транзакций с высокими требованиями к производительности операций ввода-вывода. Поддерживается параллельная работа множества пользователей. В качестве примера можно привести базы данных для поддержки критически важных приложений. |
| **Premium RS** | Предназначен для рабочих нагрузок, интенсивно использующих ввод-вывод, но не требующих гарантий максимальной доступности. К примерам относятся тестовые высокопроизводительные рабочие нагрузки или рабочие нагрузки анализа, для которых база данных не является системой учета. |
|||

Сначала определите, требуется ли выполнять отдельную базу данных с определенным количеством выделенных ресурсов, или нужно, чтобы группа баз данных совместно использовала пул ресурсов. Просмотрите [рекомендации по пулам эластичных БД](sql-database-elastic-pool-guidance.md). Чтобы выбрать уровень службы, сначала определите минимальный набор необходимых функций базы данных:

| **Возможности уровня служб** | **базовая;** | **Стандартный** | **Премиальный** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| Максимальный размер отдельной базы данных | 2 ГБ | 250 ГБ | 4 TБ*  | 500 ГБ  |
| Максимальный общий объем хранилища в эластичном пуле | 117 ГБ | 1200 ГБ | 750 ГБ | 750 ГБ |
| Максимальное количество баз данных на пул | 400  | 400 | 50 | 50 |
| Срок хранения резервной копии | 7 дней | 35 дней | 35 дней | 35 дней |
||||||

> [!IMPORTANT]
> Клиенты, использующие уровни производительности P11 и P15, могут использовать до 4 ТБ предоставленного хранилища без дополнительной платы. Сейчас эта возможность находится на этапе общедоступной предварительной версии и доступна в следующих регионах: восточная часть США 2, западная часть США, Западная Европа, Юго-Восточная Азия, восточная Япония, восточная Австралия, Центральная Канада и Восточная Канада. Текущие ограничения приведены в разделе [Параметры базы данных SQL и производительность: возможности разных уровней служб](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize).
>

Выбрав минимальный уровень службы, переходите к определению уровня производительности для базы данных (в единицах DTU). В качестве отправной точки можно использовать стандартные уровни производительности S2 и S3. Для баз данных с высокими требованиями к ЦП или операциям ввода-вывода рекомендуется начинать с уровней производительности "Премиум". На уровне "Премиум" предоставляется больше ресурсов ЦП и минимум в 10 раз больше операций ввода-вывода по сравнению с самой высокой производительностью на уровне "Стандартный".

## <a name="single-database-service-tiers-and-performance-levels"></a>Уровни служб для отдельной базы данных и уровни производительности
Для отдельных баз данных существует несколько уровней производительности в пределах каждого уровня обслуживания. У вас есть возможность выбирать уровень, который лучше всего соответствует требованиям рабочей нагрузки, с помощью [портала Azure](sql-database-manage-single-databases-portal.md), [PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md), [Transact-SQL](sql-database-manage-single-databases-tsql.md), C# и REST API. 

Вне зависимости от количества размещаемых баз данных, вашей базе данных будет выделен гарантированный набор ресурсов. Кроме того, ожидаемые показатели производительности этой базы данных останутся без изменений.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> Подробное описание всех строк этой таблицы уровней служб см. в разделе [Возможности и ограничения уровней служб](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).
> 

## <a name="scaling-up-or-scaling-down-a-single-database"></a>Увеличение или уменьшение масштаба отдельной базы данных

Изначально выбрав уровень служб и уровень производительности, вы сможете динамически увеличивать или уменьшать масштаб отдельной базы данных в процессе фактической работы. Если вам нужно увеличить или уменьшить масштаб, вы легко можете изменить уровни базы данных с помощью [портала Azure](sql-database-manage-single-databases-portal.md), [PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md), [Transact-SQL](sql-database-manage-single-databases-tsql.md), C# и REST API. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

При изменении уровня службы или уровня производительности базы данных создается реплика исходной базы данных с новым уровнем производительности, после чего устанавливаются подключения к реплике. Во время этого процесса данные не теряются, но в течение короткого периода, когда производится переподключение к реплике, соединения с базой данных становятся неактивны, поэтому некоторые текущие транзакции могут откатиться. Этот период может быть разным, но обычно он не превышает 4 секунд, а в более чем 99 % случаев его длительность составляет менее 30 секунд. Если в момент отключения подключений выполняется большое количество транзакций, этот период может быть длиннее.  

Длительность всего процесса вертикального масштабирования зависит от размера и уровня службы базы данных до и после изменения. Например, если база данных размером 250 ГБ переводится с уровня службы "Стандартный", на этот уровень или в его пределах, процесс должен завершиться в течение 6 часов. Если уровень производительности базы данных того же размера меняется в пределах уровня службы "Премиум", процесс должен завершиться в течение 3 часов.

* При снижении уровня базы данных ее размер не должен превышать максимально допустимый размер целевого уровня служб. 
* При обновлении базы данных с включенной [георепликацией](sql-database-geo-replication-portal.md) перед обновлением базы данных-источника необходимо сначала обновить базы данных-получатели до требуемого уровня производительности.
* При понижении от уровня обслуживания Premium необходимо сначала завершить все активные отношения георепликации. Чтобы остановить процесс репликации между базой данных-источником и активными базами данных-получателями, выполните действия, описанные в разделе [Восстановление после сбоя](sql-database-disaster-recovery.md) .
* Предложения службы восстановления отличаются для разных уровней служб. При переходе на более низкий уровень можно потерять возможность восстановления на момент времени или получить меньший срок хранения резервных копий. Дополнительные сведения см. в статье [Резервное копирование и восстановление Базы данных SQL Azure](sql-database-business-continuity.md).
* Новые свойства базы данных не применяются до тех пор, пока изменение не завершится.

> [!IMPORTANT]
> Подробные инструкции см. в статьях [Параметры базы данных SQL и производительность: возможности разных уровней служб](sql-database-manage-single-databases-portal.md), [Manage a single database with PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md) (Управление отдельной базой данных с помощью Powershell) и [Создание и администрирование отдельных баз данных Azure SQL с помощью Transact-SQL](sql-database-manage-single-databases-tsql.md).
>

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>Уровни службы пула эластичных баз данных и производительность в единицах eDTU

Благодаря пулам базы данных могут иметь общий доступ к ресурсам eDTU и совместно использовать их. При этом каждой базе данных в пуле не нужно назначать определенный уровень производительности. Например, отдельная база данных в пуле с уровнем обслуживания "Стандартный" может использовать от 0 до максимального количества единиц eDTU базы данных, которое вы задали в настройках пула. Пулы позволяют нескольким базам данных с меняющейся рабочей нагрузкой эффективно использовать ресурсы eDTU, доступные всему пулу. Дополнительные сведения см. в статье о [ценах и рекомендациях по производительности пула эластичных баз данных](sql-database-elastic-pool-guidance.md).

В следующей таблице описаны характеристики уровней обслуживания пула.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Каждая база данных в пуле также соответствует характеристикам отдельной базы данных для этого пула. Например, для пулов уровня "Базовый" установлено предельное количество сеансов (4800–28 800 на пул), но для отдельных баз данных в этом пуле установлено ограничение в 300 сеансов на базу данных.

## <a name="scaling-up-or-scaling-down-an-elastic-pool"></a>Увеличение или уменьшение масштаба пула эластичных баз данных

Изначально выбрав уровень служб и уровень производительности, вы сможете динамически увеличивать или уменьшать масштаб пула эластичных баз данных в процессе фактической работы. 

* Изменение минимального или максимального числа eDTU для базы данных обычно завершается за 5 минут или быстрее.
* Время изменения размера пула (eDTU) зависит от общего размера всех баз данных в пуле. Изменение занимает порядка 90 минут или меньше на каждые 100 ГБ. Например, если общее пространство, используемое всеми базами данных в пуле, равно 200 ГБ, то ожидаемая задержка при изменении числа eDTU для пула составит до 3 часов.

Подробные инструкции см. в разделе [Создание эластичного пула и управление им на портале Azure](sql-database-elastic-pool-manage-portal.md), [Создание эластичного пула и управление им с помощью PowerShell](scripts/sql-database-monitor-and-scale-pool-powershell.md), [Мониторинг пула эластичных баз данных и управление им с помощью Transact-SQL](sql-database-elastic-pool-manage-tsql.md) или [Создание и администрирование эластичного пула с помощью C](sql-database-elastic-pool-manage-csharp.md).

## <a name="creating-or-upgrading-to-4tb"></a>Создание базы данных, которой предоставляется 4 ТБ, или обновление до нее

В следующих разделах рассматриваются особенности реализации возможности использовать хранилище объемом в 4 ТБ.

### <a name="creating-in-the-azure-portal"></a>Создание на портале Azure

При создании базы данных уровня производительности P11 или P15 уже выбран параметр хранилища по умолчанию, предоставляющий 1 ТБ. Для баз данных, расположенных в одном из поддерживаемых регионов, можно увеличить максимальный размер хранилища до 4 ТБ. Во всех других регионах ползунок хранилища нельзя передвинуть. При выборе хранилища объемом в 4 ТБ цена не меняется.

### <a name="creating-using-powershell-or-transact-sql"></a>Создание с помощью PowerShell или Transact-SQL

При создании базы данных уровня производительности P11 или P15 можно задать значение maxsize, равное "1 TB" (по умолчанию) или "4 TB". Значения "1024 GB" и "4096 GB" также являются допустимыми. Если для параметра maxsize задано значение 4 ТБ, то команда create для создания подготовленной базы данных в неподдерживаемом регион завершится ошибкой.

### <a name="upgrading-to-4tb"></a>Обновление до базы данных, которой предоставляется 4 ТБ 

Для существующих баз данных уровня производительности P11 или P15, расположенных в одном из поддерживаемых регионов, можно увеличить максимальный размер хранилища (maxsize) до 4 ТБ. Это можно сделать на портале Azure, в PowerShell или с помощью Transact-SQL. В следующем примере показано, как изменить значение maxsize с помощью команды ALTER DATABASE.

```ALTER DATABASE <DatabaseName> MODIFY (MAXSIZE = 4096 GB);
```

Upgrading an existing P11 or P15 database can only be performed by a server-level principal login or by members of the dbmanager database role. 
If executed in a supported region the configuration will be updated immediately. This can be checked using the [SELECT DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx) or by inspecting the database size in the Azure portal. The database will remain online during the upgrade process. However, you will not be able to utilize the full 4 TB of storage until the actual database files have been upgraded to the new maxsize. The length of time required depends upon on the size of the database being upgraded.  

### Error messages
When creating or upgrading an P11/P15 database in an unsupported region, the create or upgrade operation will fail with the following error message: **P11 and P15 database with up to 4TB of storage are available in US East 2, West US, South East Asia, West Europe, Canada East, Canada Central, Japan East, and Australia East.**

## Current limitations of P11 and P15 databases with 4 TB maxsize

- When creating or updating a P11 or P15 database, you can only chose between 1 TB and 4 TB maxsize. Intermediate storage sizes are not currently supported.
- The 4 TB database maxsize cannot be changed to 1 TB even if the actual storage used is below 1 TB. Thus, you cannot downgrade a P11-4TB/P15-4TB to a P11-1TB/P15-1TB or a lower performance tier (e.g., to P1-P6) until we are providing additional storage options for the rest of the performance tiers. This restriction also applies to the restore and copy scenarios including point-in-time, geo-restore, long-term-backup-retention, and database copy. Once a database is configured with the 4 TB option, all restore operations of this database must be into a P11/P15 with 4 TB maxsize.
- For Active Geo-Replication scenarios:
   - Setting up a geo-replication relationship: If the primary database is P11 or P15, the secondary(ies) must also be P11 or P15; lower performance tiers will be rejected as secondaries since they are not capable of supporting 4 TB.
   - Upgrading the primary database in a geo-replication relationship: Changing the maxsize to 4 TB on a primary database will trigger the same change on the secondary database. Both upgrades must be successful for the change on the primary to take effect. Region limitations for the 4TB option apply (see above). If the secondary is in a region that does not support 4 TB, the primary will not be upgraded.
- Using the Import/Export service for loading P11-4TB/P15-4TB databases is not supported. Use SqlPackage.exe to [import](sql-database-import-sqlpackage.md) and [export](sql-database-export-sqlpackage.md) data.

## Next steps

* Learn the details of [elastic pools](sql-database-elastic-pool-guidance.md) and [price and performance considerations for elastic pools](sql-database-elastic-pool-guidance.md).
* Learn how to [Monitor, manage, and resize elastic pools](sql-database-elastic-pool-manage-portal.md) and [Monitor the performance of single databases](sql-database-single-database-monitor.md).
* Now that you know about the SQL Database tiers, try them out with a [free account](https://azure.microsoft.com/pricing/free-trial/) and learn [how to create your first SQL database](sql-database-get-started.md).
* For migration scenarios, use the [DTU Calculator](http://dtucalculator.azurewebsites.net/) to approximate the number of DTUs needed. 


