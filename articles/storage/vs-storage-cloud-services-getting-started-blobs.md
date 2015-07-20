<properties 
	pageTitle="Начало работы с хранилищем Azure" 
	description="Начало работы с использованием хранилища BLOB-объектов Azure в проекте облачной службы в Visual Studio" 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="patshea123"/>

# Начало работы со службой хранилища Azure (проекты облачных служб)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-cloud-services-getting-started-blobs.md)
> - [What Happened](vs-storage-cloud-services-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-cloud-services-getting-started-blobs.md)
> - [Queues](vs-storage-cloud-services-getting-started-queues.md)
> - [Tables](vs-storage-cloud-services-getting-started-tables.md)

Хранилище больших двоичных объектов Azure — это служба хранения большого количества неструктурированных данных, к которым можно получить доступ практически из любой точки мира по протоколам HTTP или HTTPS. Один большой двоичный объект может быть любого размера. Большими двоичными объектами могут быть изображения, аудио- и видеофайлы, необработанные данные и файлы документов.

Для начала работы необходимо сначала создать учетную запись хранилища Azure, а затем один или несколько контейнеров в хранилище. Например, можно создать хранилище с именем "Альбом", в нем создать контейнеры "изображения" для хранения картинок и "аудио" для хранения аудиофайлов. После создания контейнеров в них можно отправлять индивидуальные большие двоичные объекты. Дополнительные сведения о выполнении программных операций с большими двоичными объектами см. в статье [Использование хранилища BLOB-объектов из .NET](storage-dotnet-how-to-use-blobs.md/ "Использование хранилища BLOB-объектов из .NET").

В этой статье описано выполнение стандартных сценариев с использованием службы хранилища больших двоичных объектов Azure. Примеры написаны на C# и используют клиентскую библиотеку службы хранилища Azure для .NET. Здесь описаны такие сценарии, как **отправка**, **перечисление**,**загрузка** и **удаление** BLOB-объектов.

## Программный доступ к хранилищу BLOB-объектов

[AZURE.INCLUDE [storage-dotnet-obtain-assembly](../../includes/storage-dotnet-obtain-assembly.md)]

### Объявления пространств имен
Добавьте следующие объявления пространств имен кода в начало любого файла C#, с помощью которого вы собираетесь получать доступ к службе хранилища Azure программным способом:

    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

Обязательно используйте ссылку на сборку `Microsoft.WindowsAzure.Storage.dll`.

[AZURE.INCLUDE [storage-dotnet-retrieve-conn-string](../../includes/storage-dotnet-retrieve-conn-string.md)]

Тип **CloudBlobClient** позволяет извлекать объекты, представляющие контейнеры и большие двоичные объекты, которые хранятся в службе хранилища больших двоичных объектов. Следующий код создает объект **CloudBlobClient** с помощью объекта учетной записи хранения, полученной выше:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

[AZURE.INCLUDE [storage-dotnet-odatalib-dependencies](../../includes/storage-dotnet-odatalib-dependencies.md)]

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

Хранилище BLOB-объектов Azure поддерживает блочные и страничные BLOB-объекты. В большинстве случаев рекомендуется использовать блочные BLOB-объекты.

Для передачи файла в блочный BLOB-объект получите ссылку на контейнер и используйте ее для получения ссылки на блочный BLOB-объект. Получив ссылку на большой двоичный объект, вы можете отправить в него любой поток данных с помощью метода **UploadFromStream**. Эта операция создает большой двоичный объект, если он не существует, или заменяет его, если он существует. В следующем примере показано, как отправить BLOB-объект в контейнер. Предполагается, что контейнер уже был создан.

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

Для перечисления BLOB-объектов в контейнере сначала необходимо получить ссылку на контейнер. Затем можно использовать метод **ListBlobs** контейнера, чтобы извлечь большие двоичные объекты и/или каталоги в нем. Для доступа к широкому набору свойств и методов возвращаемого объекта **IListBlobItem** необходимо преобразовать его в объект**CloudBlockBlob**, **CloudPageBlob** или **CloudBlobDirectory**. Если тип неизвестен, можно использовать проверку типов, чтобы определить нужный тип. Следующий код демонстрирует, как получить и вывести URI каждого элемента в контейнере `photos`:

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

При вызове метода **ListBlobs** в контейнере «photos» (как в примере выше) возвращаемая коллекция будет содержать объекты **CloudBlobDirectory** и **CloudBlockBlob**, представляющие собой каталоги и большие двоичные объекты верхнего уровня. Здесь результатом будет:

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


При необходимости можно установить для параметра **UseFlatBlobListing** метода **ListBlobs** значение **true**. Это приведет к возвращению каждого большого двоичного объекта в виде **CloudBlockBlob** независимо от каталога. Здесь вызывается метод **ListBlobs**:

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

а здесь результатом будет:

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Дополнительные сведения см. в описании метода [CloudBlobContainer.ListBlobs][].

## Скачивание больших двоичных объектов

Для загрузки BLOB-объектов сначала нужно получить ссылку на BLOB-объект, а затем вызвать метод **DownloadToStream**. В следующем примере метод **DownloadToStream** используется для переноса содержимого большого двоичного объекта в объект потока, который затем можно сохранить в локальном файле.

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

Чтобы удалить большой двоичный объект, сначала нужно получить ссылку на него, а затем вызвать метод **Delete**.

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

В этом примере вы узнаете о выводе плоского списка больших двоичных объектов, а также о том, как вывести иерархический список, задав для параметра `useFlatBlobListing` метода **ListBlobsSegmentedAsync** значение `false`.

Так как в примере вызывается асинхронный метод, нужно добавить ключевое слово `async` перед ним. Этот метод должен вернуть объект **Task**. При ожидании ключевого слова для **ListBlobsSegmentedAsync** метод приостанавливает выполнение примера до тех пор, пока задача размещения результатов не завершена.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter, 
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## Дальнейшие действия

Вы изучили основные сведения о хранилище BLOB-объектов. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам. <ul> <li>Подробную информацию о доступных интерфейсах API см. в справочной документации по службе BLOB-объектов: <ul> <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731">Справочник по клиентской библиотеке хранилища для .NET</a> </li> <li><a href="http://msdn.microsoft.com/library/azure/dd179355">Справочник по REST API</a></li> </ul> </li> <li>Дополнительную информацию о более сложных задачах, которые можно выполнять в службе хранилища Azure, см. в разделе <a href="http://msdn.microsoft.com/library/azure/gg433040.aspx">Хранение данных и получение доступа к ним в Azure</a>.</li> <li>Узнайте, как упростить код, предназначенный для работы со службой хранилища Azure, с помощью <a href="../websites-dotnet-webjobs-sdk/">пакета SDK для веб-заданий для Azure</li>. <li>Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure. <ul> <li>Использование <a href="/documentation/articles/storage-dotnet-how-to-use-tables/">табличного хранилища</a> для хранения структурированных данных.</li> <li>Использование <a href="/documentation/articles/storage-dotnet-how-to-use-queues/">хранилища очередей</a> для хранения неструктурированных данных.</li> <li>Использование <a href="/documentation/articles/sql-database-dotnet-how-to-use/">Базы данных SQL</a> для хранения реляционных данных.</li> </ul> </li> </ul>

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png
  
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
 

<!---HONumber=July15_HO2-->