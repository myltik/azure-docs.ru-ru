---
title: Отдельная база данных SQL Azure | Документация Майкрософт
description: Управляйте уровнем служб, уровнем производительности и объемом хранилища для одной базы данных Azure SQL.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/04/2018
ms.author: carlrab
ms.openlocfilehash: c1fe162beca258fe8ec3d03ce2844c1abe3176dc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32188979"
---
# <a name="manage-resources-for-a-single-database-in-azure-sql-database"></a>Управление ресурсами отдельной базы данных в Базе данных SQL Azure

Для отдельной базы данных определяется объем ресурсов, необходимый ей для выполнения рабочей нагрузки, на уровне служб, уровень производительности и необходимый объем хранилища. 

## <a name="manage-single-database-resources-using-the-azure-portal"></a>Управление ресурсами отдельной базы данных с помощью портала Azure

Чтобы определить или изменить уровень служб, уровень производительности или объем хранилища для новой или существующей базы данных SQL Azure с помощью портала Azure, откройте окно **Настройка производительности** для этой базы данных, щелкнув **Ценовая категория (единицы масштабирования DTU)**, как показано на снимке экрана ниже. 

- Определите или измените уровень служб, выбрав его в соответствии со своей рабочей нагрузкой. 
- Определите или измените уровень производительности (**единицы DTU**) в пределах уровня служб с помощью ползунка **DTU**.
- Определите или измените объем хранилища для уровня производительности с помощью ползунка **Хранилище**. 

![Настройка уровня служб и производительности](./media/sql-database-single-database-resources/change-service-tier.png)

Щелкните **Обзор** для мониторинга и (или) отмены текущей операции.

![Отмена операции](./media/sql-database-single-database-resources/cancel-operation.png)

> [!IMPORTANT]
> Выбирая уровень служб P11 или P15, ознакомьтесь с разделом [Текущие ограничения баз данных P11 и P15 с максимальным размером 4 ТБ](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).
>

## <a name="manage-single-database-resources-using-powershell"></a>Управление ресурсами отдельной базы данных с помощью PowerShell

Чтобы определить или изменить уровни служб, уровни производительности и объем хранилища баз данных SQL Azure с помощью PowerShell, используйте приведенные командлеты PowerShell. Если вам нужно выполнить установку или обновление PowerShell, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). 

| Командлет | ОПИСАНИЕ |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Создает базу данных. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Получает одну или несколько баз данных.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Определяет свойства базы данных или перемещает ее в эластичный пул. Например, можно использовать свойство **MaxSizeBytes**, чтобы задать максимальный размер базы данных.|
|[Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity)|Получение состояния операций базы данных. |
|[Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity)|Отмена асинхронной операции обновления базы данных.|


> [!TIP]
> В этом примере скрипт PowerShell отслеживает метрики производительности базы данных, масштабирует ее до более высокого уровня производительности и создает правило генерации оповещений для одной из метрик производительности. См. дополнительные сведения о [мониторинге и масштабировании отдельной базы данных SQL с помощью PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

## <a name="manage-single-database-resources-using-the-azure-cli"></a>Управление ресурсами отдельной базы данных с помощью Azure CLI

Чтобы определить или изменить уровни служб, уровни производительности и объем хранилища баз данных SQL Azure с помощью Azure CLI, используйте следующие команды [Azure CLI для базы данных SQL](/cli/azure/sql/db). Запускайте интерфейс командной строки в браузере с помощью [Cloud Shell](/azure/cloud-shell/overview) либо [установите](/cli/azure/install-azure-cli) его на платформе macOS, Linux или Windows. Изучите дополнительные сведения о создании [эластичных пулов SQL](sql-database-elastic-pool.md) и управлении ими.

| Командлет | ОПИСАНИЕ |
| --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Создает правило брандмауэра для сервера.|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Выводит список правил брандмауэра на сервере.|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Отображает сведения о правиле брандмауэра.|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Обновляет правило брандмауэра.|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Удаляет правило брандмауэра.|
|[az sql db op list](/cli/azure/sql/db/op?#az_sql_db_op_list)|Получение списка операций, выполняемых с базой данных.|
|[az sql db op cancel](/cli/azure/sql/db/op#az_sql_db_op_cancel)|Отмена асинхронной операции с базой данных.|

> [!TIP]
> В этом примере скрипт Azure CLI масштабирует отдельную базу данных SQL Azure до другого уровня производительности после запроса на получение сведений о размере базы данных. См. дополнительные сведения об [использовании CLI для мониторинга и масштабирования отдельной базы данных SQL](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-single-database-resources-using-transact-sql"></a>Управление ресурсами отдельной базы данных с помощью Transact-SQL

Чтобы определить или изменить уровни служб, уровни производительности и объем хранилища баз данных SQL Azure с помощью Transact-SQL, используйте следующие команды Transact-SQL. Можно выполнить эти команды на портале Azure, в [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) или любой другой программе, которая может подключаться к серверу базы данных SQL Azure и передавать команды Transact-SQL. 

| Get-Help | ОПИСАНИЕ |
| --- | --- |
|[CREATE DATABASE (база данных SQL Azure)](/sql/t-sql/statements/create-database-azure-sql-database)|Создает базу данных. Для создания базы данных требуется подключение к базе данных master.|
| [ALTER DATABASE (база данных SQL Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Изменяет базу данных SQL Azure. |
|[sys.database_service_objectives (база данных SQL Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Возвращает сведения о выпуске (уровень служб), целевой службе (ценовую категорию), а также имя эластичного пула (при наличии) для базы данных SQL Azure или хранилища данных SQL Azure. В системе базы данных master на сервере базы данных SQL Azure возвращает сведения обо всех базах данных. Для использования хранилища данных SQL Azure необходимо подключиться к базе данных master.|
|[sys.database_usage (база данных SQL Azure)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|Выводит список сведений о числе, типе и длительности существования баз данных на сервере базы данных SQL Azure.|

В следующем примере показано, как изменить максимальный размер базы данных с помощью команды ALTER DATABASE.

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

## <a name="manage-single-database-resources-using-the-rest-api"></a>Управление ресурсами отдельной базы данных с помощью REST API

Чтобы задать или изменить уровни служб, уровни производительности и объем хранилища баз данных используйте следующие команды REST API.

| Get-Help | ОПИСАНИЕ |
| --- | --- |
|[Базы данных: создание или обновление](/rest/api/sql/databases/createorupdate)|Создает новую базу данных или обновляет имеющуюся.|
|[Базы данных: получение](/rest/api/sql/databases/get)|Получает базу данных.|
|[Базы данных: получение по эластичному пулу](/rest/api/sql/databases/getbyelasticpool)|Получает базу данных в эластичном пуле.|
|[Базы данных: получение по рекомендуемому эластичному пулу](/rest/api/sql/databases/getbyrecommendedelasticpool)|Получает базу данных в рекомендуемом эластичном пуле.|
|[Базы данных: вывод списка по эластичному пулу](/rest/api/sql/databases/listbyelasticpool)|Возвращает список баз данных в пуле эластичных баз данных.|
|[Базы данных: вывод списка по рекомендуемому эластичному пулу](/rest/api/sql/databases/listbyrecommendedelasticpool)|Возвращает список баз данных в рекомендуемом эластичном пуле.|
|[Базы данных: вывод списка по серверу](/rest/api/sql/databases/listbyserver)|Возвращает список баз данных на сервере.|
|[Базы данных: обновление](/rest/api/sql/databases/update)|Обновляет имеющуюся базу данных.|
|[Operations - List](/rest/api/sql/Operations/List)|Отображение списка всех доступных операций REST API SQL.|



## <a name="next-steps"></a>Дополнительная информация

- Узнайте больше об уровнях служб, уровнях производительности и объемах хранилища в разделах [Модель приобретения на основе DTU для базы данных SQL Azure](sql-database-service-tiers-dtu.md) и [Модель приобретения на основе виртуальных ядер для базы данных SQL Azure (предварительная версия)](sql-database-service-tiers-vcore.md).
- Узнайте об [эластичных пулах](sql-database-elastic-pool.md).
- Узнайте о [лимитах, квотах и ограничениях подписок и служб Azure](../azure-subscription-service-limits.md).
