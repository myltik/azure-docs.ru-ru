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
ms.date: 03/3/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: bed6fa355f3b32bb53aee002e34ca61f2ea2aa5b
ms.lasthandoff: 03/06/2017


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Управление Azure Data Lake Analytics с помощью пакета SDK Azure для .NET
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Узнайте, как управлять учетными записями, источниками данных, пользователями и заданиями Azure Data Lake Analytics с помощью пакета SDK Azure для .NET. Для просмотра статей, посвященных управлению с помощью других инструментов, щелкните селектор вкладок выше.

## <a name="prerequisites"></a>Предварительные требования

* **Visual Studio 2015, Visual Studio 2013 с обновлением 4 или Visual Studio 2012 с установленным Visual C++**
* **Microsoft Azure SDK для .NET (версии 2.5 или выше)**.  Вы можете установить его с помощью [установщика веб-платформы](http://www.microsoft.com/web/downloads/platform.aspx).
* **Необходимые пакеты Nuget**

### <a name="install-nuget-packages"></a>Установка пакетов Nuget
   
   1. В Visual Studio в обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**.
   2. На вкладке **Диспетчер пакетов NuGet** в поле **Источник пакета** выберите **nuget.org** и установите флажок **Включить предварительные выпуски**.

   3. Найдите и установите следующие пакеты NuGet:

    - Microsoft.Rest.ClientRuntime.Azure.Authentication (в этом учебнике используется версия 2.2.12)
    - Microsoft.Azure.Management.DataLake.Analytics (в этом учебник используется предварительная версия 2.1.0)
    - Microsoft.Azure.Management.DataLake.Store (в этом учебник используется предварительная версия 2.1.0)

   4. Закройте **Диспетчер пакетов Nuget**.

## <a name="client-management-objects"></a>Объекты управления клиентами
API-интерфейсы Azure Data Lake и Azure Data Lake Store включают наборы объектов управления клиентами, в которых выполняется большая часть программирования. Эти объекты находятся в двух пространствах имен:
* Microsoft.Azure.Management.DataLake.Analytics
* Microsoft.Azure.Management.DataLake.Store

В следующей таблице приведены объекты управления клиентами с переменными, которые используются в примерах кода в этой статье.

| Объект управления клиентами                  | Переменная кода        |
| ----------------------------------------- | -------------------- |
| DataLakeStoreAccountManagementClient      | adlsClient           |
| DataLakeAnalyticsAccountManagementClient  | adlaClient           |
| DataLakeStoreFileSystemManagementClient   | adlsFileSystemClient |
| DataLakeAnalyticsCatalogManagementClient  | adlaCatalogClient    |
| DataLakeAnalyticsJobManagementClient      | adlaJobClient        |

### <a name="data-lake-store-management-client-objects"></a>Объекты управления клиентами Data Lake Store:
* DataLakeStoreAccountManagementClient используется для создания учетных записей Data Lake Store и управления ими.
* DataLakeFileSystemAccountManagementClient используется для выполнения задач файловой системы, таких как создание папок и файлов, передача файлов, перечисление файлов, доступ к спискам управления доступом и к учетным данным, а также добавление ссылок на Azure Storage Blob.

Несмотря на то, что ссылки на службу хранилища Azure можно создавать в Data Lake, вы не сможете получить доступ к содержимому таких ссылок. Для этого необходимо использовать API пакета SDK для службы хранилища Azure. Тем не менее в Azure Storage Blob можно выполнять сценарии U-SQL.

### <a name="data-lake-analytics-management-client-objects"></a>Объекты управления клиентами Data Lake Analytics:
* DataLakeAnalyticsAccountManagementClient используется для создания учетных записей Data Lake Analytics и управления ими.
* DataLakeAnalyticsCatalogManagementClient используется для просмотра элементов каталога в Data Lake Analytics.
* DataLakeAnalyticsJobManagementClient используется для отправки заданий и управления ими в Data Lake Analytics.

### <a name="example"></a>Пример

Инициализируйте объекты управления клиентом, используя свои учетные данные, применяемые в предпочтительном методе проверки подлинности, описанном далее в этой статье. 

    // Only the Data Lake Analytics and Data Lake Store  
    // objects need a subscription ID.
    adlsClient = new DataLakeStoreAccountManagementClient(creds);
    adlsClient.SubscriptionId = <Subscription-ID>;

    adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
    adlaClient.SubscriptionId = <Subscription-ID>;

    adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);
    adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(creds);
    adlaJobClient = new DataLakeAnalyticsJobManagementClient(creds);


    // Methods to create and manage Data Lake Analytics
    . . .

## <a name="authenticate-and-connect-to-azure-data-lake-analytics"></a>Проверка подлинности и подключение к Azure Data Lake Analytics
Существует несколько способов входа в Azure Data Lake Analytics.

### <a name="interactive-with-provided-credentials"></a>Интерактивный режим с использованием предоставленных учетных данных
В следующем фрагменте кода показан простейший способ проверки подлинности пользователя с использованием учетных данных, таких как имя пользователя и пароль или ПИН-код.

    // User login via interactive popup
    // Use the client ID of an existing AAD "native nlient" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Tenant ID>"; // Replace this string with the user's Azure Active Directory tenant ID.
    var clientId = "1950a258-227b-4e31-a9cf-717495945fc2"; // Sample client ID
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(_tenantId, activeDirectoryClientSettings).Result;

Рекомендуется создать собственное приложение и субъект-службу в клиенте Azure Active Directory и затем использовать для приложения идентификатор клиента, а не приведенный здесь образец идентификатора.

### <a name="non-interactive-with-a-client-secret"></a>Неинтерактивный режим с использованием секрета клиента
Следующий фрагмент можно использовать для проверки подлинности приложения в неинтерактивном режиме с помощью секрета клиента, ключа приложения или субъекта-службы. Примените этот вариант проверки подлинности к существующему [веб-приложению Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

    // Service principal / application authentication with client secret / key
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Azure tenant ID>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(tenantId, clientCredential).Result;

### <a name="non-interactive-with-a-service-principal"></a>Неинтерактивный режим с использованием субъекта-службы
Приведенный ниже фрагмент можно использовать для проверки подлинности приложения в неинтерактивном режиме с помощью сертификата приложения или субъекта-службы. Примените этот вариант проверки подлинности к существующему [веб-приложению Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Azure tenant ID>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(tenantId, clientAssertionCertificate).Result;

## <a name="create-accounts"></a>Создание учетных записей
Перед выполнением любого задания аналитики озера данных необходимо иметь учетную запись аналитики озера данных. Кроме того, для учетной записи Data Lake Analytics необходима учетная запись Data Lake Store. Дополнительные сведения см. в разделе [Обзор Azure Data Lake Analytics](data-lake-analytics-overview.md).

### <a name="create-an-azure-resource-group"></a>Создание группы ресурсов Azure
Создайте группу ресурсов Azure, если она еще не создана, чтобы создать компоненты Data Lake Analytics. Потребуются учетные данные для проверки подлинности, идентификатор подписки и данные о расположении. Ниже приведен код, создающий группу ресурсов.

    string rgName == "<value>"; // specify name for the new resrouce group
    var resourceManagementClient = new ResourceManagementClient(credential) { SubscriptionId = subscriptionId };
    var resourceGroup = new ResourceGroup { Location = location };
    resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, rgName);

Дополнительные сведения см. в разделе [Группы ресурсов Azure и Data Lake Analytics](#Azure-Resource-Groups-and-Data-Lake-Analytics).


### <a name="create-a-data-lake-store-account"></a>Создание учетной записи хранения озера данных
Ниже приведен код, создающий учетную запись Data Lake Store. Прежде чем использовать метод Create, необходимо определить его параметры, указав расположение.

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

### <a name="create-a-data-lake-analytics-account"></a>Создание учетной записи аналитики озера данных
Ниже приведен код, создающий учетную запись Data Lake Analytics с помощью асинхронного метода. В качестве одного из своих параметров метод CreateAsync принимает коллекцию учетных записей Data Lake Store. Эту коллекцию нужно заполнить экземплярами объектов DataLakeStoreAccountInfo. В этом примере объекты DataLakeStoreAccountInfo поступают из вспомогательного метода (AdlaFromAdlsStoreAccounts).

Для любой учетной записи Data Lake Analytics необходимо включить учетные записи Data Lake Store, которые потребуются для выполнения операций анализа. Одна из этих учетных записей Data Lake Store должна быть учетной записью Data Lake Store по умолчанию.

    try
    {
        var adlaAccount = new DataLakeAnalyticsAccount()
        {
            DefaultDataLakeStoreAccount = “Accounting”,
            Location = _location,
            DataLakeStoreAccounts = new DataLakeStoreAccountInfo[]{
                new DataLakeStoreAccountInfo(“Expenditures”),
                new DataLakeStoreAccountInfo(“Accounting”)
            }
        };
        adlaClient.Account.Create(_resourceGroupName, newAccountName, adlaAccount);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }

## <a name="manage-accounts"></a>Управление учетными записями

### <a name="list-data-lake-store-and-data-lake-analytics-accounts"></a>Получение списка учетных записей Data Lake Store и Data Lake Analytics
Ниже приведен код, выводящий список учетных записей Data Lake Store в подписке. Операции List не всегда предоставляют все свойства объекта. В некоторых случаях с объектом необходимо выполнить операцию Get.

    var adlsAccounts = adlsClient.Account.List().ToList();
    foreach (var adls in adlsAccounts)
    {
        Console.WriteLine($"\t{adls.Name});

    }

    var adlaAccounts = adlaClient.Account.List().ToList();
    for (var adla in AdlaAccounts)
    {
        Console.WriteLine($"\t{adla.Name}");
    }



### <a name="get-an-account"></a>Получение учетной записи
В следующем коде используется объект DataLakeAnalyticsAccountManagementClient для возвращения учетной записи Data Lake Analytics. Сначала проверяется, существует ли учетная запись.

    DataLakeAnalyticsAccount adlaGet;
    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        adlaGet = adlaClient.Account.Get(_resourceGroupName, accountName);
        Console.WriteLine($"{adlaGet.Name}\tCreated: {adlaGet.CreationTime}");
    }

Таким же образом можно использовать объект DataLakeStoreAccountManagementClient (adlsClient), чтобы получить учетную запись Data Lake Store.

### <a name="delete-an-account"></a>Удаление учетной записи
Ниже приведен код, удаляющий учетную запись Data Lake Analytics, если она существует.

    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        adlaClient.Account.Delete(_resourceGroupName, accountName);
        Console.WriteLine($"{accountName} Deleted");
    }
    else
    {
        Console.WriteLine($"{accountName} does not exist.");
    }

Учетную запись Data Lake Store можно удалить таким же образом, используя объект DataLakeStoreAccountManagementClient.

### <a name="get-the-default-data-lake-store-account"></a>Получение учетной записи Data Lake Store по умолчанию
Для каждой учетной записи Data Lake Analytics необходима учетная запись Data Lake Store по умолчанию. Этот код позволяет определить учетную запись хранения по умолчанию для учетной записи Analytics.

    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        DataLakeAnalyticsAccount adlaGet = adlaClient.Account.Get(_resourceGroupName, accountName);
        Console.WriteLine($"{adlaGet.Name} default DL store account: {adlaGet.DefaultDataLakeStoreAccount}");
    }


## <a name="manage-data-sources"></a>Управление источниками данных
Аналитика озера данных в настоящее время поддерживает следующие источники данных:

* [Хранилище озера данных Azure](../data-lake-store/data-lake-store-overview.md)
* [Учетная запись хранения Azure](../storage/storage-introduction.md)

При создании учетной записи Data Lake Analytics необходимо указать учетную запись Azure Data Lake Store в качестве учетной записи Data Lake Store. Учетная запись хранения озера данных по умолчанию используется для хранения метаданных задания и журналов аудита задания. После создания учетной записи Data Lake Analytics можно добавить дополнительные учетные записи Data Lake Store и ссылки на учетные записи хранения Azure (BLOB-объекты).

### <a name="link-to-an-azure-storage-account-from-a-data-lake-analytics-account"></a>Ссылка на учетную запись хранения Azure из учетной записи Data Lake Analytics
Можно создать ссылки на учетные записи хранения Azure.

    AddStorageAccountParameters addParams = new AddStorageAccountParameters(<storage key value>);            
    adlaClient.StorageAccounts.Add(_resourceGroupName, _adlaAccountName, "<Azure Storage Account Name>", addParams);

### <a name="list-data-lake-store-data-sources"></a>Получение списка источников данных Data Lake
Следующий код используется для получения списка учетных записей Data Lake Store и учетных записей хранения Azure, используемых для указанной учетной записи Data Lake Analytics.

    var sAccnts = adlaClient.StorageAccounts.ListByAccount(_resourceGroupName, acctName);

    if (sAccnts != null)
    {
        Console.WriteLine("Azure Storage accounts:");
        foreach (var a in sAccnts)
        {
            Console.WriteLine($"\t{a.Name}");
        }
    }

    var stores = adlsClient.Account.List();
    if (stores != null)
    {
        Console.WriteLine("\nData stores:");
        foreach (var s in stores)
        {
            Console.WriteLine($"\t{s.Name}");
        }
    }

### <a name="upload-and-download-folders-and-files"></a>Отправка и загрузка папок и файлов
Объект управления клиентами файловой системы Data Lake Store можно использовать для отправки и загрузки отдельных файлов или папок из Azure на локальный компьютер с помощью следующих методов:

- UploadFolder
- UploadFile
- DownloadFolder
- DownloadFile

Первый параметр для этих методов переставляет собой имя учетной записи Data Lake Store. За ним следуют параметры для исходного пути и целевого пути.

В следующем примере показано, как загрузить папку в Data Lake Store.


    try
    {
        if (adlsFileSystemClient.FileSystem.PathExists(account, sourcePath))
        {
            adlsFileSystemClient.FileSystem.DownloadFolder(account, sourcePath, destinationPath);
        }
        else
        {
            Console.WriteLine("Path does not exist");
        }
    }
    catch (IOException ioex)
    {
        Console.WriteLine(ioex.Message);
    }


### <a name="create-a-file-in-a-data-lake-store-account"></a>Создание файла в учетной записи Data Lake Store
Чтобы создать содержимое для файла в Data Lake Store, можно использовать операции ввода-вывода в .NET Framework. Следующий код записывает первые четыре значения из 100 случайных массивов байтов в CSV-файл.

    MemoryStream azMem = new MemoryStream();
    StreamWriter sw = new StreamWriter(azMem, UTF8Encoding.UTF8);

    for (int i = 0; i < 100; i++)
    {
        byte[] gA = Guid.NewGuid().ToByteArray();
        string dataLine = string.Format($"{gA[0].ToString()},{gA[1].ToString()},{gA[2].ToString()},{gA[3].ToString()},{gA[4].ToString()}");
        sw.WriteLine(dataLine);
    }
    sw.Flush();
    azMem.Position = 0;

    adlsFileSystemClient.FileSystem.Create(adlsAccoutName, "/Samples/Output/randombytes.csv", azMem);

    sw.Dispose();
    azMem.Dispose();

### <a name="list-blob-containers-of-an-azure-storage-account"></a>Получение списка контейнеров BLOB-объектов учетной записи хранения Azure
Следующий код выводит список контейнеров для указанной учетной записи хранения Azure.

    string ADLAName = "<specify Data Lake Analytics account name>";
    string azStorageName = "<specify Azure Storage account name>";
    var containers = adlaClient.StorageAccounts.ListStorageContainers(_resourceGroupName, ADLAName, azStorageName);
    foreach (var c in containers)
    {
       Console.WriteLine(c.Name);
    }

### <a name="verify-azure-storage-account-paths"></a>Проверка путей к учетной записи хранения Azure
Следующий код проверяет наличие учетной записи хранения Azure (storageAccntName) в учетной записи Data Lake Analytics (analyticsAccountName), а также наличие контейнера (containerName) в учетной записи хранения Azure.

    bool accountExists = adlaClient.Account.StorageAccountExists(_resourceGroupName, analyticsAccountName, storageAccntName));
    bool containerExists = adlaClient.Account.StorageContainerExists(_resourceGroupName, analyticsAccountName, storageAccntName, containerName));

## <a name="manage-catalog-and-jobs"></a>Управление каталогами и заданиями
Объект DataLakeAnalyticsCatalogManagementClient предоставляет методы для управления базой данных SQL, предоставляемой для каждого Azure Data Lake Store. Объект DataLakeAnalyticsJobManagementClient предоставляет методы для отправки заданий, выполняемых в базе данных с помощью сценариев U-SQL, и управления ими.

### <a name="list-databases-and-schemas"></a>Получение списка баз данных и схем
Самые распространенные компоненты, список которых можно получить — это базы данных и их схемы. Следующий код получает набор баз данных и перечисляет схемы для каждой из них.

    var databases = adlaCatalogClient.Catalog.ListDatabases(adlaAccountName);
    foreach (var db in databases)
    {
        Console.WriteLine($"Database: {db.Name}");
        Console.WriteLine(" - Schemas:");
        var schemas = adlaCatalogClient.Catalog.ListSchemas(dlaAccountName, db.Name);
        foreach (var schm in schemas)
        {
            Console.WriteLine($"\t{schm.Name}");
        }
    }

Если выполнить пример кода в базе данных master по умолчанию, выходные данные будут выглядеть следующим образом:

    Database: master
    - Schemas:
            dbo
            INFORMATION_SCHEMA
            sys
            usql

### <a name="list-table-columns"></a>Получение списка столбцов таблицы
Ниже приведен код, получающий доступ к базе данных с помощью клиента управления каталогами Data Lake Analytics для получения списка столбцов в указанной таблице.

    var tbl = adlaCatalogClient.Catalog.GetTable(_adlaAnalyticsAccountTest, "master", "dbo", "MyTableName");
    IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

    foreach (USqlTableColumn utc in columns)
    {
        string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
        Stream scriptStrm = adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
        string scriptTxt = string.Empty;
        using (StreamReader sr = new StreamReader(scriptStrm))
        {
            scriptTxt = sr.ReadToEnd();
        }

        var jobName = "SR_Wikipedia";
        var jobId = Guid.NewGuid();
        var properties = new USqlJobProperties(scriptTxt);
        var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
        var jobInfo = adlaJobClient.Job.Create(_adlaAnalyticsAccountTest, jobId, parameters);
        Console.WriteLine($"Job {jobName} submitted.");

    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }


### <a name="list-failed-jobs"></a>Получение списка невыполненных заданий
Ниже приведен код, получающий список сведений о невыполненных заданиях.

    var jobs = adlaJobClient.Job.List(adlaClient,
        new ODataQuery<JobInformation> { Filter = "result eq 'Failed'" });
    foreach (var j in jobs)
    {
        Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
    }


## <a name="see-also"></a>Дополнительные материалы
* [Обзор аналитики озера данных Microsoft Azure](data-lake-analytics-overview.md)
* [Начало работы с аналитикой озера данных с помощью портала Azure](data-lake-analytics-get-started-portal.md)
* [Управление аналитикой озера данных Azure с помощью портала Azure](data-lake-analytics-manage-use-portal.md)
* [Устранение неполадок с заданиями аналитики озера данных Azure с помощью портала Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

