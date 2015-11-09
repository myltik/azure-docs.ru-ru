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
   ms.date="10/28/2015"
   ms.author="nitinme"/>

# Начало работы с хранилищем озера данных Azure с помощью пакета SDK .NET

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)

Узнайте, как с помощью пакета SDK .NET хранилища озера данных Azure создать учетную запись озера данных Azure и выполнить базовые операции, такие как создание папок, передача и загрузка файлов данных, удаление учетной записи и т. д. Дополнительные сведения об озере данных см. в разделе [Хранилище озера данных Azure](data-lake-store-overview.md).

## Предварительные требования

* Visual Studio 2013 или 2015 Для выполнения инструкций ниже использовалась Visual Studio 2015.
* **Подписка Azure.**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/ru-RU/pricing/free-trial/).
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

5. Добавьте пакет Nuget в проект.

	1. В обозревателе решений щелкните правой кнопкой мыши имя проекта и выберите пункт **Управление пакетами NuGet**.
	2. В диалоговом окне **Диспетчер пакетов Nuget** убедитесь, что в поле **Источник пакета** задано значение **nuget.org** и что флажок **Включить предварительный выпуск** установлен.
	3. Найдите и установите следующие пакеты:
	
		* Microsoft.Azure.Common.Authentication
		* Microsoft.Azure.Management.DataLake.Store
		* Microsoft.Azure.Management.DataLake.StoreFileSystem
		* Microsoft.Azure.Management.DataLake.StoreUploader

		![Добавление источника Nuget](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Создание новой учетной записи озера данных Azure")

	4. Закройте **Диспетчер пакетов Nuget**.

7. Откройте файл **Program.cs** и замените существующий блок кода следующим. Кроме того, укажите значения для параметров в фрагменте кода.

	Этот код используется в процессе создания хранилища озера данных, создания папок в хранилище, отправки и загрузки файлов и, наконец, удаления учетной записи. Если у вас нет подходящих для этих целей данных, загрузите папку **Ambulance Data** из [репозитория Git для озера данных Azure](https://github.com/MicrosoftBigData/ProjectKona/tree/master/SQLIPSamples/SampleData/AmbulanceData).
	
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Security;
		using System.IO;
		
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.DataLake.Store;
		using Microsoft.Azure.Management.DataLake.Store.Models;
		using Microsoft.Azure.Management.DataLake.StoreFileSystem;
		using Microsoft.Azure.Management.DataLake.StoreFileSystem.Models;
		using Microsoft.Azure.Management.DataLake.StoreUploader;
		using Microsoft.Azure.Common.Authentication.Factories;
		
		
		namespace CreateADLApplication
		{
		    class CreateADLApplication
		    {
		        private static DataLakeStoreManagementClient _dataLakeStoreClient;
		        private static DataLakeStoreFileSystemManagementClient _dataLakeStoreFileSystemClient;
		        private const string ResourceGroupName = "<resource_grp_name>"; //THIS SHOULD ALREADY EXIST
		
		        static void Main(string[] args)
		        {
		            var subscriptionId = new Guid("<subscription_ID>");
		            var _credentials = GetAccessToken();
		            string dataLakeAccountName = "<data_lake_store_name>";
		            string location = "East US 2";
		
		            _credentials = GetCloudCredentials(_credentials, subscriptionId);
		            _dataLakeStoreClient = new DataLakeStoreManagementClient(_credentials);
		            _dataLakeStoreFileSystemClient = new DataLakeStoreFileSystemManagementClient(_credentials);
		
		            var parameters = new DataLakeStoreAccountCreateOrUpdateParameters();
		            parameters.DataLakeStoreAccount = new DataLakeStoreAccount
		            {
		                Name = dataLakeAccountName,
		                Location = location
		            };
		
		            // Create a Data Lake Store account
		            Console.WriteLine("Creating an Azure Data Lake Store account ...");
		            _dataLakeStoreClient.DataLakeStoreAccount.Create(ResourceGroupName, parameters);
		
		            Console.WriteLine("Account created. Press ENTER to continue...");
		            Console.ReadLine();
		
		            // Create a directory called MYTEMPDIR in the store
		            Console.WriteLine("Creating a directory under the Azure Data Lake Store account");
		            CreateDir(_dataLakeStoreFileSystemClient, "/mytempdir", dataLakeAccountName, "777");
		            Console.WriteLine("Directory created. Press ENTER to continue...");
		            Console.ReadLine();
		
		            // Upload a file under MYTEMPDIR
		            Console.WriteLine("Uploading a file to the Azure Data Lake Store account");
		            bool force = false; //Set this to true if you want to overwrite existing data
		            UploadFile(_dataLakeStoreFileSystemClient, dataLakeAccountName, "C:\\users\\nitinme\\desktop\\vehicle1_09142014.csv", "/mytempdir/vehicle1_09142014.csv", force);
		            Console.WriteLine("File uploaded. Press ENTER to continue...");
		            Console.ReadLine();
		
		            // List the files in the Data Lake Store
		            Console.WriteLine("Listing all files in the Azure Data Lake Store account");
		            var fileList = ListItems(_dataLakeStoreFileSystemClient, dataLakeAccountName, "/mytempdir");
		            var fileMenuItems = fileList.Select(a => String.Format("{0,15} {1}", a.Type, a.PathSuffix)).ToList();
		            foreach (var filename in fileMenuItems)
		            {
		                Console.WriteLine(filename);
		
		            }
		            Console.WriteLine("Files listed. Press ENTER to continue...");
		            Console.ReadLine();
		
		            // Download the files from the Data Lake Store
		            Console.WriteLine("Downloading files from an Azure Data Lake Store account");
		            DownloadFile(_dataLakeStoreFileSystemClient, dataLakeAccountName, "/mytempdir/vehicle1_09142014.csv", "C:\\users\\nitinme\\desktop\\vehicle1_09142014_copy.csv", force);
		            Console.WriteLine("Files downloaded. Press ENTER to continue...");
		            Console.ReadLine();
		
		            // Delete the Data Lake Store account
		            Console.WriteLine("Azure Data Lake Store account will be deleted. Press ENTER to continue...");
		            _dataLakeStoreClient.DataLakeStoreAccount.Delete(ResourceGroupName, dataLakeAccountName);
		            Console.ReadLine();
		            Console.WriteLine("Account deleted. Press ENTER to exit...");
		            Console.ReadLine();
		        }
		
		        public static SubscriptionCloudCredentials GetAccessToken(string username = null, SecureString password = null)
		        {
		            var authFactory = new AuthenticationFactory();
		
		            var account = new AzureAccount { Type = AzureAccount.AccountType.User };
		
		            if (username != null && password != null)
		                account.Id = username;
		
		            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
		            return new TokenCloudCredentials(authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken);
		        }
		
		        public static SubscriptionCloudCredentials GetCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
		        {
		            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
		        }
		
		        public static bool CreateDir(DataLakeStoreFileSystemManagementClient dataLakeStoreFileSystemClient, string path, string dlAccountName, string permission)
		        {
		            dataLakeStoreFileSystemClient.FileSystem.Mkdirs(path, dlAccountName, permission);
		            return true;
		        }
		
		        public static bool UploadFile(DataLakeStoreFileSystemManagementClient dataLakeStoreFileSystemClient, string dlAccountName, string srcPath, string destPath, bool force = false)
		        {
		            var parameters = new UploadParameters(srcPath, destPath, dlAccountName, isOverwrite: true);
		            var frontend = new DataLakeStoreFrontEndAdapter(dlAccountName, dataLakeStoreFileSystemClient);
		            var uploader = new DataLakeStoreUploader(parameters, frontend);
		            uploader.Execute();
		            return true;
		        }
		
		        public static void DownloadFile(DataLakeStoreFileSystemManagementClient dataLakeFileSystemClient,
		        string dataLakeAccountName, string srcPath, string destPath, bool force)
		        {
		            var beginOpenResponse = dataLakeFileSystemClient.FileSystem.BeginOpen(srcPath, dataLakeAccountName,
		                new FileOpenParameters());
		            var openResponse = dataLakeFileSystemClient.FileSystem.Open(beginOpenResponse.Location);
		            if (force || !File.Exists(destPath))
		                File.WriteAllBytes(destPath, openResponse.FileContents);
		        }
		
		        public static List<FileStatusProperties> ListItems(DataLakeStoreFileSystemManagementClient dataLakeFileSystemClient, string dataLakeAccountName, string path)
		        {
		            var response = dataLakeFileSystemClient.FileSystem.ListFileStatus(path, dataLakeAccountName, new DataLakeStoreFileSystemListParameters());
		            return response.FileStatuses.FileStatus.ToList();
		        }
		    }
		}


8. Создайте и запустите приложение. Следуйте инструкциям на экране для запуска и завершения приложения.

## Другие способы создания учетной записи хранения озера данных

- [Начало работы с хранилищем озера данных с помощью портала](data-lake-store-get-started-portal.md)
- [Начало работы с хранилищем озера данных с помощью PowerShell](data-lake-store-get-started-powershell.md) 


## Дальнейшие действия

- [Защита данных в хранилище озера данных](data-lake-store-secure-data.md)
- [Использование аналитики озера данных Azure с хранилищем озера данных](data-lake-analytics-get-started-portal.md)
- [Использование Azure HDInsight с хранилищем озера данных](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=Nov15_HO1-->