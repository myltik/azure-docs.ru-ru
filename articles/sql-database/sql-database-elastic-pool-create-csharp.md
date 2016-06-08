<properties
    pageTitle="Создание пула эластичных баз данных с помощью C# | Microsoft Azure"
    description="Используйте методы разработки баз данных на C# для создания пула масштабируемых эластичных баз данных в Базе данных SQL Azure, чтобы совместно использовать ресурсы нескольких баз данных."
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="05/27/2016"
    ms.author="srinia"/>

# Создание пула эластичных баз данных с помощью C&#x23;

> [AZURE.SELECTOR]
- [Портал Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Узнайте, как создавать [пулы эластичных баз данных](sql-database-elastic-pool.md) и управлять ими с помощью языка C&#x23;.

Стандартные коды ошибок см. в статье [Коды ошибок SQL для клиентских приложений базы данных SQL: ошибки подключения к базе данных и другие проблемы](sql-database-develop-error-messages.md).

Сейчас пулы эластичных баз данных предоставляются в виде предварительной версии, которая доступна только с серверами Базы данных SQL версии 12. Если у вас есть сервер базы данных SQL версии 11, с помощью PowerShell вы можете в один шаг [обновить его до версии 12 и создать пул](sql-database-upgrade-server-portal.md).

В примерах используется [библиотека базы данных SQL для .NET](https://msdn.microsoft.com/library/azure/mt349017.aspx), поэтому вам нужно ее установить. Для этого в Visual Studio выполните следующую команду в [консоли диспетчера пакетов](http://docs.nuget.org/Consume/Package-Manager-Console) (**Средства** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**).

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre

## Создание пула

Создайте экземпляр [SqlManagementClient](https://msdn.microsoft.com/library/microsoft.azure.management.sql.sqlmanagementclient), используя значения из [Azure Active Directory](sql-database-client-id-keys.md). Создайте экземпляр [ElasticPoolCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.elasticpoolcreateorupdateparameters) и вызовите метод [CreateOrUpdate](https://msdn.microsoft.com/library/microsoft.azure.management.sql.databaseoperationsextensions.createorupdate). Значения eDTU на пул, а также минимальные и максимальные значения DTU ограничены значением уровня службы ("Базовый", "Стандартный" или "Премиум"). См. раздел [eDTU и размеры хранилища для эластичных баз данных и пулов эластичных баз данных](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).


    ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = "Standard",
            Dtu = 400,
            DatabaseDtuMin = 0,
            DatabaseDtuMax = 100
         }
    };

    // Create the pool
    var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

## Создание базы данных в пуле

Создайте экземпляр [DataBaseCreateorUpdateProperties](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databasecreateorupdateproperties) и задайте свойства новой базы данных. Затем вызовите метод CreateOrUpdate с группой ресурсов, именем сервера и именем новой базы данных.

    // Create a database: configure create or update parameters and properties explicitly
    DatabaseCreateOrUpdateParameters newPooledDatabaseParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentServer.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = 268435456000, // 250 GB,
            Collation = "SQL_Latin1_General_CP1_CI_AS"
        }
    };

    var poolDbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database2", newPooledDatabaseParameters);

Сведения о перемещении существующей базы данных в пул см. в разделе [Перемещение базы данных в пул эластичных БД](sql-database-elastic-pool-manage-csharp.md#Move-a-database-into-an-elastic-pool).

## Пример. Создание пула с помощью C&#x23

В этом примере создается группа ресурсов Azure, экземпляр Azure SQL Server и эластичный пул.
 

Для выполнения этого примера требуются дополнительные библиотеки. Чтобы установить их, в Visual Studio выполните следующие команды в [консоли диспетчера пакетов](http://docs.nuget.org/Consume/Package-Manager-Console) (**Средства** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**).

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.Resources –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre

Создайте консольное приложение и замените содержимое файла Program.cs приведенным ниже. Сведения о том, как получить идентификатор клиента и другие значения, см. в статье [Получение идентификатора клиента и ключа для доступа к базе данных SQL из кода](sql-database-client-id-keys.md). Используйте командлет [Get AzureRmSubscription](https://msdn.microsoft.com/library/mt619284.aspx), чтобы получить значение subscriptionId.

    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    namespace SqlDbElasticPoolsSample
    {
    class Program
    {
        // authentication variables
        static string subscriptionId = "<your Azure subscription>";
        static string clientId = "<your client id>";
        static string redirectUri = "<your redirect URI>";
        static string domainName = "<i.e. microsoft.onmicrosoft.com>";
        static AuthenticationResult token;

        // resource group variables
        static string datacenterLocation = "<location>";
        static string resourceGroupName = "<resource group name>";

        // server variables
        static string serverName = "<server name>";
        static string adminLogin = "<server admin>";
        static string adminPassword = "<server password (store it securely!)>";
        static string serverVersion = "12.0";

        // pool variables
        static string elasticPoolName = "<pool name>";
        static string poolEdition = "Standard";
        static int poolDtus = 400;
        static int databaseMinDtus = 0;
        static int databaseMaxDtus = 100;


        static void Main(string[] args)
        {
            // Sign in to Azure
            token = GetAccessToken();
            Console.WriteLine("Logged in as: " + token.UserInfo.DisplayableId);

            // Create a resource group
            ResourceGroup rg = CreateResourceGroup();
            Console.WriteLine(rg.Name + " created.");

            // Create a server
            Console.WriteLine("Creating server... ");
            ServerGetResponse srvr = CreateServer();
            Console.WriteLine("Creation of server " + srvr.Server.Name + ": " + srvr.StatusCode.ToString());

            // Create a pool
            Console.WriteLine("Creating elastic database pool... ");
            ElasticPoolCreateOrUpdateResponse epool = CreateElasticDatabasePool();
            Console.WriteLine("Creation of pool " + epool.ElasticPool.Name + ": " + epool.Status.ToString());


            // keep the console open until Enter is pressed!
            Console.WriteLine("Press Enter to exit.");
            Console.ReadLine();
        }


        static ElasticPoolCreateOrUpdateResponse CreateElasticDatabasePool()
        {
            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create elastic pool: configure create or update parameters and properties explicitly
            ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = datacenterLocation,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = poolEdition,
                    Dtu = poolDtus, 
                    DatabaseDtuMin = databaseMinDtus,
                    DatabaseDtuMax = databaseMaxDtus
                }
            };

            // Create the pool
            var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate(resourceGroupName, serverName, elasticPoolName, newPoolParameters);
            return newPoolResponse;
        }



        static ServerGetResponse CreateServer()
        {

            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = datacenterLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = adminLogin,
                    AdministratorLoginPassword = adminPassword,
                    Version = serverVersion
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;

        }


        static ResourceGroup CreateResourceGroup()
        {
           
            Microsoft.Rest.TokenCredentials creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);
            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = datacenterLocation,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }


        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    clientId,
            new Uri(redirectUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }
    }
    }

  

## Дальнейшие действия

- [Управление пулом.](sql-database-elastic-pool-manage-csharp.md)
- [Обзор заданий обработки эластичных баз данных](sql-database-elastic-jobs-overview.md). Эти задания упрощают выполнение сценариев T-SQL для любого количества эластичных баз данных в пуле.
- [Общие сведения о возможностях эластичных баз данных](sql-database-elastic-scale-introduction.md). Использование средств эластичных баз данных для масштабирования.

## Дополнительные ресурсы

- [База данных SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [API управления ресурсами](https://msdn.microsoft.com/library/azure/dn948464.aspx)

<!---HONumber=AcomDC_0601_2016-->