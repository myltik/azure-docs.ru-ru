<properties 
	pageTitle="Использование хранилища BLOB-объектов из .NET в Microsoft Azure" 
	description="Узнайте, как использовать хранилище больших двоичных объектов (BLOB) Microsoft Azure для отправки, загрузки, вывода списка и удаления содержимого больших двоичных объектов. Примеры написаны на C#." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/12/2015" 
	ms.author="tamram"/>


# Использование хранилища BLOB-объектов из .NET

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Обзор

В этом руководстве показано, как реализовать типичные сценарии с использованием
службы хранения больших двоичных объектов Azure. Примеры написаны на C# и используют клиентскую библиотеку хранилища Azure для .NET. Здесь описаны такие сценарии, как
**отправка**, **перечисление**, **загрузка** и **удаление** BLOB-объектов.

> [AZURE.NOTE] В этом руководстве используется клиентская библиотека хранилища Azure для .NET 2.x и выше. Рекомендуется использовать клиентскую библиотеку хранилища 4.x, которая доступна через [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) или как часть [пакета SDK Azure для .NET](/downloads/). См. раздел [Программный доступ к хранилищу BLOB-объектов](#programmatically-access-blob-storage) ниже, в котором приводятся дополнительные сведения о получении клиентской библиотеки хранилища.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../../includes/storage-configure-connection-string-include.md)]

## Программный доступ к хранилищу BLOB-объектов

### Получение сборки
Мы рекомендуем использовать NuGet для получения сборки `Microsoft.WindowsAzure.Storage.dll`. Щелкните правой кнопкой мыши проект в **Обозревателе решений** и выберите **Управление пакетами NuGet**.  Выполните в Интернете поиск WindowsAzure.Storage и нажмите кнопку **Установить**, чтобы установить пакет хранилища Azure и зависимые компоненты.

Библиотека Microsoft.WindowsAzure.Storage.dll также включена в состав пакета SDK Azure для .NET, который можно скачать в <a href="http://azure.microsoft.com/develop/net/#">Центре разработчиков .NET</a>. Сборка устанавливается в каталог  `%Program Files%\Microsoft SDKs\Azure.NET SDK<sdk-version>\ref`.

### Объявления пространств имен
Добавьте следующие объявления пространств имен кода в начало любого файла C#,в котором вы собираетесь получать доступ к хранилищу Azure программным способом:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

Убедитесь, что указана ссылка на сборку  `Microsoft.WindowsAzure.Storage.dll`.

### Получение строки подключения
Для представлений сведений о вашей учетной записи хранения можно использовать тип **CloudStorageAccount**. Если вы используете 
шаблон проекта Azure и/или ссылаетесь на 
Microsoft.WindowsAzure.CloudConfigurationManager, вы можете использовать тип **CloudConfigurationManager**, чтобы получить строку подключения к хранилищу и сведения учетной записи хранения из конфигурации службы Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Если вы создаете приложение без ссылки на пространство имен Microsoft.WindowsAzure.CloudConfigurationManager и строка подключения находится в файле `web.config` или `app.config`, как показано выше, то для получения строки подключения можно использовать **ConfigurationManager**.  Необходимо добавить ссылку на файл System.Configuration.dll в проект и добавить для него другое объявление пространства имен:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

Тип **CloudBlobClient** позволяет извлекать объекты, представляющие контейнеры и большие двоичные объекты, которые хранятся в службе хранилища BLOB-объектов. Следующий код создает объект **CloudBlobClient** с помощью объекта учетной записи хранения, полученной выше:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

### Зависимости ODataLib
Зависимости ODataLib в клиентской библиотеке хранения для .NET разрешаются через пакеты ODataLib (версия 5.0.2), доступные через NuGet, а не через службы данных WCF.  Библиотеки ODataLib можно загрузить напрямую или указать на них ссылку в проекте через NuGet.  Мы используем пакеты ODataLib [OData], [Edm] и [Spatial].

## Создание контейнера

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

## Отправка BLOB-объекта в контейнер

Хранилище BLOB-объектов Azure поддерживает блочные и страничные BLOB-объекты.  В большинстве случаев рекомендуется использовать блочные BLOB-объекты.

Для передачи файла в блочный BLOB-объект получите ссылку на контейнер и используйте ее для получения ссылки на блочный BLOB-объект. Получив ссылку на большой двоичный объект, вы можете отправить в него любой поток данных с помощью вызова метода **UploadFromStream()**. Эта операция создает большой двоичный объект, если он не существует, или заменяет его, если он существует. В следующем примере показано, как отправить BLOB-объект в контейнер. Предполагается, что контейнер уже был создан.

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

## Перечисление BLOB-объектов в контейнере

Для перечисления BLOB-объектов в контейнере сначала необходимо получить ссылку на контейнер. Затем можно использовать метод **ListBlobs** контейнера, чтобы извлечь большие двоичные объекты и/или каталоги в нем. Для доступа к широкому набору свойств и методов возвращаемого объекта **IListBlobItem** необходимо преобразовать его в объект **CloudBlockBlob**, 
**CloudPageBlob** или **CloudBlobDirectory**.  Если тип неизвестен, можно использовать проверку типов, чтобы определить нужный тип.  Следующий код демонстрирует, как получить и вывести URI каждого элемента в контейнере `photos`:

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

Как показано выше, служба BLOB-объектов также использует концепцию каталогов внутри контейнеров. Таким образом, можно организовать BLOB-объекты в структуре, похожей на папки. Например, рассмотрим следующий набор блочных BLOB-объектов в контейнере с именем `photos`:

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

При вызове метода **ListBlobs** в контейнере 'photos' (как в примере выше) возвращаемая коллекция будет содержать объекты **CloudBlobDirectory** и **CloudBlockBlob**, представляющие собой каталоги и BLOB-объекты верхнего уровня. Здесь результатом будет:

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


При необходимости можно задать для параметра **UseFlatBlobListing** метода **ListBlobs** значение 
**true**.. Это приведет к возвращению каждого BLOB-объекта в виде **CloudBlockBlob**
независимо от каталога.  Здесь вызывается метод **ListBlobs**:

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}
 а здесь результатом будет:

	Блок большого двоичного объекта размером 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Дополнительную информацию см. в разделе [CloudBlobContainer.ListBlobs][].

## Загрузка BLOB-объектов

Для загрузки BLOB-объектов сначала нужно получить ссылку на BLOB-объект, а затем вызвать метод **DownloadToStream** . В следующем
примере метод **DownloadToStream** используется для передачи содержимого BLOB-объекта
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

Можно также использовать метод **DownloadToStream**, чтобы загрузить содержимое BLOB-объекта как текстовую строку.

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

## Удаление blob-объектов

Для удаления BLOB-объекта сначала нужно получить ссылку на BLOB-объект, а затем вызвать метод
**Delete** для него.

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


## Асинхронное перечисление BLOB-объектов в страницах

Если вам нужно расположить большое количество BLOB-объектов или вы хотите управлять отображением количества объектов в результате запроса, вы можете задать расположение BLOB-объектов на странице. В этом примере вы узнаете, как расположить запрошенные результаты на странице асинхронно для того, чтобы не блокировать выполнение задачи ожиданием большого объема возвращаемых данных.

В этом примере вы узнаете о выводе плоского списка больших двоичных объектов, а также о том, как вывести иерархический список, задав для параметра  `useFlatBlobListing` метода **ListBlobsSegmentedAsync** значение  `false`.

Так как в примере вызывается асинхронный метод, нужно добавить ключевое слово  `async` перед ним. Этот метод должен вернуть объект **Task**. При ожидании ключевого слова для **ListBlobsSegmentedAsync** метод приостанавливает выполнение примера до тех пор, пока задача вывода списка не завершена.

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

## Дальнейшие действия

Вы изучили основные сведения о хранилище BLOB-объектов. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.
<ul>
<li>Дополнительную информацию о доступных API-интерфейсах см. в справочной документации по службе BLOB-объектов:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Справочник по клиентской библиотеке хранилища для .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/library/azure/dd179355">Справочник по REST API</a></li>
  </ul>
</li>
<li>Дополнительную информацию о более сложных задачах, которые можно выполнить с помощью службы хранилища Azure, см. в разделе <a href="http://msdn.microsoft.com/library/azure/gg433040.aspx">Хранение и доступ к данным в Azure</a>.</li>
<li>Узнайте, как упростить код, предназначенный для работы со службой хранилища Azure с помощью <a href="../websites-dotnet-webjobs-sdk/">пакета SDK для Azure WebJobs.</li>
<li>Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.
  <ul>
    <li>Использование <a href="/documentation/articles/storage-dotnet-how-to-use-tables/">табличного хранилища</a> для хранения структурированных данных.</li>
    <li>Использование <a href="/documentation/articles/storage-dotnet-how-to-use-queues/">хранилища очередей</a> для хранения неструктурированных данных.</li>
    <li>Использование <a href="/documentation/articles/sql-database-dotnet-how-to-use/">Базы данных SQL</a> для хранения реляционных данных.</li>
  </ul>
</li>
</ul>

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png
  
  [Хранение и доступ к данным в Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Блог рабочей группы службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Настройка строк подключения]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [Справочник по клиентской библиотеке .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Справочник по REST API]: http://msdn.microsoft.com/library/azure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

<!--HONumber=49--> 

<!--HONumber=49--> 