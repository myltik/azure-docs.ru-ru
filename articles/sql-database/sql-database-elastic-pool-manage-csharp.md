---
title: Мониторинг пула эластичных баз данных и управление им с помощью C# | Microsoft Docs
description: Используйте методы разработки баз данных на C# для управлением пулом эластичных баз данных в Базе данных SQL Azure.
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: csharp
ms.workload: data-management
ms.date: 10/04/2016
ms.author: sstein

---
# <a name="monitor-and-manage-an-elastic-database-pool-with-c&#x23;"></a>Мониторинг пула эластичных баз данных и управление им с помощью C&#x23;
> [!div class="op_single_selector"]
> * [Портал Azure](sql-database-elastic-pool-manage-portal.md)
> * [PowerShell](sql-database-elastic-pool-manage-powershell.md)
> * [C#](sql-database-elastic-pool-manage-csharp.md)
> * [T-SQL](sql-database-elastic-pool-manage-tsql.md)
> 
> 

Узнайте, как управлять [пулом эластичных баз данных](sql-database-elastic-pool.md) с помощью языка C&#x23;. 

> [!NOTE]
> Многие новые функции Базы данных SQL поддерживаются только при использовании [модели развертывания с помощью Azure Resource Manager](../resource-group-overview.md). Поэтому всегда используйте последнюю **библиотеку управления базами данных SQL Azure для .NET ([документы](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [пакет NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. Более ранние [библиотеки на основе классической модели развертывания](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) поддерживаются только для обратной совместимости. Поэтому советуем использовать более новые библиотеки на основе Resource Manager.
> 
> 

Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Пул эластичных БД (пул, которым вы хотите управлять). Создание пула описано в статье [Создание пула эластичных баз данных на C#](sql-database-elastic-pool-create-csharp.md).
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
* Изменение минимального или максимального числа eDTU для базы данных обычно завершается за 5 минут или быстрее.
* Время изменения размера пула (eDTU) зависит от общего размера всех баз данных в пуле. Изменение занимает порядка 90 минут или меньше на каждые 100 ГБ. Например, если общее пространство, используемое всеми базами данных в пуле, равно 200 ГБ, то ожидаемая задержка при изменении числа eDTU для пула составит до 3 часов.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Коды ошибок SQL для клиентских приложений Базы данных SQL: ошибки подключения к базе данных и другие проблемы](sql-database-develop-error-messages.md).
* [База данных SQL](https://azure.microsoft.com/documentation/services/sql-database/)
* [API управления ресурсами](https://msdn.microsoft.com/library/azure/dn948464.aspx)
* [Создание нового пула эластичных баз данных с помощью C#](sql-database-elastic-pool-create-csharp.md)
* [Когда следует использовать пул эластичных баз данных?](sql-database-elastic-pool-guidance.md)
* Ознакомьтесь с разделом [Масштабирование базы данных SQL Azure](sql-database-elastic-scale-introduction.md). В нем описывается использование инструментов эластичной базы данных для масштабирования, перемещения данных, выполнения запросов и создания транзакций.

<!--HONumber=Oct16_HO2-->


