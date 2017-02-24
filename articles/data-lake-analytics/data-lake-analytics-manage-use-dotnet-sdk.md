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
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 3ed1b4121e8e64b07abaeb1117f2b8a0cfd75406
ms.openlocfilehash: 7b2380e45c62684ed29fe819db7e254b968d55d0


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Управление Azure Data Lake Analytics с помощью пакета SDK Azure для .NET
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Узнайте, как управлять учетными записями, источниками данных, пользователями и заданиями Azure Data Lake Analytics с помощью пакета SDK Azure для .NET. Для просмотра статей, посвященных управлению с помощью других инструментов, щелкните селектор вкладок выше.

**Предварительные требования**

Для работы с этим учебником необходимо следующее:

* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="create-an-azure-resource-group"></a>Создание группы ресурсов Azure
Создайте группу ресурсов Azure, если она еще не создана, чтобы создать компоненты Data Lake Analytics. Ниже приведен код, создающий группу ресурсов.

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

Дополнительные сведения см. в разделе о [группах ресурсов Azure и Data Lake Analytics](## Azure Resource Groups and Data Lake Analytics).


## <a name="connect-to-azure-data-lake"></a>Подключение к Azure Data Lake
Необходимо установить следующие пакеты Nuget:

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Common
    Install-Package Microsoft.Azure.Common.Dependencies
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
    Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
    Install-Package Microsoft.WindowsAzure.Common
    Install-Package Microsoft.WindowsAzure.Common.Dependencies


В методе Main следующего примера кода показано, как подключиться к Azure и инициализировать объекты управления клиентами Data Lake для учетной записи Analytics и учетной записи службы хранилища.

    using System;
    using System.Collections.Generic;
    using System.Threading;

    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataLake.Analytics;
    using Microsoft.Azure.Management.DataLake.Analytics.Models;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.Azure.Management.DataLake.StoreUploader;

    namespace ConsoleAcplication1
    {
        class Program
        {

            private const string _SubID = "<Specify your Azure subscription ID>"; 
            private const string _ClientID = "1950a258-227b-4e31-a9cf-717495945fc2"; // An ID made availble for developers
            private const string _resourceGroupName ="<Specify your resource group name>";
            private static string _location = "East US 2"; // Specify your location

            // Replace 'common' with user's Azure Active Directory tenant ID or domain name, if needed.
            private const string _Domain = "common"; 

            // Data Lake client management objects
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
            private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobsClient;

            private static void Main(string[] args)
            {

                // Call logon method
                var creds = AuthenticateAzure(_Domain, _ClientID);

                // Initialize Data Lake management client objects, using
                // your credentials (creds). Initialize others as needed.
                _adlsClient = new DataLakeStoreAccountManagementClient(creds);
                _adlsClient.SubscriptionId = _SubID;
                
                _adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
                _adlaClient.SubscriptionId = _SubID; 


                // Methods to create and manage Data Lake accounts and resources
                . . .

            }

            // Interactive logon
            public static ServiceClientCredentials AuthenticateAzure(string domainName, string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());

                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }
        }
    }

## <a name="data-lake-client-management-objects"></a>Объекты управления клиентами Data Lake
Пакет SDK для Azure Data Lake включает наборы объектов управления клиентами, в которых выполняется большая часть программирования. Эти объекты находятся в двух пространствах имен:
* Mirosoft.Azure.Management.DataLake.Analytics;
* Microsot.Azure.Management.DataLake.Store.

В следующей таблице перечислены эти объекты и их переменные, используемые в примерах в этой статье.

| Объект управления клиентами                  | Переменная кода         |
| ----------------------------------------- | --------------------- |
| DataLakeStoreAccountManagementClient      | _adlsClient           |
| DataLakeAnalyticsAccountManagementClient  | _adlaClient           |
| DataLakeStoreFileSystemManagementClient   | _adlsFileSystemClient |
| DataLakeAnalyticsCatalogManagementClient  | _adlaCatalogClient    |
| DataLakeAnalyticsJobManagementClient      | _adlaJobsClient       |

### <a name="data-lake-store-management-client-objects"></a>Объекты управления клиентами Data Lake Store:
* DataLakeStoreAccountManagementClient используется для создания хранилищ Data Lake Store и управления ими.
* DataLakeFileSystemAccountManagementClient используется для выполнения задач файловой системы, таких как создание папок и файлов, передача файлов, перечисление файлов, доступ к спискам управления доступом и к учетным данным, а также добавление ссылок на Azure Storage Blob.

Несмотря на то, что ссылки на службу хранилища Azure можно создавать в Data Lake, вы не сможете получить доступ к содержимому таких ссылок. Для этого необходимо использовать API пакета SDK для службы хранилища Azure. Тем не менее в Azure Storage Blob можно выполнять сценарии U-SQL.

### <a name="data-lake-analytics-management-client-objects"></a>Объекты управления клиентами Data Lake Analytics:
* DataLakeAnaylyticsAccountManagementClient используется для создания учетных записей в службе аналитики Data Lake и управления ими.
* DataLakeAnalyticsCatalogManagementClient используется для настройки баз данных SQL, в том числе для схемы перечисления.
* DataLakeAnalyticsJobManagementClient используется для создания заданий U-SQL и управления ими.

## <a name="create-accounts"></a>Создание учетных записей
Перед выполнением любого задания аналитики озера данных необходимо иметь учетную запись аналитики озера данных. В отличие от Azure HDInsight учетная запись аналитики не оплачивается, если ни одно задание не выполняется.  Вы платите только за время выполнения задания.  Дополнительные сведения см. в разделе [Обзор аналитики озера данных Azure](data-lake-analytics-overview.md).

Кроме того, для учетной записи Data Lake Analytics необходима учетная запись Data Lake Store.
  
### <a name="create-a-data-lake-store-account"></a>Создание учетной записи хранения озера данных
Ниже приведен код, создающий учетную запись Data Lake Store. Прежде чем использовать метод Create, необходимо определить его параметры, указав расположение.

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

### <a name="create-a-data-lake-analytics-account"></a>Создание учетной записи аналитики озера данных
Ниже приведен код, создающий учетную запись Data Lake Analytics. Метод Create объекта DataLakeAnalyticsAccountManagementClient принимает коллекцию учетных записей Data Lake Store в качестве одного из своих параметров. Эту коллекцию нужно заполнить экземплярами объектов DataLakeStoreAccountInfo. В этом примере объекты DataLakeStoreAccountInfo поступают из вспомогательного метода (AdlaFromAdlsStoreAccounts). Кроме того, не все учетные записи Data Lake Store в подписке должны находиться в одной учетной записи Data Lake Analytics, поэтому этот код проверяет имена по утвержденному списку.

        // create analytics account
        public void CreateAnalyticsAccount(string acctname)
        {
            IEnumerable<DataLakeStoreAccountInfo> dlaInfos = AdlaFromAdlsStoreAccounts();

            var dlInfo = new DataLakeAnalyticsAccount()
            {
                DefaultDataLakeStoreAccount = _adlsAccountName,
                Location = _location,
                DataLakeStoreAccounts = dlaInfos.ToList<DataLakeStoreAccountInfo>()
            };

            _adlaClient.Account.Create(_resourceGroupName, acctname, dlInfo);
        }

        // A helper method to collect Data Lake Store account information to create an  
        // an analytics account, and also validates accounts before including.
        public IEnumerable<DataLakeStoreAccountInfo> AdlaFromAdlsStoreAccounts()
        {
            List<DataLakeStoreAccount> adlsAccounts = _adlsClient.Account.List().ToList();

            // Create a collection for approved accounts
            List<DataLakeStoreAccount> approvedAccounts = new List<DataLakeStoreAccount>();

            foreach (DataLakeStoreAccount dlsa in adlsAccounts)
            {
                // The IsApprovedDataStore method (not shown) 
                // evaluates a Data Lake store name.
                if (IsApprovedDataStore(dlsa.Name))
                {
                    approvedAccounts.Add(dlsa);
                }
            }

            return approvedAccounts.Select(element => new DataLakeStoreAccountInfo(element.Name));
        }

## <a name="manage-accounts"></a>Управление учетными записями

### <a name="list-data-lake-store-and-analytic-accounts"></a>Получение списка учетных записей Data Lake Store и Data Lake Analytics
Следующий код выводит список учетных записей Data Lake Store в подписке. Операции List не всегда предоставляют все свойства объекта. В некоторых случаях с объектом необходимо выполнить операцию Get.
            
    var adlsAccounts = _adlsClient.Account.List().ToList();
    Console.WriteLine($"You have {adlsAccounts.Count} Data Lake Store accounts.");
    for (int i = 0; i < adlsAccounts.Count; i++)
    {
        Console.WriteLine($"\t{adlsAccounts[i].Name}");
    }

    var adlaAccounts = _adlaClient.Account.List().ToList();
    Console.WriteLine($"\nYou have {adlaAccounts.Count} Data Lake Analytic accounts.");
    for (int j = 0; j < adlaAccounts.Count; j++)
    {
        Console.WriteLine($"\t{adlaAccounts[j].Name}");
    }
        

        
### <a name="get-an-account"></a>Получение учетной записи
В следующем коде используется объект DataLakeAnalyticsAccountManagementClient для возвращения учетной записи Data Lake Analytics, если она существует. 

    public DataLakeAnalyticsAccount GetDlaAccount(string strName)
    {
        DataLakeAnalyticsAccount dlaGet;
        if (_adlaClient.Account.Exists(_resourceGroupName, strName))
        {
            dlaGet = _adlaClient.Account.Get(_resourceGroupName, strName);
            Console.WriteLine($"{dlaGet.Name}\tCreated: {dlaGet.CreationTime}");
            return dlaGet;
        }
        else
        {
            return null;
        }

Таким же образом можно использовать объект DataLakeStoreAccountManagementClient (_adlsClient), чтобы получить учетную запись Data Lake Store.        
### <a name="delete-an-account"></a>Удаление учетной записи
Ниже приведен код, удаляющий учетную запись Data Lake Analytics, если она существует. 

    public void DeleteAnalyticsAccount(string strName)
    {
        if (_adlaClient.Account.Exists(_resourceGroupName, strName))
        {
            _adlaClient.Account.Delete(_resourceGroupName, strName);
            Console.WriteLine($"{strName} Deleted");
        }
        else
        {
            Console.WriteLine($"{strName} does not exist.");
        }

    }

Учетную запись Data Lake Store можно удалить таким же образом, используя объект DataLakeStoreAccountManagementClient.

### <a name="get-the-default-data-lake-store-account"></a>Получение учетной записи Data Lake Store по умолчанию
Для каждой учетной записи Data Lake Analytics необходима учетная запись Data Lake Store по умолчанию. Этот код позволяет определить учетную запись хранения по умолчанию для учетной записи Analytics.

    public void GetDefaultDLStoreAccount(string DLAaccountName)
    {
        if (_adlaClient.Account.Exists(_resourceGroupName, DLAaccountName))
        {
            DataLakeAnalyticsAccount dlaGet = _adlaClient.Account.Get(_resourceGroupName, DLAaccountName);
            Console.WriteLine($"{dlaGet.Name} default DL store account: {dlaGet.DefaultDataLakeStoreAccount}");
        }
    }

## <a name="manage-data-sources"></a>Управление источниками данных
Аналитика озера данных в настоящее время поддерживает следующие источники данных:

* [Хранилище озера данных Azure](../data-lake-store/data-lake-store-overview.md)
* [Хранилище Azure](../storage/storage-introduction.md)

При создании учетной записи аналитики необходимо указать учетную запись хранения озера данных Azure в качестве учетной записи хранения по умолчанию. Учетная запись хранения озера данных по умолчанию используется для хранения метаданных задания и журналов аудита задания. После создания учетной записи Analytics можно добавить дополнительные учетные записи хранения Data Lake и ссылки на учетные записи хранения Azure. 

### <a name="include-a-link-to-azure-storage-in-data-lake"></a>Добавление ссылки на службу хранилища Azure в Data Lake
В среде Data Lake можно создавать ссылки на Azure Storage Blob. 

    string storageKey = "<paste the key value here>";

    AddStorageAccountParameters addParams = new AddStorageAccountParameters(storageKey);            
    _adlaClient.StorageAccounts.Add(_resourceGroupName, _adlaAccountName, "<Azure Storage Account Name>", addParams);

### <a name="list-data-lake-data-sources"></a>Получение списка источников данных Data Lake
В следующем коде перечислены учетные записи Data Lake Store и учетные записи хранения Data Lake (для службы хранилища Azure) для указанной учетной записи Data Lake Analytics.

    var sAccnts = _adlaClient.StorageAccounts.ListByAccount(_resourceGroupName, acctName);

    if (sAccnts != null)
    {
        Console.WriteLine("Storage accounts:");
        foreach (var a in sAccnts)
        {
            Console.WriteLine($"\t{a.Name}");
        }
    }

    var stores = _adlsClient.Account.List();
    if (stores != null)
    {
        Console.WriteLine("\nData stores:");
        foreach (var s in stores)
        {
            Console.WriteLine($"\t{s.Name}");
        }
    }

### <a name="upload-a-file-to-a-data-lake-store-account"></a>Отправка файла в учетную запись Data Lake Store
В следующем коде используется объект DataLakeStoreFileSystemManagementClient для отправки локального файла в учетную запись Data Lake Store.

    bool force = true;
    string adlsAccnt = "Accounting";
    string srcFilePath = @"c:\DataLakeTemp\localData.csv";
    string dstFilePath = "/Reports/FY2016/2016data.csv";
    var parameters = new UploadParameters(srcFilePath, dstFilePath, adlsAccnt, isOverwrite: force);
    var frontend = new DataLakeStoreFrontEndAdapter(adlsAccnt, _adlsFileSystemClient);
    var uploader = new DataLakeStoreUploader(parameters, frontend);
    uploader.Execute();

### <a name="create-a-file-in-a-data-lake-store-account"></a>Создание файла в учетной записи Data Lake Store
Помимо отправки файлов их можно легко создавать программным образом в учетной записи Data Lake Store для анализа. Следующий код записывает первые четыре значения из 100 случайных массивов байтов в CSV-файл.

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

        _adlsFileSystemClient.FileSystem.Create(adlsAccoutName, "/Samples/Output/randombytes.csv", azMem);

        sw.Dispose();
        azMem.Dispose();

### <a name="copy-files-from-a-data-lake-store-account"></a>Копирование файлов из учетной записи Data Lake Store
В следующем коде показано использование объекта DataLakeFileSystemAccountManagementClient для выполнения операций файловой системы. Он копирует файлы электронной таблицы (CSV) из каталога Samples/Data/AmbulanceData в локальный каталог на компьютере.

    // This method takes the name of a Data Lake Store account,
    // and the the path to a directory in the account. In this

    public void CopyCSVFiles(string accnt, string fPath)
    {
        try
        {
            if (_adlsFileSystemClient.FileSystem.PathExists(accnt,fPath))
            {
                var fStatus = _adlsFileSystemClient.FileSystem.ListFileStatus(accnt, fPath);
                foreach (var fs in fStatus.FileStatuses.FileStatus)
                {
                    string localF = string.Empty;
                    if (fs.Type == Microsoft.Azure.Management.DataLake.Store.Models.FileType.FILE &&
                        fs.PathSuffix.Contains("csv"))
                    {
                        Stream fStream = _adlsFileSystemClient.FileSystem.Open(accnt, fPath + "/" + fs.PathSuffix);
                        localF = @"c:\DataLakeTemp\" + fs.PathSuffix;
                        FileStream localStream = new FileStream(localF, FileMode.Create);
                        fStream.CopyTo(localStream);

                    }
                    Console.WriteLine($"Copied {localF}.");
                }
            }
            else
            {
                Console.WriteLine($"File path {fPath} does not exist.");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }

### <a name="list-azure-storage-containers"></a>Получение списка контейнеров службы хранилища Azure
Следующий код выводит список контейнеров для указанной учетной записи хранения Azure.

    string DLAName = "<specify Data Lake Analytics account name>";
    string azStorageName = "<specify Azure Storage account name>";
    var containers = _adlaClient.StorageAccounts.ListStorageContainers(_resourceGroupName, DLAName, azStorageName);
    foreach (var c in containers)
    {
       Console.WriteLine(c.Name);
    }

### <a name="verify-azure-storage-account-paths"></a>Проверка путей к учетной записи хранения Azure
Следующий код проверяет наличие учетной записи хранения Azure (storageAccntName) в учетной записи Data Lake Analytics (analyticsAccountName), а также наличие контейнера (containerName) в учетной записи хранения Azure. 

    bool accountExists = _adlaClient.Account.StorageAccountExists(_resourceGroupName, analyticsAccountName, storageAccntName));
    bool containerExists = _adlaClient.Account.StorageContainerExists(_resourceGroupName, analyticsAccountName, storageAccntName, containerName));

## <a name="manage-catalog-and-jobs"></a>Управление каталогами и заданиями
Объект DataLakeAnalyticsCatalogManagementClient предоставляет методы для управления базой данных SQL, предоставляемой для каждого Azure Data Lake Store. Объект DataLakeAnalyticsJobManagementClient предоставляет методы для отправки заданий, выполняемых в базе данных с помощью сценариев U-SQL, и управления ими.

### <a name="list-databases-and-schemas"></a>Получение списка баз данных и схем
Самые распространенные компоненты, список которых можно получить — это базы данных и их схемы. Следующий код получает набор баз данных и перечисляет схемы для каждой из них.

    private void ListCatalogItems(string dlaAccountName)
    {
        var databases = _adlaCatalogClient.Catalog.ListDatabases(dlaAccountName);
        foreach (var db in databases)
        {
            Console.WriteLine($"Database: {db.Name}");
            Console.WriteLine(" - Schemas:");
            var schemas = _adlaCatalogClient.Catalog.ListSchemas(dlaAccountName, db.Name);
            foreach (var schm in schemas)
            {
                Console.WriteLine($"\t{schm.Name}");
            }
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

    var tbl = _adlaCatalogClient.Catalog.GetTable(_adlaAnalyticsAccountTest, "master", "dbo", "MyTableName");
    IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

    foreach (USqlTableColumn utc in columns)
    {
        string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
        Stream scriptStrm = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
        string scriptTxt = string.Empty;
        using (StreamReader sr = new StreamReader(scriptStrm))
        {
            scriptTxt = sr.ReadToEnd();
        }

        var jobName = "SR_Wikipedia";
        var jobId = Guid.NewGuid();
        var properties = new USqlJobProperties(scriptTxt);
        var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
        var jobInfo = _adlaJobsClient.Job.Create(_adlaAnalyticsAccountTest, jobId, parameters);
        Console.WriteLine($"Job {jobName} submitted.");

    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }


### <a name="list-failed-jobs"></a>Получение списка невыполненных заданий
Ниже приведен код, получающий список сведений о невыполненных заданиях.

    var jobs = _adlaJobsClient.Job.List(_adlaAnalyticsAccountName);

    foreach (var j in jobs)
    {
        if (j.Result == JobResult.Failed)
        {
            Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
        }
    }
### <a name="reference-azure-storage-in-u-sql-scripts"></a>Создание ссылки на службу хранилища Azure в сценарии U-SQL
Следующий код является началом сценария U-SQL. Этот сценарий указывает, что нужно читать данные из файла в учетной записи Data Lake Store (/Samples/Data/SearchLog.tsv).

    @searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

Для чтения данных из большого двоичного объекта в связанной учетной записи хранения Azure необходимо использовать полный URL-адрес большого двоичного объекта в следующем формате:

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/<path to source>

Например, если исходный файл (SearchLog.tsv) хранится в контейнере samples в учетной записи хранения contso_33, путь к инструкции FROM будет следующим:

    FROM: "wasb://samples@constoso_33.blob.core.windows.net/SearchLog.tsv"

## <a name="azure-resource-groups-and-data-lake-analytics"></a>Группы ресурсов Azure и Data Lake Analytics
Обычно приложения состоят из множества компонентов, например веб-приложения, базы данных, сервера базы данных, хранилища и служб сторонних поставщиков. Azure Resource Manager позволяет работать с ресурсами в приложении в виде группы, которая называется группой ресурсов Azure. Вы можете развертывать, обновлять, отслеживать или удалять все ресурсы для приложения в рамках одной скоординированной операции. Развертывание осуществляется на основе шаблона, используемого для разных сред, в том числе для тестовой, промежуточной и рабочей. Дополнительные сведения см. в статье [Обзор диспетчера ресурсов Azure](../azure-resource-manager/resource-group-overview.md). 

Служба аналитики озера данных может включать следующие компоненты:

* Учетная запись аналитики озера данных Azure
* Требуемая учетная запись хранения озера данных Azure по умолчанию
* Одна или несколько учетных записей Azure Data Lake Analytics.
* Одна или несколько учетных записей Azure Data Lake Store (требуется как минимум одна).
* Дополнительные связанные учетные записи хранения Azure Data Lake.
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



<!--HONumber=Feb17_HO1-->


