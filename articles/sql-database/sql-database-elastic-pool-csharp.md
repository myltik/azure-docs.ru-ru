.<properties
    pageTitle="Разработка базы данных на C#: пулы эластичных баз данных | Microsoft Azure"
    description="Используйте методы разработки баз данных на C# для создания пула эластичных баз данных SQL Azure, чтобы совместно использовать ресурсы нескольких баз данных."
    services="sql-database"
    keywords="база данных на C#, разработка для SQL"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="08/18/2016"
    ms.author="sstein"/>

# Разработка базы данных на C&#x23;: создание и настройка пула эластичных баз данных для базы данных SQL

> [AZURE.SELECTOR]
- [Портал Azure](sql-database-elastic-pool-create-portal.md)
- [C#](sql-database-elastic-pool-csharp.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


В этой статье описано, как создать [пул эластичных баз данных](sql-database-elastic-pool.md) для баз данных SQL из приложения, используя методы разработки баз данных на C#.

> [AZURE.NOTE] Сейчас пулы эластичных баз данных предоставляются в виде предварительной версии, которая доступна только с серверами Базы данных SQL версии 12. Если у вас есть сервер базы данных SQL версии 11, с помощью PowerShell вы можете в одно действие [обновить его до версии 12 и создать пул](sql-database-upgrade-server-powershell.md).

В примерах используется [библиотека базы данных SQL Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). Код разбит на отдельные фрагменты для ясности, и пример консольного приложения объединяет все команды в нижней части этой статьи.


> [AZURE.NOTE] Библиотека базы данных SQL для .NET предоставляется в виде предварительной версии.



Если у вас еще нет подписки Azure, щелкните **БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ** в верхней части этой страницы. После оформления подписки вернитесь к этой статье. Бесплатный экземпляр Visual Studio см. на странице [Загрузки Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).

## Установка необходимых библиотек

Получите необходимые библиотеки управления, установив следующие пакеты с помощью [консоли диспетчера пакетов](http://docs.nuget.org/Consume/Package-Manager-Console) для разработки для SQL:

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.Resources –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre


## Настройка проверки подлинности в Azure Active Directory

Перед началом разработки для SQL на C# необходимо выполнить некоторые задачи на портале Azure. Сначала предоставьте приложению доступ к REST API, настроив необходимую аутентификацию.

[Интерфейсы REST API Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn948464.aspx) используют для аутентификации Azure Active Directory, а не сертификаты, используемые в более ранней классической модели развертывания.

Для аутентификации клиентского приложения необходимо сначала зарегистрировать приложение в домене AAD, связанном с подпиской, в которой были созданы ресурсы Azure. Если подписка Azure была создана на базе учетной записи Майкрософт, а не на базе рабочей или учебной учетной записи, то у вас уже есть домен AAD по умолчанию. Зарегистрируйте приложение на [классическом портале](https://manage.windowsazure.com/).

Для создания приложения и его регистрации в соответствующем каталоге Active Directory выполните следующее.

1. Найдите службу **Active Directory** и откройте ее.

    ![Разработка баз данных SQL на C#: настройка Active Directory][1]

2. Выберите каталог для аутентификации приложения и щелкните его **имя**.

    ![Выберите каталог.][4]

3. На странице каталога выберите **ПРИЛОЖЕНИЯ**.

    ![Щелкните «Приложения».][5]

4. Нажмите кнопку **ДОБАВИТЬ**, чтобы добавить приложение.

    ![Нажмите кнопку «Добавить», чтобы создать приложение C#.][6]

5. Выберите **Добавить приложение, разрабатываемое моей организацией**.

5. Укажите **ИМЯ** приложения и выберите **СОБСТВЕННОЕ КЛИЕНТСКОЕ ПРИЛОЖЕНИЕ**.

    ![Добавление приложения][7]

6. Укажите **URI ПЕРЕНАПРАВЛЕНИЯ**. Он не обязательно должен быть фактической конечной точкой, а просто допустимым URI.

    ![Добавление приложения][8]

7. Завершите создание приложения, щелкните **НАСТРОИТЬ** и скопируйте **ИДЕНТИФИКАТОР КЛИЕНТА** (его нужно указать в коде).

    ![Получение идентификатора клиента][9]


1. В нижней части страницы щелкните **Добавить приложение**.
1. Выберите **Приложения Майкрософт**.
1. Выберите **API управления службами Azure**, а затем завершите работу мастера.
2. С помощью выбранного API теперь нужно предоставлять конкретные разрешения, необходимые для доступа к этому API, выбрав **Доступ к управлению службами Azure (предварительная версия)**.

    ![Установка разрешений][2]

2. Щелкните **СОХРАНИТЬ**.



### Идентификация имени домена

Имя домена требуется для кода. Простой способ идентифицировать правильное имя домена:

1. Перейдите на [портал Azure](https://portal.azure.com).
2. Наведите указатель мыши на имя в правом верхнем углу и запомните домен, который отобразится во всплывающем окне. Замените **domain.onmicrosoft.com** в приведенном ниже фрагменте кода значением, соответствующим вашей учетной записи.

    ![Идентификация имени домена][3]



**Дополнительные ресурсы AAD**

Подробнее об использовании Azure Active Directory для аутентификации см. в [этой записи блога](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/).


### Получение маркера доступа для текущего пользователя

Клиентское приложение должно получить маркер доступа приложения для текущего пользователя. При первом выполнении кода вам будет предложено ввести свои учетные данные пользователя, и результирующий маркер будет помещен в локальный кэш. При последующих вызовах маркер будет извлечен из кэша, и запрос на вход отобразится, только если истек срок действия маркера.


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



> [AZURE.NOTE] Примеры в этой статье используют синхронную форму каждого запроса API и блокируют до завершения вызова REST на базовой службе. Доступны асинхронные методы.



## Создание группы ресурсов

При наличии диспетчера ресурсов все ресурсы необходимо создавать в группе ресурсов. Группа ресурсов представляет собой контейнер, содержащий связанные ресурсы для приложения. Для создания пула эластичных баз данных в существующую группу ресурсов необходимо добавить сервер базы данных SQL Azure. Выполните следующий код C#, создающий группу ресурсов:


    // Create a resource management client
    ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken ));

    // Resource group parameters
    ResourceGroup resourceGroupParameters = new ResourceGroup()
    {
        Location = "South Central US"
    };

    //Create a resource group
    var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("resourcegroup-name", resourceGroupParameters);



## Создание сервера

Пулы эластичных баз данных находятся на серверах баз данных SQL Azure, поэтому следующим шагом является создание сервера. Это имя сервера должно быть глобально уникальным для серверов SQL Azure, и если оно уже используется, то отобразится сообщение об ошибке. Кроме того, выполнение этой команды может занять несколько минут. Чтобы приложение могло подключиться к серверу, необходимо создать на сервере правило брандмауэра, открывающее доступ с IP-адреса клиента.


    // Create a SQL Database management client
    SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* Subscription id*/, token.AccessToken));

    // Create a server
    ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ServerCreateOrUpdateProperties()
        {
            AdministratorLogin = "ServerAdmin",
            AdministratorLoginPassword = "P@ssword1",
            Version = "12.0"
        }
    };

    var serverResult = sqlClient.Servers.CreateOrUpdate("resourcegroup-name", "server-name", serverParameters);




## Создание правила серверного брандмауэра для доступа к серверу

По умолчанию правил брандмауэра, открывающих доступ с любого адреса, на сервере нет. Чтобы подключиться к серверу или какой-либо базе данных на сервере, необходимо определить [правило брандмауэра](sql-database-firewall-configure.md), которое разрешает доступ с IP-адреса клиента.

В следующем примере создается правило брандмауэра для сервера, которое открывает доступ к серверу с любого IP-адреса. Рекомендуется создать соответствующие имя и пароль для входа в SQL для защиты базы данных и не полагаться на правила брандмауэра как на основное средство защиты от вторжения. Подробнее об этом см. в статье [Управление базами данных и учетными записями в базе данных SQL Azure](sql-database-manage-logins.md).


    // Create a firewall rule on the server to allow TDS connection
    FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
    {
        Properties = new FirewallRuleCreateOrUpdateProperties()
        {
            StartIpAddress = "0.0.0.0",
            EndIpAddress = "255.255.255.255"
        }
    };

    var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("resourcegroup-name", "server-name", "FirewallRule1", firewallParameters);




Чтобы разрешить другим службам Azure доступ к серверу, добавьте правило брандмауэра и задайте для параметров StartIpAddress и EndIpAddress значение 0.0.0.0. Это разрешит доступ к серверу трафику Azure из *любой* подписки Azure.


## Создание базы данных

В следующем примере создается новая основная база данных, причем если на сервере уже существует база данных с таким же именем, существующая база данных будет обновлена.

        // Create a database

        // Retrieve the server on which the database will be created
        Server currentServer = sqlClient.Servers.Get("resourcegroup-name", "server-name").Server;

        // Create a database: configure create or update parameters and properties explicitly
        DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
        {
            Location = currentServer.Location,
            Properties = new DatabaseCreateOrUpdateProperties()
            {
                Edition = "Basic",
                RequestedServiceObjectiveName = "Basic",
                MaxSizeBytes = 2147483648,
                Collation = "SQL_Latin1_General_CP1_CI_AS"
            }
        };

        var dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", newDatabaseParameters);



## Создание пула эластичных баз данных

В следующем примере создается новый пул эластичных баз данных.



    // Create elastic pool: configure create or update parameters and properties explicitly
    ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = "Standard",
            Dtu = 100,  // alternatively set StorageMB, if both are specified they must agree based on the eDTU:storage ratio of the edition
            DatabaseDtuMin = 0,
            DatabaseDtuMax = 100
         }
    };

    // Create the pool
    var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## Обновление пула эластичных баз данных

В следующем примере обновляются характеристики производительности существующего пула эластичных баз данных:

    // Retrieve existing pool properties
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



## Перемещение существующей базы данных в пул эластичных баз данных

Кроме того, после создания пула можно использовать Transact-SQL для перемещения существующих баз данных в пул и из него. Дополнительные сведения см. в разделе [Мониторинг пула эластичных баз данных и управление им с помощью Transact-SQL](sql-database-elastic-pool-manage-tsql.md). *

В следующем примере существующая база данных Azure переносится в пул:


    // Update database service objective to add the database to a pool

    // Retrieve current database properties
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

    // Update the database
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);




## Создание новой базы данных внутри пула эластичных баз данных

*Кроме того, после создания пула можно использовать Transact-SQL для создания эластичных баз данных в пуле. Дополнительные сведения см. в статье [Мониторинг пула эластичных баз данных и управление им с помощью Transact-SQL](sql-database-elastic-pool-manage-tsql.md).*

В следующем примере новая база данных создается непосредственно в пуле.


    // Create a new database in the pool

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



## Список всех баз данных в пуле эластичных баз данных

В следующем примере создается список всех существующих в пуле баз данных.

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }




## Образец консольного приложения


    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    using System.Security;

    namespace AzureSqlDatabaseRestApiExamples
    {
    class Program
    {
        /// <summary>
        /// Prompts for user credentials when first run or if the cached credentials have expired.
        /// </summary>
        /// <returns>The access token from AAD.</returns>
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

        private static AuthenticationResult GetAccessTokenUsingUserCredentials(UserCredential userCredential)
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + "YOU.onmicrosoft.com" /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* application client ID from AAD*/,
                userCredential);

            return token;
        }
        private static SecureString convertToSecureString(string secret)
        {
            var secureStr = new SecureString();
            if (secret.Length > 0)
            {
                foreach (var c in secret.ToCharArray()) secureStr.AppendChar(c);
            }
            return secureStr;
        }

        static void Main(string[] args)
        {
            var token = GetAccessToken();

            // Who am I?
            Console.WriteLine("Identity is {0} {1}", token.UserInfo.GivenName, token.UserInfo.FamilyName);
            Console.WriteLine("Token expires on {0}", token.ExpiresOn);
            Console.WriteLine("");

            // Create a resource management client
            ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken));

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = "South Central US"
            };

            //Create a resource group
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("resourcegroup-name", resourceGroupParameters);

            Console.WriteLine("Resource group {0} create or update completed with status code {1} ", resourceGroupResult.ResourceGroup.Name, resourceGroupResult.StatusCode);

            //create a SQL Database management client
            TokenCloudCredentials tokenCredentials = new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* Subscription id*/, token.AccessToken);

            SqlManagementClient sqlClient = new SqlManagementClient(tokenCredentials);

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = "South Central US",
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = "ServerAdmin",
                    AdministratorLoginPassword = "P@ssword1",
                    Version = "12.0"
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate("resourcegroup-name", "server-name", serverParameters);

            var serverGetResult = sqlClient.Servers.Get("resourcegroup-name", "server-name");


            Console.WriteLine("Server {0} create or update completed with status code {1}", serverResult.Server.Name, serverResult.StatusCode);

            // Create a firewall rule on the server to allow TDS connection

            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };

            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("resourcegroup-name", "server-name", "FirewallRule1", firewallParameters);

            Console.WriteLine("Firewall rule {0} create or update completed with status code {1}", firewallResult.FirewallRule.Name, firewallResult.StatusCode);


            var dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", newDatabaseParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);


           // Create an elastic database pool
            var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

            Console.WriteLine("Elastic pool {0} create or update completed with status code {1}.", newPoolResponse.ElasticPool.Name, newPoolResponse.StatusCode);

            // Update pool: retrieve existing pool properties
            var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

            // Update pool: configure create or update parameters with existing property values, override those to be changed.
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

            Console.WriteLine("Elastic pool {0} create or update completed with status code {1}.", newPoolResponse.ElasticPool.Name, newPoolResponse.StatusCode);

            // Update a databases service objective to add the database to a pool

            // Update database: retrieve current database properties
            currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

            // Update database: configure create or update parameters with existing property values, override those to be changed.
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


            // Create a new database in the pool

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

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective: {2}({3}) ", poolDbResponse.Database.Name, poolDbResponse.StatusCode, poolDbResponse.Database.Properties.ServiceObjective, poolDbResponse.Database.Properties.ElasticPoolName);
      }
    }







## дополнительные ресурсы.


[База данных SQL](https://azure.microsoft.com/documentation/services/sql-database/)

[API управления ресурсами](https://msdn.microsoft.com/library/azure/dn948464.aspx)

<!--Image references-->
[1]: ./media/sql-database-elastic-pool-csharp/aad.png
[2]: ./media/sql-database-elastic-pool-csharp/permissions.png
[3]: ./media/sql-database-elastic-pool-csharp/getdomain.png
[4]: ./media/sql-database-elastic-pool-csharp/aad2.png
[5]: ./media/sql-database-elastic-pool-csharp/aad-applications.png
[6]: ./media/sql-database-elastic-pool-csharp/add.png
[7]: ./media/sql-database-elastic-pool-csharp/add-application.png
[8]: ./media/sql-database-elastic-pool-csharp/add-application2.png
[9]: ./media/sql-database-elastic-pool-csharp/clientid.png

<!---HONumber=AcomDC_0824_2016-->