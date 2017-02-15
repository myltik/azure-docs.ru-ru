---
title: "Мониторинг пула эластичных баз данных и управление им с помощью C# | Документация Майкрософт"
description: "Используйте методы разработки баз данных на C# для управлением пулом эластичных баз данных в Базе данных SQL Azure."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: f6e6ff3b-6b60-43c1-afe9-575991e38237
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: csharp
ms.workload: data-management
ms.date: 10/04/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: a6d5b0ccb1b160be4f8f3c09814202634759c8c6


---
# <a name="monitor-and-manage-an-elastic-pool-with-cx23"></a>Мониторинг пула эластичных баз данных и управление им с помощью C&#x23;
> [!div class="op_single_selector"]
> * [Портал Azure](sql-database-elastic-pool-manage-portal.md)
> * [PowerShell](sql-database-elastic-pool-manage-powershell.md)
> * [C#](sql-database-elastic-pool-manage-csharp.md)
> * [T-SQL](sql-database-elastic-pool-manage-tsql.md)
> 
> 

Узнайте, как управлять [пулом эластичных баз данных](sql-database-elastic-pool.md) с помощью языка C&#x23;. 

> [!NOTE]
> Многие новые функции базы данных SQL поддерживаются только при использовании [модели развертывания с помощью Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Поэтому всегда используйте последнюю **библиотеку управления базами данных SQL Azure для .NET ([документы](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [пакет NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. Более ранние [библиотеки на основе классической модели развертывания](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) поддерживаются только для обратной совместимости. Поэтому советуем использовать более новые библиотеки на основе Resource Manager.
> 
> 

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Пул эластичных БД (пул, которым вы хотите управлять). Создание пула описано в статье [Создание эластичного пула с помощью C#](sql-database-elastic-pool-create-csharp.md).
* приведенному. Бесплатный экземпляр Visual Studio см. на странице [Загрузки Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).

## <a name="move-a-database-into-an-elastic-pool"></a>Перемещение базы данных в пул эластичных БД
Автономную базу данных можно переместить в пул и из него.  

    // Retrieve current database properties.

    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database.
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

## <a name="list-databases-in-an-elastic-pool"></a>Получение списка баз данных в пуле эластичных БД
Для получения всех баз данных в пуле вызовите метод [ListDatabases](https://msdn.microsoft.com/library/microsoft.azure.management.sql.elasticpooloperationsextensions.listdatabases) .

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## <a name="change-performance-settings-of-a-pool"></a>Изменение параметров производительности пула
Получите существующие свойства пула. Измените значения и выполните метод CreateOrUpdate.

    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## <a name="latency-of-elastic-pool-operations"></a>Задержка операций эластичного пула
* Изменение минимального или максимального числа eDTU для базы данных обычно завершается за&5; минут или быстрее.
* Время изменения размера пула (eDTU) зависит от общего размера всех баз данных в пуле. Изменение занимает порядка 90 минут или меньше на каждые 100 ГБ. Например, если общее пространство, используемое всеми базами данных в пуле, равно 200 ГБ, то ожидаемая задержка при изменении числа eDTU для пула составит до 3 часов.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Коды ошибок SQL для клиентских приложений Базы данных SQL: ошибки подключения к базе данных и другие проблемы](sql-database-develop-error-messages.md).
* [База данных SQL](https://azure.microsoft.com/documentation/services/sql-database/)
* [API управления ресурсами](https://msdn.microsoft.com/library/azure/dn948464.aspx)
* [Создание эластичного пула с помощью C#](sql-database-elastic-pool-create-csharp.md)
* [Когда следует использовать эластичный пул?](sql-database-elastic-pool-guidance.md)
* Ознакомьтесь с разделом [Масштабирование базы данных SQL Azure](sql-database-elastic-scale-introduction.md). В нем описывается использование инструментов эластичной базы данных для масштабирования, перемещения данных, выполнения запросов и создания транзакций.




<!--HONumber=Dec16_HO2-->


