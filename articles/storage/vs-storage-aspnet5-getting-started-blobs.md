<properties 
	pageTitle="Начало работы с хранилищем Azure" 
	description="Начало работы с использованием хранилища больших двоичных объектов Azure в проекте ASP.NET 5 в Visual Studio" 
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
	ms.date="07/22/2015" 
	ms.author="patshea123"/>

# Начало работы со службой хранилища Azure (проекты ASP.NET 5)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-blobs.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

Хранилище больших двоичных объектов Azure — это служба хранения большого количества неструктурированных данных, к которым можно получить доступ практически из любой точки мира по протоколам HTTP или HTTPS. Один большой двоичный объект может быть любого размера. Большими двоичными объектами могут быть изображения, аудио- и видеофайлы, необработанные данные и файлы документов. В этой статье описывается, как приступить к работе с хранилищем больших двоичных объектов после создания учетной записи хранения Azure с помощью диалогового окна **Добавление подключенных служб** в проекте ASP.NET 5.

Так же как файлы располагаются в папках, большие двоичные объекты хранилища располагаются в контейнерах. После создания хранилища вы можете создать один или несколько контейнеров в нем. Например, можно создать хранилище с именем "Альбом", в нем создать контейнеры "изображения" для хранения картинок и "аудио" для хранения аудиофайлов. После создания контейнеров в них можно отправлять индивидуальные большие двоичные объекты. Дополнительные сведения о выполнении программных операций с большими двоичными объектами см. в статье [Использование хранилища BLOB-объектов из .NET](storage-dotnet-how-to-use-blobs.md "Использование хранилища BLOB-объектов из .NET").

##Создание контейнеров больших двоичных объектов в обозревателе серверов Visual Studio

[AZURE.INCLUDE [vs-create-blob-container-in-server-explorer](../../includes/vs-create-blob-container-in-server-explorer.md)]



##Доступ к контейнерам больших двоичных объектов в коде 

Для программного доступа к большим двоичным объектам в проектах ASP.NET 5 необходимо добавить следующие элементы, если они еще не существуют.

1. Добавляйте следующие объявления пространств имен кода в начало каждого файла C#, в котором вы собираетесь обращаться к службе хранилища Azure программным способом.

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
		using System.Threading.Tasks;
		using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Получите объект **CloudStorageAccount**, представляющий данные учетной записи хранения. Используйте следующий код, чтобы получить строку подключения и сведения об учетной записи хранения из конфигурации службы Azure.

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));

    **ПРИМЕЧАНИЕ.** Вставьте весь код, представленный выше, перед кодом из следующих разделов.


3. Используйте объект **CloudBlobClient**, чтобы получить ссылку **CloudBlobContainer** на существующий контейнер в вашей учетной записи хранения.

		// Create a blob client.
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");



##Создание контейнера в коде

Вы также можете использовать объект **CloudBlobClient** для создания контейнера в вашей учетной записи хранения. Вам нужно всего лишь добавить вызов `CreateIfNotExistsAsync()`, как в следующем примере:

	// Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “my-new-container.”
    CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();    


**ПРИМЕЧАНИЕ.** Интерфейсы API, которые выполняют вызовы в хранилище Azure в ASP.NET 5, являются асинхронными. Дополнительные сведения см. в статье [Асинхронное программирование с использованием ключевых слов Async и Await](http://msdn.microsoft.com/library/hh191443.aspx). В следующем примере кода предполагается, что используются асинхронные методы программирования.

Чтобы сделать файлы в контейнере доступными для всех пользователей, сделайте контейнер общедоступным с помощью следующего примера кода.

	await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

##Отправка BLOB-объекта в контейнер

Для отправки файла большого двоичного объекта в контейнер получите ссылку на контейнер и используйте ее для получения ссылки на большой двоичный объект. Получив ссылку на большой двоичный объект, вы можете отправить в него любой поток данных с помощью метода **UploadFromStreamAsync()**. Эта операция создает большой двоичный объект, если он не существует, или заменяет его, если он существует. В следующем примере показано, как отправить BLOB-объект в контейнер. Предполагается, что контейнер уже был создан.

	// Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");            

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

##Перечисление BLOB-объектов в контейнере
Для перечисления BLOB-объектов в контейнере сначала необходимо получить ссылку на контейнер. После этого вы сможете считывать из контейнера большие двоичные объекты и каталоги с помощью метода **ListBlobsSegmentedAsync()**. Для доступа к широкому набору свойств и методов возвращаемого объекта **IListBlobItem** необходимо преобразовать его в объект**CloudBlockBlob**, **CloudPageBlob** или **CloudBlobDirectory**. Если тип большого двоичного объекта неизвестен, можно использовать проверку типов, чтобы определить нужный. Следующий код показывает, как получить и вывести URI каждого элемента в контейнере.

	BlobContinuationToken token = null;
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
            token = resultSegment.ContinuationToken;

            foreach (IListBlobItem item in resultSegment.Results)
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
        } while (token != null);

Можно вывести список содержимого контейнера BLOB-объектов и другими способами. Дополнительные сведения см. в статье [Как использовать хранилище больших двоичных объектов из .NET](storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container).

##Загрузка BLOB-объектов
Чтобы скачать большой двоичный объект, сначала получите ссылку на него, а затем вызовите метод **DownloadToStreamAsync()**. В следующем примере метод **DownloadToStreamAsync()** используется для переноса содержимого большого двоичного объекта в объект потока, который затем можно сохранить в локальном файле.

	// Get a reference to a blob named "photo1.jpg".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

	// Save the blob contents to a file named “myfile”.
	using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
	{
    	await blockBlob.DownloadToStreamAsync(fileStream);
	}

Вы можете сохранить BLOB-объекты в виде файлов и другими способами. Дополнительные сведения см. в статье [Как использовать хранилище больших двоичных объектов из .NET](storage-dotnet-how-to-use-blobs.md/#download-blobs).

##Удаление большого двоичного объекта
Чтобы удалить большой двоичный объект, сначала получите ссылку на него, а затем вызовите метод **DeleteAsync()**.

	// Get a reference to a blob named "myblob.txt".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

	// Delete the blob.
	await blockBlob.DeleteAsync();

## Дальнейшие действия

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]



 
[Image1]: ./media/vs-storage-aspnet5-getting-started-blobs/vs-storage-create-blob-containers-in-Server-Explorer.png

<!---HONumber=July15_HO5-->