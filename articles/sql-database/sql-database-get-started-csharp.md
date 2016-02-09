<properties 
	pageTitle="Руководство по базам данных SQL: создание базы данных SQL с помощью C# | Microsoft Azure" 
	description="Используйте базы данных SQL для разработки приложений на основе SQL и C#. Создайте базу данных Azure SQL с помощью C# и библиотеки баз данных SQL для .NET." 
	keywords="учебник sql, sql и c#"   
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="data-management" 
   ms.date="01/22/2016"
   ms.author="sstein"/>

# Руководство по базам данных SQL: создание базы данных SQL с помощью C# и библиотеки базы данных SQL для .NET 

**Отдельная база данных**

> [AZURE.SELECTOR]
- [Azure portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)



Узнайте, как использовать команды C# для создания базы данных SQL Azure с помощью [библиотеки баз данных SQL Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).

Вы познакомитесь с базами данных SQL на примере создания отдельной базы данных с помощью SQL и C#. Создание пула эластичных баз данных описано в статье [Создание пула эластичных баз данных](sql-database-elastic-pool-portal.md).

Код разбит на отдельные фрагменты для ясности, и пример консольного приложения объединяет все команды в нижней части этой статьи.

Библиотека базы данных SQL Azure для .NET предоставляет API на основе [диспетчера ресурсов Azure](resource-group-overview.md), который создает оболочку для [API REST базы данных SQL на основе диспетчера ресурсов](https://msdn.microsoft.com/library/azure/mt163571.aspx). Эта клиентская библиотека следует общему шаблону для клиентских библиотек на основе диспетчера ресурсов. Диспетчеру ресурсов необходимы группы ресурсов и проверка подлинности с помощью [Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx) (AAD).

<br>

> [AZURE.NOTE] Библиотека базы данных SQL для .NET предоставляется в виде предварительной версии.

<br>

Чтобы выполнить действия, описанные в этой статье, необходимо следующее:

- Подписка Azure. Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ**. Оформив подписку, вернитесь к этой статье.
- приведенному. Бесплатный экземпляр Visual Studio можно получить на странице [Загрузки Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).


## Установка необходимых библиотек

Чтобы создать базу данных SQL с помощью C#, установите с помощью [консоли диспетчера пакетов](http://docs.nuget.org/Consume/Package-Manager-Console) следующие пакеты, которые содержат нужные библиотеки управления. Для этого в Visual Studio последовательно выберите элементы **Средства** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.Resources –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre


## Настройка проверки подлинности в Azure Active Directory

Сначала необходимо предоставить клиентскому приложению доступ к службе баз данных SQL, настроив необходимую проверку подлинности.

Для проверки подлинности клиентского приложения на основе текущего пользователя необходимо сначала зарегистрировать приложение в домене AAD, связанном с подпиской, в которой были созданы ресурсы Azure. Если подписка Azure была создана на базе учетной записи Microsoft, а не на базе рабочей или учебной учетной записи, у вас уже будет домен AAD по умолчанию.

Для создания нового приложения и его регистрации в правильном Active Directory сделайте следующие.

1. Войдите на [классический портал Azure](https://manage.windowsazure.com/).
1. В левой части выберите службу **Active Directory**, укажите каталог для проверки подлинности приложения и щелкните его **имя**.

    ![Руководство по базам данных SQL: настройка Azure Active Directory (AAD).][1]

2. На странице каталога выберите **ПРИЛОЖЕНИЯ**.

    ![Страница каталога с элементом приложения.][5]

4. Нажмите кнопку **ДОБАВИТЬ**, чтобы добавить новое приложение.

5. Выберите **Добавить приложение, разрабатываемое моей организацией**.

5. Укажите **ИМЯ** приложения и выберите **СОБСТВЕННОЕ КЛИЕНТСКОЕ ПРИЛОЖЕНИЕ**.

    ![Ввод сведений о приложении SQL C#.][7]

6. Укажите **URI ПЕРЕНАПРАВЛЕНИЯ**. Он не обязательно должен быть фактической конечной точкой, а просто допустимым URI.

    ![Ввод URL-адреса перенаправления для приложения SQL C#.][8]

7. Чтобы завершить создание приложения, нажмите кнопку **Настроить** и скопируйте **идентификатор клиента** (позднее его нужно будет указать в коде).

    ![Ввод идентификатора клиента для приложения SQL C#.][9]


1. В нижней части страницы нажмите **Добавить приложение**.
1. Выберите **Приложения Майкрософт**.
1. Выберите **API управления службами Azure**, а затем завершите работу мастера.
2. С помощью выбранного API теперь нужно предоставлять конкретные разрешения, необходимые для доступа к этому API, выбрав **Доступ к управлению службами Azure (предварительная версия)**.

    ![Установка разрешений.][2]

2. Щелкните **СОХРАНИТЬ**.



### Идентификация имени домена

Имя домена требуется для кода. Простой способ идентифицировать правильное имя домена:

1. Перейдите на [портал Azure](http://portal.azure.com).
2. Наведите указатель мыши на имя в правом верхнем углу и запомните домен, который отобразится во всплывающем окне.

    ![Определение имени домена.][3]





**Дополнительные ресурсы AAD**

Подробнее об использовании Azure Active Directory для аутентификации см. в [этой записи блога](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/).


### Получение маркера доступа для текущего пользователя 

Клиентское приложение должно получить маркер доступа приложения для текущего пользователя. Первый раз, когда код выполняется пользователем, ему будет предложено ввести свои учетные данные пользователя, и результирующий маркер будет кэширован локально. При последующих вызовах маркер будет извлечен из кэша, и пользователю будет предложено войти, только если истек срок действия маркера.

Этот код возвращает элемент Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationResult, который понадобится в других фрагментах кода ниже.

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

При наличии диспетчера ресурсов все ресурсы необходимо создавать в группе ресурсов. Группа ресурсов представляет собой контейнер, содержащий связанные ресурсы для приложения. При наличии базы данных SQL Azure сервер базы данных нужно создавать в существующей группе ресурсов.

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }


## Создание сервера 

Базы данных SQL содержатся на серверах. Это имя сервера должно быть глобально уникальным для серверов SQL Azure, и если оно уже занято, появится сообщение об ошибке. Кроме того, выполнение этой команды может занять несколько минут.

    static void CreateServer()
    {
        // Create a SQL Database management client
        SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

        // Create a server
        ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
        {
            Location = location,
            Properties = new ServerCreateOrUpdateProperties()
            {
                AdministratorLogin = administratorLogin,
                AdministratorLoginPassword = administratorPassword,
                Version = serverVersion
            }
        };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
    }


### Создание администратора внешнего сервера


    // Create a server external admin
    ServerAdministratorCreateOrUpdateParameters aadAdminParameters = new ServerAdministratorCreateOrUpdateParameters()
    {
        Properties = new ServerAdministratorCreateOrUpdateProperties()
        {
            AdministratorType = "ActiveDirectory",
            Login = "<login>",
            Sid = new Guid("<Azure AD admin sid>"),
            TenantId = new Guid("<Azure AD tenant id>")
        }
    };

    var adminResult = sqlClient.ServerAdministrators.CreateOrUpdate(resourceGroupName, serverName, "ActiveDirectory", aadAdminParameters);
    var adminGetResult = sqlClient.ServerAdministrators.Get(resourceGroupName, serverName, "ActiveDirectory");




## Создание правила серверного брандмауэра для доступа к серверу

По умолчанию к серверу нельзя подключиться из любого места. Чтобы разрешить подключение к серверу или базам данных на сервере, определите [правило брандмауэра](https://msdn.microsoft.com/library/azure/ee621782.aspx), которое разрешает доступ с IP-адреса клиента.

В следующем примере создается правило, которое открывает доступ к серверу с любого IP-адреса. Рекомендуется создать соответствующие имя и пароль для входа в SQL для защиты базы данных и не полагаться на правила брандмауэра как на основное средство защиты от вторжения.


        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server 
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }




Чтобы разрешить другим службам Azure доступ к серверу, добавьте правило брандмауэра и задайте для параметров StartIpAddress и EndIpAddress значение 0.0.0.0. Обратите внимание, что это правило разрешает доступ к серверу из *любой* подписки Azure.


## Создание базы данных SQL с помощью C#

Следующая команда C# создает новую базу данных SQL, если базы данных с таким именем на сервере нет. В противном случае такая база данных будет обновлена.


        static void CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
        }



## Пример консольного приложения C#

В следующем примере создается группа ресурсов, сервер, правило брандмауэра и база данных SQL. В разделе *Настройка проверки подлинности в Azure Active Directory* в начале этой статьи описано, где можно получить значения для переменных clientId, redirectUri и domainName.


    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    
    namespace SqlDbConsoleApp
    {
    class Program
    {
        // Get these values from the Azure portal
        static string subscriptionId = "<Azure subscription id>";
        static string clientId = "<Azure App clientId>";
        static string redirectUri = "<Azure App redirectURI>";
        static string domainName = "<domain>";

        // You create these values 
        static string resourceGroupName = "<your resource group name>";
        static string location = "<Azure data center location>";

        static string serverName = "<your server name>";
        static string administratorLogin = "<your server admin>";
        
        // store your password securely!
        static string administratorPassword = "<your server admin password>";
        static string serverVersion = "12.0";

        static string firewallRuleName = "<your firewall rule name>";

        static string databaseName = "dbfromcsharparticle";
        static string databaseEdition = "Basic"; // Basic, Standard, or Premium
        static string databasePerfLevel = "";

        static AuthenticationResult token;
        static Microsoft.Rest.TokenCredentials creds;

        static SqlManagementClient sqlClient;


        static void Main(string[] args)
        {
            Console.WriteLine("Logging in...");

            token = GetAccessToken();

            Console.WriteLine("Logged in as: " + token.UserInfo.DisplayableId);

            Console.WriteLine("Creating resource group...");
            CreateResourceGroup();

            sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            Console.WriteLine("Creating server...");
            CreateServer();

            Console.WriteLine("Creating firewall rule...");
            CreateFirewallRule();

            Console.WriteLine("Creating database...");

            DatabaseCreateOrUpdateResponse dbResponse = CreateDatabase();
            Console.WriteLine("Status: " + dbResponse.Status.ToString() 
                + " Code: " + dbResponse.StatusCode.ToString());

            Console.WriteLine("Press enter to exit...");
            Console.ReadLine();
        }

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }

        static void CreateServer()
        {
            // Create a SQL Database management client
            //SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = location,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = administratorLogin,
                    AdministratorLoginPassword = administratorPassword,
                    Version = serverVersion
                }
            };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
        }

        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server 
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    // replace with your client IP address
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }

        static DatabaseCreateOrUpdateResponse CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
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
Вы уже познакомились с базами данных SQL и настроили базу данных с помощью C#. Теперь можно перейти к изучению следующих статей.

- [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md)

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

<!---HONumber=AcomDC_0204_2016-->