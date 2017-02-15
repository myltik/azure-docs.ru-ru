---
title: "Управление Azure Data Lake Analytics с помощью пакета SDK Azure для .NET | Документация Майкрософт"
description: "Узнайте, как управлять заданиями аналитики озера данных, источниками данных и пользователями. "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 811d172d-9873-4ce9-a6d5-c1a26b374c79
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/23/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 63ce27657ecf1c89f5d3dfc163ee655bc15ce0b7


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Управление Azure Data Lake Analytics с помощью пакета SDK Azure для .NET
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Узнайте, как управлять учетными записями, источниками данных, пользователями и заданиями Azure Data Lake Analytics с помощью пакета SDK Azure для .NET. Для просмотра статей, посвященных управлению с помощью других инструментов, щелкните селектор вкладок выше.

**Предварительные требования**

Перед началом работы с этим учебником необходимо иметь следующее:

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).

<!-- ################################ -->
<!-- ################################ -->


## <a name="connect-to-azure-data-lake-analytics"></a>Подключение к Azure Data Lake Analytics
Необходимо установить следующие пакеты Nuget:

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Common 
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre


В следующем примере кода показано, как подключиться к Azure и вывести список существующих учетных записей Data Lake Analytics в подписке Azure.

    using System;
    using System.Collections.Generic;
    using System.Threading;

    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;

    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Analytics;
    using Microsoft.Azure.Management.DataLake.Analytics.Models;

    namespace ConsoleAcplication1
    {
        class Program
        {

            private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
            private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
            private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

            private static DataLakeAnalyticsAccountManagementClient _adlaClient;

            private static void Main(string[] args)
            {

                var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

                _adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
                _adlaClient.SubscriptionId = SUBSCRIPTIONID;

                var adlaAccounts = ListADLAAccounts();

                Console.WriteLine("You have %i Data Lake Analytics account(s).", adlaAccounts.Count);
                for (int i = 0; i < adlaAccounts.Count; i ++)
                {
                    Console.WriteLine(adlaAccounts[i].Name);
                }

                System.Console.WriteLine("Press ENTER to continue");
                System.Console.ReadLine();
            }

            public static ServiceClientCredentials AuthenticateAzure(
            string domainName,
            string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }

            public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
            {
                var response = _adlaClient.Account.List();
                var accounts = new List<DataLakeAnalyticsAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlaClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                return accounts;
            }
        }
    }


## <a name="manage-accounts"></a>Управление учетными записями
Перед выполнением любого задания аналитики озера данных необходимо иметь учетную запись аналитики озера данных. В отличие от Azure HDInsight учетная запись аналитики не оплачивается, если ни одно задание не выполняется.  Вы платите только за время, когда выполняется задание.  Дополнительные сведения см. в разделе [Обзор аналитики озера данных Azure](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Создание учетных записей
Для выполнения следующего примера необходима группа управления ресурсами Azure и учетная запись Data Lake Store.

Ниже приведен код, создающий группу ресурсов.

    public static async Task<ResourceGroup> CreateResourceGroupAsync(
        ServiceClientCredentials credential,
        string groupName,
        string subscriptionId,
        string location)
    {

        Console.WriteLine("Creating the resource group...");
        var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
        var resourceGroup = new ResourceGroup { Location = location };
        return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
    }

Ниже приведен код, создающий учетную запись Data Lake Store.

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

Ниже приведен код, создающий учетную запись Data Lake Analytics.

    var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
    var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
    var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: location);
    var adlaAccount = _adlaClient.Account.Create(resourceGroupName, adlaAccountName, adlaParameters);

### <a name="list-accounts"></a>Список учетных записей
Ознакомьтесь с разделом [Подключение к Azure Data Lake Analytics](#connect_to_azure_data_lake_analytics).

### <a name="find-an-account"></a>Поиск учетной записи
После получения объекта со списком учетных записей Data Lake Analytics вы можете найти учетную запись следующим образом.

    Predicate<DataLakeAnalyticsAccount> accountFinder = (DataLakeAnalyticsAccount a) => { return a.Name == adlaAccountName; };
    var myAdlaAccount = adlaAccounts.Find(accountFinder);

### <a name="delete-data-lake-analytics-accounts"></a>Удаление учетных записей аналитики озера данных
Ниже приведен фрагмент кода, удаляющий учетную запись Data Lake Analytics.

    _adlaClient.Account.Delete(resourceGroupName, adlaAccountName);

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Управление источниками данных учетной записи
Аналитика озера данных в настоящее время поддерживает следующие источники данных:

* [Хранилище озера данных Azure](../data-lake-store/data-lake-store-overview.md)
* [Хранилище Azure](../storage/storage-introduction.md)

При создании учетной записи аналитики необходимо указать учетную запись хранения озера данных Azure в качестве учетной записи хранения по умолчанию. Учетная запись хранения озера данных по умолчанию используется для хранения метаданных задания и журналов аудита задания. После создания учетной записи аналитики можно добавить дополнительные учетные записи хранения озера данных и учетные записи хранения Azure. 

### <a name="find-the-default-data-lake-store-account"></a>Поиск учетной записи хранения озера данных по умолчанию
Поиск учетной записи Data Lake Analytics описан в разделе "Поиск учетной записи" этой статьи. Найдя нужную учетную запись, используйте следующую команду.

    string adlaDefaultDataLakeStoreAccountName = myAccount.Properties.DefaultDataLakeStoreAccount;


## <a name="use-azure-resource-manager-groups"></a>Использование групп диспетчера ресурсов Azure
Обычно приложения состоят из множества компонентов, например веб-приложения, базы данных, сервера базы данных, хранилища и служб сторонних поставщиков. Azure Resource Manager позволяет работать с ресурсами в приложении в виде группы, которая называется группой ресурсов Azure. Вы можете развертывать, обновлять, отслеживать или удалять все ресурсы для приложения в рамках одной скоординированной операции. Вы можете уточнить счета для своей организации, просмотрев сведенные затраты для всей группы. Дополнительные сведения см. в статье [Обзор диспетчера ресурсов Azure](../azure-resource-manager/resource-group-overview.md). 

Служба аналитики озера данных может включать следующие компоненты:

* Учетная запись аналитики озера данных Azure
* Требуемая учетная запись хранения озера данных Azure по умолчанию
* Дополнительные учетные записи хранения озера данных Azure
* Дополнительные учетные записи хранения Azure

Можно создать все эти компоненты в одной группе управления ресурсами, чтобы ими было проще управлять.

![Аналитика озера данных Azure: учетная запись и хранилище](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Учетная запись аналитики озера данных и зависимые учетные записи хранения должны находиться в одном центре обработки данных Azure.
Однако группа управления ресурсами может находиться в другом центре обработки данных.  

## <a name="see-also"></a>Дополнительные материалы
* [Обзор аналитики озера данных Microsoft Azure](data-lake-analytics-overview.md)
* [Начало работы с аналитикой озера данных с помощью портала Azure](data-lake-analytics-get-started-portal.md)
* [Управление аналитикой озера данных Azure с помощью портала Azure](data-lake-analytics-manage-use-portal.md)
* [Устранение неполадок с заданиями аналитики озера данных Azure с помощью портала Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)




<!--HONumber=Nov16_HO3-->


