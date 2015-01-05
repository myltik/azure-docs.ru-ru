<properties urlDisplayName="Blob Service" pageTitle="Использование хранилища BLOB-объектов из .NET на Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Learn how to use Microsoft Azure Blob storage to upload,  download, list, and delete blob content. Samples are written in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Blob storage in .NET" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/10/2014" ms.author="tamram" />

# Использование хранилища BLOB-объектов из .NET

В этом руководстве показано, как реализовать типичные сценарии с использованием
службы хранения больших двоичных объектов Azure. Примеры написаны на языке C# и
в них используется библиотека клиента службы хранилища Azure для .NET. Здесь описаны такие сценарии, как
**передача**, **перечисление**, **закачка** и **удаление** больших двоичных объектов. Для
получения дополнительной информации о больших двоичных объектах см. раздел [Дальнейшие действия][].

> [WACOM.NOTE] В этом руководстве используется клиентская библиотека хранилища Azure для .NET 2.x и выше. Рекомендуется использовать клиентскую библиотеку хранилища версии 4.x, которую можно скачать с помощью [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) или в составе [пакета SDK для Azure для .NET](/ru-ru/downloads/). См. [Практическое руководство. Программный доступ к хранилищу больших двоичных объектов][] ниже для дополнительной информации о получении библиотеки клиента хранения.

##Оглавление

-   [Что такое хранилище больших двоичных объектов][]
-   [Основные понятия][]
-   [Создание учетной записи хранения Azure][]
-   [Настройка строки подключения к хранилищу][]
-   [Практическое руководство. Программный доступ к хранилищу больших двоичных объектов][]
-   [Практическое руководство. Создание контейнера][]
-   [Практическое руководство. Отправка большого двоичного объекта в контейнер][]
-   [Практическое руководство. Перечисление больших двоичных объектов в контейнере][]
-   [Практическое руководство. Закачка больших двоичных объектов][]
-   [Практическое руководство. Удаление больших двоичных объектов][]
-   [Практическое руководство. Асинхронное перечисление больших двоичных объектов в страницах][]
-   [Дальнейшие действия][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

##<a name="create-account"></a>Создание учетной записи хранения Azure
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

##<a name="setup-connection-string"></a>Настройка строки подключения к хранилищу

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a>Практическое руководство: Программный доступ к хранилищу BLOB-объектов

###Получение сборки
Мы рекомендуем использовать NuGet для получения сборки Microsoft.WindowsAzure.Storage.dll. Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Управление пакетами NuGet**.  Выполните в Интернете поиск "WindowsAzure.Storage" и нажмите кнопку **Установить**, чтобы установить пакет службы хранилища Azure и зависимые компоненты.

Библиотека Microsoft.WindowsAzure.Storage.dll также включена в пакет SDK для Azure для .NET, который можно скачать из <a href="http://www.windowsazure.com/ru-ru/develop/net/#">Центра разработчиков .NET</a>. Эта сборка устанавливается в каталог %Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<версия-sdk>\ref\.

###Объявления пространств имен
Добавьте следующие объявления пространств имен в начало любого файла на языке C#,
из которого вы хотите программно обращаться к службе хранилища Azure:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

Убедитесь, что указана ссылка на сборку "Microsoft.WindowsAzure.Storage.dll".

###Получение строки подключения
С помощью типа **CloudStorageAccount** можно представить 
информацию об учетной записи хранения. Если вы используете 
шаблон проекта Azure и/или ссылаетесь на 
Microsoft.WindowsAzure.CloudConfigurationManager, 
с помощью типа **CloudConfigurationManager** можно
извлечь строку подключения к хранилищу и информацию об учетной записи хранения
из конфигурации службы Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Если вы создаете приложение без ссылки на пространство имен Microsoft.WindowsAzure.CloudConfigurationManager и строка подключения находится в файле web.config или app.config, как показано выше, то для получения строки подключения можно использовать **ConfigurationManager**.  Необходимо добавить ссылку на файл System.Configuration.dll в проект и добавить для него другое объявление пространства имен:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

Тип **CloudBlobClient** позволяет извлекать объекты, представляющие
контейнеры и большие двоичные объекты, хранящиеся в службе хранилища больших двоичных объектов. Пример:
код создает объект **CloudBlobClient** с помощью объекта учетной записи хранения,
полученной выше:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

###Зависимости ODataLib
Зависимости ODataLib в клиентской библиотеке хранения для .NET разрешаются через пакеты ODataLib (версия 5.0.2), доступные через NuGet, а не через службы данных WCF.  Библиотеки ODataLib можно загрузить напрямую или указать на них ссылку в проекте через NuGet.  Мы используем пакеты [OData], [Edm] и [Spatial].

## <a name="create-container"> </a>Практическое руководство: Создание контейнера

Каждый BLOB-объект в Azure должен располагаться в контейнере. В этом примере показано, как создать контейнер:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

По умолчанию новый контейнер закрытый, и вам необходимо указать ключ доступа к хранилищу, чтобы загрузить BLOB-объекты из этого контейнера. Чтобы сделать файлы в этом контейнере доступными для всех пользователей, сделайте контейнер открытым, используя следующий код:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
 	    BlobContainerPublicAccessType.Blob }); 

Любой пользователь в Интернете может видеть большие двоичные объекты в открытом контейнере, но изменить или удалить их можно только при наличии ключа доступа.

## <a name="upload-blob"> </a>Практическое руководство: Отправка BLOB-объекта в контейнер

Хранилище BLOB-объектов Azure поддерживает блочные и страничные BLOB-объекты.  В большинстве случаев рекомендуется использовать блочные BLOB-объекты.

Для передачи файла в блочный BLOB-объект получите ссылку на контейнер и используйте ее для получения ссылки на блочный BLOB-объект. Получив ссылку, вы можете отправить в него любой поток данных с помощью метода **UploadFromStream**. Эта операция создает большой двоичный объект, если он не существует, или заменяет его, если он существует. В следующем примере показано, как отправить BLOB-объект в контейнер. Предполагается, что контейнер уже был создан.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    } 

##<a name="list-blob"> </a>Практическое руководство: Перечисление BLOB-объектов в контейнере

Для перечисления BLOB-объектов в контейнере сначала необходимо получить ссылку на контейнер. Затем можно использовать метод **ListBlobs** контейнера, чтобы извлечь большие двоичные объекты и/или каталоги в нем. Для доступа к широкому набору свойств и методов возвращаемого объекта **IListBlobItem** необходимо преобразовать его в объект **CloudBlockBlob**, **CloudPageBlob** или **CloudBlobDirectory**.  Если тип неизвестен, можно использовать проверку типов, чтобы определить нужный тип.  Следующий код демонстрирует, как получить и вывести универсальный код ресурса каждого элемента 
в контейнере "photos":

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client. 
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve reference to a previously created container.
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

	// Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, false))
	{
		if (item.GetType() == typeof(CloudBlockBlob))
		{
			CloudBlockBlob blob = (CloudBlockBlob)item;

			Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
                                        
		}
		else if (item.GetType() == typeof(CloudPageBlob))
		{
			CloudPageBlob pageBlob = (CloudPageBlob)item;

			Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

		}
		else if (item.GetType() == typeof(CloudBlobDirectory))
		{
			CloudBlobDirectory directory = (CloudBlobDirectory)item;
			
			Console.WriteLine("Directory: {0}", directory.Uri);
		}
	}

Как показано выше, служба BLOB-объектов также использует концепцию каталогов внутри контейнеров. Таким образом, можно организовать BLOB-объекты в структуре, похожей на папки. Например, рассмотрим следующий набор блочных BLOB-объектов в контейнере с именем "photos":

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

При вызове метода **ListBlobs** для контейнера "photos" возвращаемая коллекция будет содержать объекты **CloudBlobDirectory** и **CloudBlockBlob**, 
представляющие каталоги и большие двоичные объекты, содержащиеся на верхнем уровне. Здесь результатом будет:

	Каталог: https://<accountname>.blob.core.windows.net/photos/2010/
	Каталог: https://<accountname>.blob.core.windows.net/photos/2011/
	Блок большого двоичного объекта размером 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


При необходимости можно установить для параметра **UseFlatBlobListing** метода **ListBlobs** значение **true**. Это приведет к возвращению каждого большого двоичного объекта в виде **CloudBlockBlob** независимо от каталога.  Здесь вызывается метод **ListBlobs**:

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

а здесь результатом будет:

	Блок большого двоичного объекта размером 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Блок большого двоичного объекта размером 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Блок большого двоичного объекта размером 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Блок большого двоичного объекта размером 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Блок большого двоичного объекта размером 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Блок большого двоичного объекта размером 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Блок большого двоичного объекта размером 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Блок большого двоичного объекта размером 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Дополнительную информацию см. в описании метода [CloudBlobContainer.ListBlobs][].

## <a name="download-blobs"> </a>Практическое руководство: Загрузка BLOB-объектов

Для закачки больших двоичных объектов сначала нужно получить ссылку на большой двоичный объект, а затем вызвать метод **DownloadToStream**. В следующем
примере метод **DownloadToStream** используется для переноса содержимого большого двоичного объекта
в объект потока, который затем можно сохранить в локальном файле.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    } 

Можно также использовать метод **DownloadToStream**, чтобы закачать содержимое большого двоичного объекта как текстовую строку.

	// Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// Retrieve reference to a blob named "myblob.txt"
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

##<a name="delete-blobs"> </a>Практическое руководство: Удаление blob-объектов

Для удаления BLOB-объекта сначала нужно получить ссылку на BLOB-объект, а затем вызвать метод
**Delete**.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete(); 


##<a name="list-blobs-async"> </a>Практическое руководство: Асинхронное расположение BLOB-объектов на странице.

Если вам нужно расположить большое количество BLOB-объектов или вы хотите управлять отображением количества объектов в результате запроса, вы можете задать расположение BLOB-объектов на странице. В этом примере вы узнаете, как расположить запрошенные результаты на странице асинхронно для того, чтобы не блокировать выполнение задачи ожиданием большого объема возвращаемых данных.

В этом примере вы узнаете о плоском размещении результатов, а также о том, как задать иерархическое размещение результатов, установив для параметра useFlatBlobListing метода **ListBlobsSegmentedAsync** значение false.

Поскольку в примере вызывается метод асинхронного расположения результатов, вам необходимо задать ключевое слово async перед ним. После этого метод вернет объект **Task**. При ожидании ключевого слова для **ListBlobsSegmentedAsync** метод приостанавливает выполнение примера до тех пор, пока задача размещения результатов не завершена.

    async public static Task ListBlobsSegmentedInFlatListing()
    {
        // Retrieve storage account from connection string.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            CloudConfigurationManager.GetSetting("StorageConnectionString"));

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.GetContainerReference("myblobs");

        //List blobs in pages.
        Console.WriteLine("List blobs in pages:");

        //List blobs with a paging size of 10, for the purposes of the example. 
		//The first call does not include the continuation token.
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(
                "", true, BlobListingDetails.All, 10, null, null, null);

        //Enumerate the result segment returned.
        int i = 0;
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token, if there are additional pages of results.
        BlobContinuationToken continuationToken = resultSegment.ContinuationToken;

        //Check whether there are more results and list them in pages of 10 while a continuation token is returned.
        while (continuationToken != null)
        {
            //This overload allows control of the page size. 
			//You can return all remaining results by passing null for the maxResults parameter, 
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync(
					"", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the next continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
    }

## <a name="next-steps"></a>Дальнейшие действия

Вы изучили основные сведения о хранилище BLOB-объектов. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.
<ul>
<li>Дополнительную информацию о доступных API-интерфейсах см. в справочной документации по службе BLOB-объектов:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Справочник по клиентской библиотеке хранения для .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179355">Справочник по REST API</a></li>
  </ul>
</li>
<li>Дополнительную информацию о более сложных задачах, которые можно выполнить со службой хранилища Azure, см. в статье <a href="http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx">Хранение данных и доступ к ним в Azure</a>.</li>
<li>Дополнительную информацию о работе со службой хранилища Azure в серверных процессах для веб-сайтов Azure см. в статье <a href="/ru-ru/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Начало работы с пакетом SDK веб-заданий Azure</a>.</li>
<li>Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.
  <ul>
    <li>Использовать <a href="/ru-ru/documentation/articles/storage-dotnet-how-to-use-tables/">Хранилище таблиц</a> для хранения структурированных данных.</li>
    <li>Использовать <a href="/ru-ru/documentation/articles/storage-dotnet-how-to-use-queues/">Хранилище очередей</a> для хранения неструктурированных данных.</li>
    <li>Использовать <a href="/ru-ru/documentation/articles/sql-database-dotnet-how-to-use/">База данных SQL</a> для хранения реляционных данных.</li>
  </ul>
</li>
</ul>

  [Дальнейшие действия]: #next-steps
  [Что такое хранилище больших двоичных объектов]: #what-is
  [Основные понятия]: #concepts
  [Создание учетной записи хранения Azure]: #create-account
  [Настройка строки подключения к хранилищу]: #setup-connection-string
  [Практическое руководство. Программный доступ к хранилищу больших двоичных объектов]: #configure-access
  [Практическое руководство. Создание контейнера]: #create-container
  [Практическое руководство. Отправка большого двоичного объекта в контейнер]: #upload-blob
  [Практическое руководство. Перечисление больших двоичных объектов в контейнере]: #list-blob
  [Практическое руководство. Скачивание больших двоичных объектов]: #download-blobs
  [Практическое руководство. Удаление больших двоичных объектов]: #delete-blobs
  [Практическое руководство. Асинхронное перечисление больших двоичных объектов в страницах]: #list-blobs-async
  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png
  
  [Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
  [Блог команды разработчиков службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Настройка строк подключения]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee758697.aspx
  [Справочник по клиентской библиотеке .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Справочник по REST API]: http://msdn.microsoft.com/ru-ru/library/windowsazure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Пространственный]: http://nuget.org/packages/System.Spatial/5.0.2

<!--HONumber=35.1-->
