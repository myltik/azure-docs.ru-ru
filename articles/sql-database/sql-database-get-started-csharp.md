<properties 
   pageTitle="Создание базы данных SQL Azure c помощью C#" 
   description="В этой статье показано, как создавать базы данных SQL Azure с помощью C#, используя библиотеку баз данных SQL Azure для .NET." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="data-management" 
   ms.date="09/01/2015"
   ms.author="sstein"/>

# Создание базы данных SQL с помощью C&#x23;

**Отдельная база данных**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)



В этой статье представлены команды для создания базы данных SQL Azure с помощью C# и [библиотеки баз данных SQL Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).

В этой статье описывается создание одной базы данных; создание эластичных баз данных описывается в статье [Создание пула эластичных баз данных](sql-database-elastic-pool-portal.md).

Код разбит на отдельные фрагменты для ясности, и пример консольного приложения объединяет все команды в нижней части этой статьи.

Библиотека баз данных SQL Azure для .NET предоставляет API на основе [диспетчера ресурсов Azure](resource-group-overview.md), который создает оболочку для [API REST Базы данных SQL на основе диспетчера ресурсов](https://msdn.microsoft.com/library/azure/mt163571.aspx). Эта клиентская библиотека следует общему шаблону для клиентских библиотек на основе диспетчера ресурсов. Диспетчеру ресурсов необходимы группы ресурсов и проверка подлинности с помощью [Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx) (AAD).

<br>

> [AZURE.NOTE]Библиотека базы данных SQL для .NET предоставляется в виде предварительной версии.

<br>

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

- Подписка Azure. Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ**. Оформив подписку, вернитесь к этой статье.
- приведенному. Бесплатный экземпляр Visual Studio см. на странице [Загрузки Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).


## Установка необходимых библиотек

Получите необходимые библиотеки управления, установив следующие пакеты с помощью [консоли диспетчера пакетов](http://docs.nuget.org/Consume/Package-Manager-Console):

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre
    PM> Install-Package Microsoft.Azure.Management.Resources –Pre
    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


## Настройка проверки подлинности в Azure Active Directory

Сначала необходимо предоставить клиентскому приложению доступ к API REST, настроив необходимую проверку подлинности.

[API REST диспетчера ресурсов Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx) используют Azure Active Directory для проверки подлинности.

Для проверки подлинности клиентского приложения на основе текущего пользователя необходимо сначала зарегистрировать приложение в домене AAD, связанном с подпиской, в которой были созданы ресурсы Azure. Если подписка Azure была создана на базе учетной записи Microsoft, а не на базе рабочей или учебной учетной записи, у вас уже будет домен AAD по умолчанию. Зарегистрировать приложение можно на [портале Azure](https://manage.windowsazure.com/).

Для создания нового приложения и его регистрации в правильном Active Directory сделайте следующие.

1. Прокрутите меню с левой стороны, чтобы найти службу **Active Directory**, и откройте ее.

    ![AAD][1]

2. Выберите каталог для проверки подлинности приложения и щелкните его **имя**.

    ![Каталоги][4]

3. На странице каталога выберите **ПРИЛОЖЕНИЯ**.

    ![Приложения][5]

4. Нажмите кнопку **ДОБАВИТЬ**, чтобы добавить новое приложение.

    ![Добавление приложения][6]

5. Выберите **Добавить приложение, разрабатываемое моей организацией**.

5. Укажите **ИМЯ** приложения и выберите **СОБСТВЕННОЕ КЛИЕНТСКОЕ ПРИЛОЖЕНИЕ**.

    ![Добавление приложения][7]

6. Укажите **URI ПЕРЕНАПРАВЛЕНИЯ**. Он не обязательно должен быть фактической конечной точкой, а просто допустимым URI.

    ![Добавление приложения][8]

7. Завершите создание приложения, нажмите кнопку **НАСТРОИТЬ** и скопируйте **ИДЕНТИФИКАТОР КЛИЕНТА** (в коде потребуется идентификатор клиента).

    ![get client id][9]


1. В нижней части страницы нажмите **Добавить приложение**.
1. Выберите **Приложения Майкрософт**.
1. Выберите **API управления службами Azure**, а затем завершите работу мастера.
2. С помощью выбранного API теперь нужно предоставлять конкретные разрешения, необходимые для доступа к этому API, выбрав **Доступ к управлению службами Azure (предварительная версия)**.

    ![permissions][2]

2. Щелкните **СОХРАНИТЬ**.



### Идентификация имени домена

Имя домена требуется для кода. Простой способ идентифицировать правильное имя домена:

1. Войдите на [портал предварительной версии Azure](https://portal.azure.com).
2. Наведите указатель мыши на имя в правом верхнем углу и запомните домен, который отобразится во всплывающем окне.

    ![Идентификация имени домена][3]





**Дополнительные ресурсы AAD**

Дополнительные сведения об использовании Azure Active Directory для проверки подлинности можно найти в [этой полезной записи блога](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/).


### Получение маркера доступа для текущего пользователя 

Клиентское приложение должно получить маркер доступа приложения для текущего пользователя. Первый раз, когда код выполняется пользователем, ему будет предложено ввести свои учетные данные пользователя, и результирующий маркер будет кэширован локально. При последующих вызовах маркер будет извлечен из кэша, и пользователю будет предложено войти, только если истек срок действия маркера.

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
        new Uri(clientAppUri) /* redirect URI */,
        PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

        return token;
    }



> [AZURE.NOTE]Примеры в этой статье используют синхронную форму каждого запроса API и блокируют до завершения вызова REST на базовой службе. Доступны асинхронные методы.



## Создание группы ресурсов

При наличии диспетчера ресурсов все ресурсы необходимо создавать в группе ресурсов. Группа ресурсов представляет собой контейнер, содержащий связанные ресурсы для приложения. При наличии базы данных SQL Azure сервер базы данных сначала следует создать в существующей группе ресурсов, а затем создать на сервере базу данных. Прежде чем приложение сможет подключиться к серверу или базе данных, необходимо также создать правило брандмауэра на сервере, чтобы открыть доступ с IP-адреса клиента.


    // Create a resource management client 
    ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken ));
    
    // Resource group parameters
    ResourceGroup resourceGroupParameters = new ResourceGroup()
    {
        Location = "South Central US"
    };
    
    //Create a resource group
    var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("ResourceGroupName", resourceGroupParameters);



## Создание сервера 

Базы данных SQL содержатся на серверах. Это имя сервера должно быть глобально уникальным для серверов SQL Azure, и если оно уже занято, появится сообщение об ошибке. Кроме того, выполнение этой команды может занять несколько минут.


    // Create a SQL Database management client
    SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* Subscription id*/, token.AccessToken));

    // Create a server
    ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ServerCreateOrUpdateProperties()
        {
            AdministratorLogin = "ServerAdmin",
            AdministratorLoginPassword = securelyStoredPassword,
            Version = "12.0"
        }
    };

    var serverResult = sqlClient.Servers.CreateOrUpdate("ResourceGroupName", "ServerName", serverParameters);




## Создание правила серверного брандмауэра для доступа к серверу

По умолчанию к серверу нельзя подключиться из любого места. Чтобы подключиться к серверу или какой-либо базе данных на сервере, необходимо определить [правило брандмауэра](https://msdn.microsoft.com/library/azure/ee621782.aspx), которое разрешает доступ с IP-адреса клиента.

В следующем примере создается правило, которое открывает доступ к серверу с любого IP-адреса. Рекомендуется создать соответствующие имя и пароль для входа в SQL для защиты базы данных и не полагаться на правила брандмауэра как на основное средство защиты от вторжения.


    // Create a firewall rule on the server to allow TDS connection 
    FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
    {
        Properties = new FirewallRuleCreateOrUpdateProperties()
        {
            StartIpAddress = "0.0.0.0",
            EndIpAddress = "255.255.255.255"
        }
    };

    var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("ResourceGroupName", "ServerName", "FirewallRuleName", firewallParameters);




Чтобы разрешить другим службам Azure доступ к серверу, добавьте правило брандмауэра и задайте для параметров StartIpAddress и EndIpAddress значение 0.0.0.0. Обратите внимание, что это разрешает доступ к серверу трафику Azure от *любой* подписки Azure.


## Создание базы данных

Следующая команда создает новую базу данных уровня Basic, если база данных с таким именем не существует на сервере. Если база данных с тем же именем существует, она будет обновлена.

        // Create a database

        // Retrieve the server on which the database will be created
        Server currentServer = sqlClient.Servers.Get("ResourceGroupName", "ServerName").Server;
 
        // Create a database: configure create or update parameters and properties explicitly
        DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
        {
            Location = currentServer.Location,
            Properties = new DatabaseCreateOrUpdateProperties()
            {
                Edition = "Standard",
                RequestedServiceObjectiveName = "S0",
            }
        };

        var dbResponse = sqlClient.Databases.CreateOrUpdate("ResourceGroupNname", "ServerName", "Database1", newDatabaseParameters);






## Образец консольного приложения


    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    
    namespace CreateSqlDatabase
    {
     class Program
     {

        #region user variables
        
        // Azure subscription id
        private static string subscriptionId = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";
     
        
        // application client ID from Azure Active Directory (AAD)
        private static string clientAppUri = "http://app1";
        private static string clientId = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";

        private static string resourcegroupName = "rg1";

        private static string dataCenterLocation = "Japan West";

        private static string databaseName = "newDatabaseName";
        private static string databaseEdition = "Standard";
        private static string databasePerformanceLevel = "S0";

        private static string serverName = "serverName";
        private static string serverAdmin = "admin";
        private static string serverAdminPassword = securelyStoredPassword;
        private static string serverVersion = "12.0";

        private static string firewallRuleName = "rule1";
        private static string firewallRuleStartIp = "0.0.0.0";
        private static string firewallRuleEndIp = "255.255.255.255";



        private static string domainName = "microsoft.onmicrosoft.com";

        private static string serverLocation = "Japan West";

        #endregion


        static void Main(string[] args)
        {
            var token = GetAccessToken();

            // Who am I?
            Console.WriteLine("Identity: {0} {1}", token.UserInfo.GivenName, token.UserInfo.FamilyName);
            Console.WriteLine("Token expires on {0}", token.ExpiresOn);
            Console.WriteLine("");

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = dataCenterLocation
            };

            //Create a resource group
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourcegroupName, resourceGroupParameters);

            Console.WriteLine("Resource group {0} create or update completed with status code {1} ", resourceGroupResult.ResourceGroup.Name, resourceGroupResult.StatusCode);

            //create a SQL Database management client
            TokenCloudCredentials tokenCredentials = new TokenCloudCredentials(subscriptionId, token.AccessToken);

            SqlManagementClient sqlClient = new SqlManagementClient(tokenCredentials);

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = serverVersion
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate(resourcegroupName, serverName, serverParameters);

            var serverGetResult = sqlClient.Servers.Get(resourcegroupName, serverName);


            Console.WriteLine("Server {0} create or update completed with status code {1}", serverResult.Server.Name, serverResult.StatusCode);

            // Create a firewall rule on the server to allow TDS connection 

            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = firewallRuleStartIp,
                    EndIpAddress = firewallRuleEndIp
                }
            };

            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourcegroupName, serverName, firewallRuleName, firewallParameters);

            Console.WriteLine("Firewall rule {0} create or update completed with status code: {1}", firewallResult.FirewallRule.Name, firewallResult.StatusCode);

            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourcegroupName, serverName).Server;

            // Create a database
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerformanceLevel,
                }
            };

            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourcegroupName, serverName, databaseName, newDatabaseParameters);

            Console.WriteLine("Database {0} created with status code: {1}. Service Objective: {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);
           
            Console.WriteLine(System.Environment.NewLine + "Press any key to exit.");
            Console.ReadKey();
        }

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
            new Uri(clientAppUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }
      }
    }


## Дальнейшие действия

- [Подключение к базе данных SQL и выполнение запросов с помощью языка C#](sql-database-connect-query.md)
- [Соединение с SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)

## Дополнительные ресурсы

- [База данных SQL](https://azure.microsoft.com/documentation/services/sql-database/)





<!--Image references-->
[1]: ./media/sql-database-get-started-csharp/aad.png
[2]: ./media/sql-database-get-started-csharp/permissions.png
[3]: ./media/sql-database-get-started-csharp/getdomain.png
[4]: ./media/sql-database-get-started-csharp/aad2.png
[5]: ./media/sql-database-get-started-csharp/aad-applications.png
[6]: ./media/sql-database-get-started-csharp/add.png
[7]: ./media/sql-database-get-started-csharp/add-application.png
[8]: ./media/sql-database-get-started-csharp/add-application2.png
[9]: ./media/sql-database-get-started-csharp/clientid.png

<!---HONumber=Oct15_HO3-->