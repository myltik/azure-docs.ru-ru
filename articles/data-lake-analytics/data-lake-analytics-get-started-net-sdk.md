<properties 
   pageTitle="Начало работы с аналитикой озера данных Azure с помощью пакета SDK .NET | Azure" 
   description="Узнайте, как использовать пакет SDK .NET для создания учетных записей хранения озера данных, создания заданий аналитики озера данных и отправки заданий на языке U-SQL." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/25/2016"
   ms.author="edmaca"/>

# Учебник. Начало работы с аналитикой озера данных Azure с помощью пакета SDK .NET

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Узнайте, как использовать пакет SDK Azure .NET для создания учетных записей аналитики озера данных Azure, определения заданий аналитики озера данных в [U-SQL](data-lake-analytics-u-sql-get-started.md) и отправки заданий в учетные записи аналитики озера данных. Дополнительные сведения об аналитике озера данных см. в статье [Обзор аналитики озера данных Azure](data-lake-analytics-overview.md).

В этом учебнике вам предстоит разработать консольное приложение C#, содержащее скрипт U-SQL, который считывает файл с разделителями-табуляциями (TSV) и преобразует его в файл с разделителями-запятыми (CSV). Для навигации по учебнику с помощью других поддерживаемых средств используйте вкладки в верхней части этого раздела.

**Базовый процесс аналитики озера данных:**

![Блок-схема процесса аналитики озера данных Azure](./media/data-lake-analytics-get-started-portal/data-lake-analytics-process.png)

1. Создайте учетную запись аналитики озера данных.
2. Подготовьте исходные данные. Задания аналитики озера данных могут считывать данные из учетных записей хранения озера данных Azure или учетных записей хранения больших двоичных объектов Azure.   
3. Разработайте скрипт U-SQL.
4. Отправьте задание (скрипт U-SQL) в учетную запись аналитики озера данных. Задание считывает значения из исходных данных, обрабатывает данные, как описывается в скрипте U-SQL, и сохраняет выходные данные в учетной записи хранения озера данных или в учетной записи хранения больших двоичных объектов.

##Предварительные требования

Перед началом работы с этим учебником необходимо иметь следующее:

- **Visual Studio 2015, Visual Studio 2013 с обновлением 4 или Visual Studio 2012 с установленным Visual C++**
- **Microsoft Azure SDK для .NET (версии 2.5 или выше)**. Вы можете установить его с помощью [установщика веб-платформы](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Средства озера данных для Visual Studio](http://aka.ms/adltoolsvs)**. 
- **Учетная запись аналитики озера данных**. См. статью [Создание учетной записи аналитики озера данных Azure](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).

	Средства озера данных для Visual Studio не позволяют создавать учетные записи в службе аналитики озера данных. Чтобы создать такую учетную запись, используйте портал Azure, Azure PowerShell, пакет Azure SDK для .NET или Azure CLI.

##Создание консольного приложения

В этом учебнике обрабатываются некоторые журналы поиска. Журнал поиска может храниться в хранилище озера данных или в хранилище больших двоичных объектов Azure.

Пример журнала поиска был скопирован в общедоступный контейнер больших двоичных объектов Azure. В приложении загрузите файл на свою рабочую станцию и затем передайте его в учетную запись хранения озера данных по умолчанию.

**Создание приложения**

1. Откройте Visual Studio.
2. Создайте консольное приложение на C#.
3. Откройте консоль управления пакетами Nuget и выполните следующие команды:

        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
        Install-Package WindowsAzure.Storage

4. Добавьте в проект новый файл с именем **SampleUSQLScript.txt**, а затем вставьте в него следующий скрипт U-SQL. Задания аналитики озера данных записываются на языке U-SQL. Дополнительные сведения о языке U-SQL см. в статье [Начало работы с языком U-SQL](data-lake-analytics-u-sql-get-started.md) и в [Справочнике по языку U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

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
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	Этот скрипт U-SQL считывает файл исходных данных с помощью **Extractors.Tsv()**, а затем создает CSV-файл с помощью **Outputters.Csv()**.
    
    В программе C# необходимо подготовить файл **/Samples/Data/SearchLog.tsv** и папку **/Output/**.
	
	Проще использовать относительные пути для файлов, которые хранятся в учетных записях озера данных по умолчанию. Также можно использовать абсолютные пути. Например:
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Необходимо использовать абсолютные пути для доступа к файлам в связанных учетных записях хранения. Для файлов, хранящихся в связанной учетной записи хранения Azure, используется следующий синтаксис:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Контейнер больших двоичных объектов Azure с разрешениями на доступ к общедоступным большим двоичным объектам или общедоступным контейнерам в настоящее время не поддерживается.
       
       
5. Вставьте следующий код в Program.cs:

        using System;
        using System.IO;
        using System.Security;
        using System.Text;
        using System.Collections.Generic;
        using System.Linq;

        using Microsoft.Azure.Management.DataLake.Analytics;
        using Microsoft.Azure.Management.DataLake.Analytics.Models;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;
        using Microsoft.WindowsAzure.Storage.Blob;

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeAnalyticsAccountManagementClient _adlaClient;
                private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
                private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlaAccountName;
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name for a NEW Store account.
                    _adlaAccountName = "<DATA-LAKE-ANALYTICS-NAME>"; // TODO: Replace this value with the name for a NEW Analytics account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value. This resource group should already exist.
                    _location = "East US 2";
                    
                    string localFolderPath = @"C:\local_path"; // TODO: Make sure this exists and contains the U-SQL script.
                    
                    // Authenticate the user
                    // For more information about applications and instructions on how to get a client ID, see: 
                    //   https://azure.microsoft.com/ru-RU/documentation/articles/resource-group-create-service-principal-portal/
                    var tokenCreds = AuthenticateUser("common", "https://management.core.windows.net/",
                        "<APPLICATION-CLIENT-ID>", new Uri("https://<APPLICATION-REDIRECT-URI>")); // TODO: Replace bracketed values.
                    
                    SetupClients(tokenCreds, "<SUBSCRIPTION-ID>"); // TODO: Replace bracketed value.
                    
                    // Run sample scenarios
                    WaitForNewline("Authenticated.", "Creating NEW accounts.");
                    CreateAccounts();
                    WaitForNewline("Accounts created.", "Preparing the source data file.");

                    // Transfer the source file from a public Azure Blob container to Data Lake Store.
                    CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://adltutorials.blob.core.windows.net/adls-sample-data/SearchLog.tsv"));
                    blob.DownloadToFile(localFolderPath + "SearchLog.tsv", FileMode.Create); // from WASB
                    UploadFile(localFolderPath + "SearchLog.tsv", "/Samples/Data/SearchLog.tsv"); // to ADLS
                    WaitForNewline("Source data file prepared.", "Submitting a job.");

                    // Submit the job
                    Guid jobId = SubmitJobByPath(localFolderPath + "SampleUSQLScript.txt", "My First ADLA Job");
                    WaitForNewline("Job submitted.", "Waiting for job completion.");

                    // Wait for job completion
                    WaitForJob(jobId);
                    WaitForNewline("Job completed.", "Downloading job output.");

                    // Download job output
                    DownloadFile("/Output/SearchLog-from-Data-Lake.csv", localFolderPath + "SearchLog-from-Data-Lake.csv");
                    WaitForNewline("Job output downloaded.", "Deleting accounts.");

                    // Delete accounts
                    DeleteAccounts();
                    WaitForNewline("Accounts deleted. You can now exit.");
                }

                // Helper function to show status and wait for user input
                public static void WaitForNewline(string reason, string nextAction = "")
                {
                    if (!String.IsNullOrWhiteSpace(nextAction))
                    {
                        Console.WriteLine(reason + "\r\nPress ENTER to continue...");
                        Console.ReadLine();
                        Console.WriteLine(nextAction);
                    }
                    else
                    {
                        Console.WriteLine(reason + "\r\nPress ENTER to continue...");
                        Console.ReadLine();
                    }
                }

                // Authenticate the user with AAD through an interactive popup.
                // You need to have an application registered with AAD in order to authenticate.
                //   For more information and instructions on how to register your application with AAD, see: 
                //   https://azure.microsoft.com/ru-RU/documentation/articles/resource-group-create-service-principal-portal/
                public static TokenCredentials AuthenticateUser(string tenantId, string resource, string appClientId, Uri appRedirectUri, string userId = "")
                {
                    var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);

                    var tokenAuthResult = authContext.AcquireToken(resource, appClientId, appRedirectUri,
                        PromptBehavior.Auto, UserIdentifier.AnyUser);

                    return new TokenCredentials(tokenAuthResult.AccessToken);
                }

                // Authenticate the application with AAD through the application's secret key.
                // You need to have an application registered with AAD in order to authenticate.
                //   For more information and instructions on how to register your application with AAD, see: 
                //   https://azure.microsoft.com/ru-RU/documentation/articles/resource-group-create-service-principal-portal/
                public static TokenCredentials AuthenticateApplication(string tenantId, string resource, string appClientId, Uri appRedirectUri, SecureString clientSecret)
                {
                    var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);
                    var credential = new ClientCredential(appClientId, clientSecret);

                    var tokenAuthResult = authContext.AcquireToken(resource, credential);

                    return new TokenCredentials(tokenAuthResult.AccessToken);
                }

                //Set up clients
                public static void SetupClients(TokenCredentials tokenCreds, string subscriptionId)
                {
                    _adlaClient = new DataLakeAnalyticsAccountManagementClient(tokenCreds);
                    _adlaClient.SubscriptionId = subscriptionId;

                    _adlaJobClient = new DataLakeAnalyticsJobManagementClient(tokenCreds);
                    _adlaJobClient.SubscriptionId = subscriptionId;

                    _adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(tokenCreds);
                    _adlaCatalogClient.SubscriptionId = subscriptionId;

                    _adlsClient = new DataLakeStoreAccountManagementClient(tokenCreds);
                    _adlsClient.SubscriptionId = subscriptionId;

                    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
                    _adlsFileSystemClient.SubscriptionId = subscriptionId;
                }

                // Create accounts
                public static void CreateAccounts()
                {
                    // Create ADLS account
                    var adlsParameters = new DataLakeStoreAccount(location: _location);
                    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

                    // Create ADLA account
                    var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(_adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
                    var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: _adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
                    var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: _location);
                    _adlaClient.Account.Create(_resourceGroupName, _adlaAccountName, adlaParameters);
                }

                // Delete accounts
                public static void DeleteAccounts()
                {
                    _adlaClient.Account.Delete(_resourceGroupName, _adlaAccountName);

                    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
                }

                // List all ADLA accounts within the subscription
                public static List<DataLakeAnalyticsAccount> ListAdlAnalyticsAccounts()
                {
                    var response = _adlaClient.Account.List(_adlaAccountName);
                    var accounts = new List<DataLakeAnalyticsAccount>(response);

                    while (response.NextPageLink != null)
                    {
                        response = _adlaClient.Account.ListNext(response.NextPageLink);
                        accounts.AddRange(response);
                    }

                    return accounts;
                }

                // List all ADLS accounts within the subscription
                public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
                {
                    var response = _adlsClient.Account.List(_adlsAccountName);
                    var accounts = new List<DataLakeStoreAccount>(response);
                    
                    while (response.NextPageLink != null)
                    {
                        response = _adlsClient.Account.ListNext(response.NextPageLink);
                        accounts.AddRange(response);
                    }

                    return accounts;
                }

                // Submit a U-SQL job by providing script contents.
                // Returns the job ID
                public static Guid SubmitJobByScript(string script, string jobName)
                {
                    var jobId = Guid.NewGuid();
                    var properties = new USqlJobProperties(script);
                    var parameters = new JobInformation(jobName, JobType.USql, properties);

                    var jobInfo = _adlaJobClient.Job.Create(jobId, parameters, _adlaAccountName);
                    
                    return jobId;
                }

                // Submit a U-SQL job by providing a path to the script
                public static Guid SubmitJobByPath(string scriptPath, string jobName)
                {
                    var script = File.ReadAllText(scriptPath);

                    var jobId = Guid.NewGuid();
                    var properties = new USqlJobProperties(script);
                    var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1000, degreeOfParallelism: 1);

                    var jobInfo = _adlaJobClient.Job.Create(jobId, parameters, _adlaAccountName);

                    return jobId;
                }

                public static JobResult WaitForJob(Guid jobId)
                {
                    var jobInfo = _adlaJobClient.Job.Get(jobId, _adlaAccountName);
                    while (jobInfo.State != JobState.Ended)
                    {
                        jobInfo = _adlaJobClient.Job.Get(jobId, _adlaAccountName);
                    }
                    return jobInfo.Result.Value;
                }

                // List jobs
                public static List<JobInformation> ListJobs()
                {
                    var response = _adlaJobClient.Job.List(_adlaAccountName);
                    var jobs = new List<JobInformation>(response);

                    while (response.NextPageLink != null)
                    {
                        response = _adlaJobClient.Job.ListNext(response.NextPageLink);
                        jobs.AddRange(response);
                    }

                    return jobs;
                }

                // Upload a file
                public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
                {
                    var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
                    var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
                    var uploader = new DataLakeStoreUploader(parameters, frontend);
                    uploader.Execute();
                }

                // Download file
                public static void DownloadFile(string srcPath, string destPath)
                {
                    var stream = _adlsFileSystemClient.FileSystem.Open(srcPath, _adlsAccountName);
                    var fileStream = new FileStream(destPath, FileMode.Create);

                    stream.CopyTo(fileStream);
                    fileStream.Close();
                    stream.Close();
                }
            }
        }

7. Нажмите клавишу **F5** для запуска приложения.

## См. также

- Для просмотра учебника с помощью других средств используйте вкладки-селекторы в верхней части страницы.
- Более сложный запрос можно посмотреть в статье [Анализ журналов веб-сайта с помощью аналитики озера данных Azure](data-lake-analytics-analyze-weblogs.md).
- Чтобы приступить к разработке приложений U-SQL, ознакомьтесь со статьей [Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Сведения о языке U-SQL см. в статье [Учебник. Приступая к работе с языком U-SQL для аналитики озера данных Azure](data-lake-analytics-u-sql-get-started.md) и в [справочнике по языку U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).
- Задачи управления описываются в статье [Управление аналитикой озера данных Azure с помощью портала Azure](data-lake-analytics-manage-use-portal.md).
- Общие сведения об аналитике озера данных см. в статье [Обзор аналитики озера данных Azure](data-lake-analytics-overview.md).

<!---HONumber=AcomDC_0323_2016-->