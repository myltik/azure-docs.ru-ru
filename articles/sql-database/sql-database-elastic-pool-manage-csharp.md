---
title: "Создание и администрирование пула эластичных баз данных SQL Azure с помощью C# | Документация Майкрософт"
description: "Используйте методы разработки баз данных на C# для управлением пулом эластичных баз данных в Базе данных SQL Azure."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: f6e6ff3b-6b60-43c1-afe9-575991e38237
ms.service: sql-database
ms.custom: scale out apps
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: csharp
ms.workload: data-management
ms.date: 10/04/2016
ms.author: sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: e155891ff8dc736e2f7de1b95f07ff7b2d5d4e1b
ms.openlocfilehash: b34cfe18ac3d03802173605f5483879217d1fe1f
ms.contentlocale: ru-ru
ms.lasthandoff: 05/02/2017


---
# <a name="create-and-manage-an-elastic-pool-with-cx23"></a>Создание и администрирование эластичного пула с помощью C#

В этой статье показано, как с помощью C# создавать и администрировать масштабируемые [эластичные пулы](sql-database-elastic-pool.md). Вы также можете создать эластичный пул Azure и управлять им с помощью [портала Azure](https://portal.azure.com/), [PowerShell](sql-database-elastic-pool-manage-powershell.md) или REST API. Вы также можете использовать [Transact-SQL](sql-database-elastic-pool-manage-tsql.md), чтобы создавать новые базы данных в эластичных пулах и перемещать базы данных в пулы и обратно.

> [!NOTE]
> Многие новые функции базы данных SQL поддерживаются только при использовании [модели развертывания с помощью Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Поэтому всегда используйте последнюю **библиотеку управления базами данных SQL Azure для .NET ([документы](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [пакет NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. Более ранние [библиотеки на основе классической модели развертывания](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) поддерживаются только для обратной совместимости. Поэтому советуем использовать более новые библиотеки на основе Resource Manager.
> 

В этой статье показано, как создавать и администрировать эластичные пулы SQL Azure с помощью C# и [библиотеки баз данных SQL Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). Дополнительные сведения см. в [руководстве по созданию базы данных SQL с помощью C# и библиотеки базы данных SQL для .NET](sql-database-get-started-csharp.md).

Библиотека базы данных SQL Azure для .NET предоставляет API на основе [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md), который создает оболочку для [REST API базы данных SQL на основе Resource Manager](https://msdn.microsoft.com/library/azure/mt163571.aspx).

> [!NOTE]
> Многие новые функции базы данных SQL поддерживаются только при использовании [модели развертывания с помощью Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Поэтому всегда используйте последнюю **библиотеку управления базами данных SQL Azure для .NET ([документы](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [пакет NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. Более ранние [библиотеки на основе классической модели развертывания](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) поддерживаются только для обеспечения обратной совместимости. Поэтому мы рекомендуем использовать более новые библиотеки на основе Resource Manager.
> 

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

* Подписка Azure. Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **Бесплатная учетная запись**. Оформив подписку, вернитесь к этой статье.
* приведенному. Бесплатный экземпляр Visual Studio см. на странице [Скачиваемые файлы для Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).

## <a name="create-a-console-app-and-install-the-required-libraries"></a>Создание консольного приложения и установка необходимых библиотек
1. Запустите Visual Studio.
2. Выберите **Файл** > **Создать** > **Проект**.
3. Создайте на языке C# **консольное приложение** и присвойте ему имя *SqlElasticPoolConsoleApp*

## <a name="create-a-sql-database"></a>Создание базы данных SQL

Чтобы создать базу данных SQL с помощью C#, загрузите необходимые библиотеки управления (используя [консоль диспетчера пакетов](http://docs.nuget.org/Consume/Package-Manager-Console)):

1. Выберите **Инструменты** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.
2. Введите `Install-Package Microsoft.Azure.Management.Sql -Pre` , чтобы установить [библиотеку управления Microsoft Azure SQL](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).
3. Введите `Install-Package Microsoft.Azure.Management.ResourceManager -Pre` , чтобы установить [библиотеку управления Microsoft Azure Resource Manager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager).
4. Введите `Install-Package Microsoft.Azure.Common.Authentication -Pre` , чтобы установить [библиотеку управления общей проверки подлинности Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Common.Authentication). 

> [!NOTE]
> Примеры в этой статье используют синхронную форму каждого запроса API и блокируют до завершения вызова REST на базовой службе. Доступны асинхронные методы.
>  

## <a name="create-a-sql-elastic-pool---c-example"></a>Пример создания эластичного пула с помощью C#
В следующем примере создается группа ресурсов, сервер, правило брандмауэра, пул эластичных баз данных и входящая в него база данных SQL. Чтобы получить переменные `_subscriptionId, _tenantId, _applicationId, and _applicationSecret`, см. раздел [Создание субъекта-службы для доступа к ресурсам](#create-a-service-principal-to-access-resources).

Замените содержимое файла **Program.cs** приведенным ниже кодом и обновите `{variables}`, используя значения для своего приложения (удалите `{}`).

```
using Microsoft.Azure;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.Azure.Management.ResourceManager.Models;
using Microsoft.Azure.Management.Sql;
using Microsoft.Azure.Management.Sql.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace SqlElasticPoolConsoleApp
{
    class Program
        {

        // For details about these four (4) values, see
        // https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/
        static string _subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _tenantId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationSecret = "{your-password}";

        // Create management clients for the Azure resources your app needs to work with.
        // This app works with Resource Groups, and Azure SQL Database.
        static ResourceManagementClient _resourceMgmtClient;
        static SqlManagementClient _sqlMgmtClient;

        // Authentication token
        static AuthenticationResult _token;

        // Azure resource variables
        static string _resourceGroupName = "{resource-group-name}";
        static string _resourceGrouplocation = "{Azure-region}";

        static string _serverlocation = _resourceGrouplocation;
        static string _serverName = "{server-name}";
        static string _serverAdmin = "{server-admin-login}";
        static string _serverAdminPassword = "{server-admin-password}";

        static string _firewallRuleName = "{firewall-rule-name}";
        static string _startIpAddress = "{0.0.0.0}";
        static string _endIpAddress = "{255.255.255.255}";

        static string _poolName = "{pool-name}";
        static string _poolEdition = "{Standard}";
        static int _poolDtus = {100};
        static int _databaseMinDtus = {10};
        static int _databaseMaxDtus = {100};

        static string _databaseName = "{elasticdbfromcsarticle}";



        static void Main(string[] args)
        {
            // Authenticate:
            _token = GetToken(_tenantId, _applicationId, _applicationSecret);
            Console.WriteLine("Token acquired. Expires on:" + _token.ExpiresOn);

            // Instantiate management clients:
            _resourceMgmtClient = new ResourceManagementClient(new Microsoft.Rest.TokenCredentials(_token.AccessToken));
            _sqlMgmtClient = new SqlManagementClient(new TokenCloudCredentials(_subscriptionId, _token.AccessToken));


            Console.WriteLine("Resource group...");
            ResourceGroup rg = CreateOrUpdateResourceGroup(_resourceMgmtClient, _subscriptionId, _resourceGroupName, _resourceGrouplocation);
            Console.WriteLine("Resource group: " + rg.Id);


            Console.WriteLine("Server...");
            ServerGetResponse sgr = CreateOrUpdateServer(_sqlMgmtClient, _resourceGroupName, _serverlocation, _serverName, _serverAdmin, _serverAdminPassword);
            Console.WriteLine("Server: " + sgr.Server.Id);

            Console.WriteLine("Server firewall...");
            FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule(_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);
            Console.WriteLine("Server firewall: " + fwr.FirewallRule.Id);

            Console.WriteLine("Elastic pool...");
            ElasticPoolCreateOrUpdateResponse epr = CreateOrUpdateElasticDatabasePool(_sqlMgmtClient, _resourceGroupName, _serverName, _poolName, _poolEdition, _poolDtus, _databaseMinDtus, _databaseMaxDtus);
            Console.WriteLine("Elastic pool: " + epr.ElasticPool.Id);

            Console.WriteLine("Database...");
            DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _poolName);
            Console.WriteLine("Database: " + dbr.Database.Id);


            Console.WriteLine("Press any key to continue...");
            Console.ReadKey();
        }

        static ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupName, string resourceGroupLocation)
        {
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = resourceGroupLocation,
            };
            resourceMgmtClient.SubscriptionId = subscriptionId;
            ResourceGroup resourceGroupResult = resourceMgmtClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }

        static ServerGetResponse CreateOrUpdateServer(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverLocation, string serverName, string serverAdmin, string serverAdminPassword)
        {
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = "12.0"
                }
            };
            ServerGetResponse serverResult = sqlMgmtClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;
        }


        static FirewallRuleGetResponse CreateOrUpdateFirewallRule(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string firewallRuleName, string startIpAddress, string endIpAddress)
        {
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = startIpAddress,
                    EndIpAddress = endIpAddress
                }
            };
            FirewallRuleGetResponse firewallResult = sqlMgmtClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
            return firewallResult;
        }



        static ElasticPoolCreateOrUpdateResponse CreateOrUpdateElasticDatabasePool(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string poolName, string poolEdition, int poolDtus, int databaseMinDtus, int databaseMaxDtus)
        {
            // Retrieve the server that will host this elastic pool
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create elastic pool: configure create or update parameters and properties explicitly
            ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = poolEdition,
                    Dtu = poolDtus,
                    DatabaseDtuMin = databaseMinDtus,
                    DatabaseDtuMax = databaseMaxDtus
                }
            };

            // Create the pool
            var newPoolResponse = sqlMgmtClient.ElasticPools.CreateOrUpdate(resourceGroupName, serverName, poolName, newPoolParameters);
            return newPoolResponse;
        }




        static DatabaseCreateOrUpdateResponse CreateOrUpdateDatabase(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string databaseName, string poolName)
        {
            // Retrieve the server that will host this database
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    CreateMode = DatabaseCreateMode.Default,
                    ElasticPoolName = poolName
                }
            };
            DatabaseCreateOrUpdateResponse dbResponse = sqlMgmtClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }



        private static AuthenticationResult GetToken(string tenantId, string applicationId, string applicationSecret)
        {
            AuthenticationContext authContext = new AuthenticationContext("https://login.windows.net/" + tenantId);
            _token = authContext.AcquireToken("https://management.core.windows.net/", new ClientCredential(applicationId, applicationSecret));
            return _token;
        }
    }
}
```

## <a name="create-a-service-principal-to-access-resources"></a>Создание субъекта-службы для доступа к ресурсам
Следующий сценарий PowerShell создает приложение Active Directory (AD) и субъект-службу, которые необходимы для проверки подлинности нашего приложения C#. Сценарий выводит значения, необходимые для предыдущего примера на C#. Дополнительные сведения см. в статье [Использование Azure PowerShell для создания субъекта-службы и доступа к ресурсам](../azure-resource-manager/resource-group-authenticate-service-principal.md).

    # Sign in to Azure.
    Add-AzureRmAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




Чтобы выполнить действия, описанные в этой статье, необходимо следующее.

* Пул эластичных баз данных. Дополнительные сведения о создании эластичного пула см. в разделе [Создание и администрирование эластичного пула с помощью C#](sql-database-elastic-pool-manage-csharp.md).
* приведенному. Бесплатный экземпляр Visual Studio см. на странице [Загрузки Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).

## <a name="move-a-database-into-an-elastic-pool"></a>Перемещение базы данных в пул эластичных БД
Автономную базу данных можно перемещать в эластичный пул и из него.  

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
Чтобы получить все базы данных в эластичном пуле, вызовите метод [ListDatabases](https://msdn.microsoft.com/library/microsoft.azure.management.sql.elasticpooloperationsextensions.listdatabases).

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## <a name="change-performance-settings-of-an-elastic-pool"></a>Изменение параметров производительности эластичного пула
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
*  См. дополнительные сведения о [кодах ошибок](sql-database-develop-error-messages.md) SQL для клиентских приложений базы данных SQL, ошибках подключения к базе данных и других проблемах.
* [API управления ресурсами](https://msdn.microsoft.com/library/azure/dn948464.aspx)
* См. дополнительные сведения в руководстве по [использованию эластичного пула](sql-database-elastic-pool.md).


