<properties 
   pageTitle="Использование пакета SDK .NET хранилища озера данных для разработки приложений | Azure" 
   description="Использование пакета SDK .NET хранилища озера данных для разработки приложений" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/04/2016"
   ms.author="nitinme"/>

# Начало работы с хранилищем озера данных Azure с помощью пакета SDK .NET

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Using Azure CLI](data-lake-store-get-started-cli.md)
- [Using Node.js](data-lake-store-manage-use-nodejs.md)

Узнайте, как с помощью пакета SDK .NET хранилища озера данных Azure создать учетную запись озера данных Azure и выполнить базовые операции, такие как создание папок, передача и загрузка файлов данных, удаление учетной записи и т. д. Дополнительные сведения об озере данных см. в разделе [Хранилище озера данных Azure](data-lake-store-overview.md).

## Предварительные требования

* Visual Studio 2013 или 2015 Для выполнения инструкций ниже использовалась Visual Studio 2015.
* **Подписка Azure.**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Включите свою подписку Azure** для общедоступной предварительной версии хранилища озера данных. См. [инструкции](data-lake-store-get-started-portal.md#signup).

## Создание приложения .NET

1. Откройте Visual Studio и создайте консольное приложение.

2. В меню **Файл** выберите команду **Создать**, а затем — **Проект**.

3. В окне **Новый проект** введите или выберите следующие значения.

	| Свойство | Значение |
	|----------|-----------------------------|
	| Категория | Templates/Visual C#/Windows |
	| Шаблон | Консольное приложение |
	| Имя | CreateADLApplication |

4. Нажмите кнопку **ОК**, чтобы создать проект.

5. Добавьте пакеты Nuget в проект.

	1. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**.
	2. На вкладке **Диспетчер пакетов NuGet** в поле **Источник пакета** задайте значение **nuget.org** и установите флажок **Включить предварительный выпуск**.
	3. Найдите и установите следующие пакеты хранилища озера данных:
	
		* Microsoft.Azure.Management.DataLake.Store
		* Microsoft.Azure.Management.DataLake.StoreUploader
        * Microsoft.IdentityModel.Clients.ActiveDirectory

		![Добавление источника Nuget](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Создание новой учетной записи озера данных Azure")

	4. Закройте **Диспетчер пакетов Nuget**.

7. Откройте файл **Program.cs** и замените существующий блок кода следующим. Кроме того, укажите значения параметров в фрагменте кода, например параметров subscriptionId, dataLakeAccountName и localPath.

	Этот код используется в процессе создания учетной записи хранилища озера данных, создания папок в хранилище, передачи и скачивания файлов и, наконец, удаления учетной записи. Если у вас нет под рукой подходящих для этих целей данных, передайте папку **Ambulance Data** из [репозитория Git для озера данных Azure](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).
	
        using System;
        using System.IO;
        using System.Security;
        using System.Text;
        using System.Collections.Generic;
        using System.Linq;

        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name for a NEW Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value. This resource group should already exist.
                    _location = "East US 2";
                    
                    string localFolderPath = @"C:\local_path"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = @"C:\local_path\file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";
                    
                    // Authenticate the user
                    // For more information about applications and instructions on how to get a client ID, see: 
                    //   https://azure.microsoft.com/ru-RU/documentation/articles/resource-group-create-service-principal-portal/
                    var tokenCreds = AuthenticateUser("common", "https://management.core.windows.net/",
                        "<APPLICATION-CLIENT-ID>", new Uri("https://<APPLICATION-REDIRECT-URI>")); // TODO: Replace bracketed values.
                    
                    SetupClients(tokenCreds, "<SUBSCRIPTION-ID>"); // TODO: Replace bracketed value.

                    // Run sample scenarios
                    WaitForNewline("Authenticated.", "Creating NEW account.");
                    CreateAccount();
                    WaitForNewline("Account created.", "Creating a directory.");

                    // Create a directory in the Data Lake Store
                    CreateDirectory(remoteFolderPath);
                    WaitForNewline("Directory created.", "Showing directory info.");

                    // Get info about the directory in the Data Lake Store
                    var itemInfo = GetItemInfo(remoteFolderPath);
                    Console.WriteLine("Type: " + itemInfo.Type);
                    Console.WriteLine("Last modified (UTC): " +
                                      new DateTime(1970, 1, 1, 0, 0, 0, 0, DateTimeKind.Utc).AddMilliseconds(
                                          itemInfo.ModificationTime.Value));
                    WaitForNewline("Directory info shown.", "Uploading a file.");

                    // Upload a file to the Data Lake Store
                    UploadFile(localFilePath, remoteFilePath);
                    WaitForNewline("File uploaded.", "Listing files and directories.");

                    // List the files in the Data Lake Store
                    var itemList = ListItems(remoteFolderPath);
                    var fileMenuItems = itemList.Select(a => String.Format("{0,15} {1}", a.Type, a.PathSuffix));
                    Console.WriteLine(String.Join("\r\n", fileMenuItems));
                    WaitForNewline("Files and directories listed.", "Appending content to a file.");

                    // Append to a file in the Data Lake Store
                    AppendToFile(remoteFilePath, "123");
                    WaitForNewline("Content appended.", "Downloading a file.");

                    // Download a file from the Data Lake Store
                    DownloadFile(remoteFilePath, localFilePath);
                    WaitForNewline("File downloaded.", "Deleting account.");

                    // Delete account
                    DeleteAccount();
                    WaitForNewline("Account deleted. You can now exit.");
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
                    _adlsClient = new DataLakeStoreAccountManagementClient(tokenCreds);
                    _adlsClient.SubscriptionId = subscriptionId;

                    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
                    _adlsFileSystemClient.SubscriptionId = subscriptionId;
                }

                // Create account
                public static void CreateAccount()
                {
                    // Create ADLS account
                    var adlsParameters = new DataLakeStoreAccount(location: _location);
                    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
                }

                // Delete account
                public static void DeleteAccount()
                {
                    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
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

                // Upload a file
                public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
                {
                    var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
                    var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
                    var uploader = new DataLakeStoreUploader(parameters, frontend);
                    uploader.Execute();
                }

                // Concatenate files
                public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
                {
                    _adlsFileSystemClient.FileSystem.Concat(destFilePath, srcFilePaths, _adlsAccountName);
                }

                // Get file or directory info
                public static FileStatusProperties GetItemInfo(string path)
                {
                    return _adlsFileSystemClient.FileSystem.GetFileStatus(path, _adlsAccountName).FileStatus;
                }

                // List files and directories
                public static List<FileStatusProperties> ListItems(string directoryPath)
                {
                    return _adlsFileSystemClient.FileSystem.ListFileStatus(directoryPath, _adlsAccountName).FileStatuses.FileStatus.ToList();
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

                // Append to file
                public static void AppendToFile(string path, string content)
                {
                    var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));

                    _adlsFileSystemClient.FileSystem.Append(path, stream, _adlsAccountName);
                }

                // Create a directory
                public static void CreateDirectory(string path)
                {
                    _adlsFileSystemClient.FileSystem.Mkdirs(path, _adlsAccountName);
                }
            }
        }


8. Создайте и запустите приложение. Следуйте инструкциям на экране для запуска и завершения приложения.

## Другие способы создания учетной записи хранения озера данных

- [Начало работы с хранилищем озера данных с помощью портала](data-lake-store-get-started-portal.md)
- [Начало работы с хранилищем озера данных с помощью PowerShell](data-lake-store-get-started-powershell.md)
- [Начало работы с хранилищем озера данных с помощью Azure CLI](data-lake-store-get-started-cli.md)


## Дальнейшие действия

- [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)
- [Использование аналитики озера данных Azure с хранилищем озера данных](data-lake-analytics-get-started-portal.md)
- [Использование Azure HDInsight с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0224_2016-->