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
	ms.date="04/20/2015" 
	ms.author="patshea123"/>

# Начало работы со службой хранилища Azure (проекты ASP.NET 5)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-blobs.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

Хранилище больших двоичных объектов Azure — это служба хранения большого количества неструктурированных данных, к которым можно получить доступ практически из любой точки мира по протоколам HTTP или HTTPS. Один большой двоичный объект может быть любого размера. Большими двоичными объектами могут быть изображения, аудио- и видеофайлы, необработанные данные и файлы документов.

Для начала работы необходимо сначала создать учетную запись хранилища Azure, а затем один или несколько контейнеров в хранилище. Например, можно создать хранилище с именем "Альбом", в нем создать контейнеры "изображения" для хранения картинок и "аудио" для хранения аудиофайлов. После создания контейнеров в них можно отправлять индивидуальные большие двоичные объекты. Дополнительные сведения о выполнении программных операций с большими двоичными объектами см. в статье [Использование хранилища BLOB-объектов из .NET](storage-dotnet-how-to-use-blobs.md "Использование хранилища BLOB-объектов из .NET").

Для программного доступа к большим двоичным объектам в проектах ASP.NET 5 необходимо добавить следующие элементы, если они еще не существуют.

1. Добавьте следующие объявления пространств имен кода в начало любого файла C#,в котором вы собираетесь получать доступ к хранилищу Azure программным способом.

		using Microsoft.Framework.ConfigurationModel;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
		using System.Threading.Tasks;

2. Используйте следующий код для получения параметров настройки.

		 IConfigurationSourceRoot config = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

#####Получение строки подключения к хранилищу
Прежде чем что-либо делать с большим двоичным объектом, необходимо получить строку подключения для учетной записи хранилища, в котором он будет располагаться. Для представления информации об учетной записи хранения используется тип **CloudStorageAccount**. При использовании проекта ASP.NET 5 можно вызвать метод GET объекта Configuration, чтобы получить строку подключения к хранилищу и сведения учетной записи хранилища из конфигурации службы Azure, как показано в следующем примере кода.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

#####Создание контейнера
Так же как файлы располагаются в папках, большие двоичные объекты хранилища располагаются в контейнерах. Вы можете добавить ссылку на существующий контейнер с помощью объекта **CloudBlobClient** или создать новый, вызвав метод CreateCloudBlobClient().

В следующем примере кода показано, как создать контейнер хранилища больших двоичных объектов. Для начала этот код создает объект **BlobClient**, открывающий доступ к функциям объекта, например созданию контейнера хранилища. Затем код ссылается на контейнер с именем mycontainer. Если контейнер с таким именем отсутствует, он будет создан.

**ПРИМЕЧАНИЕ.** Интерфейсы API, которые выполняют вызовы в хранилище Azure в ASP.NET 5, являются асинхронными. Дополнительные сведения см. в статье [Асинхронное программирование с использованием ключевых слов Async и Await](http://msdn.microsoft.com/library/hh191443.aspx). В следующем примере кода предполагается, что используются асинхронные методы программирования.

	// Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “mycontainer.”
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();    

По умолчанию новый контейнер закрытый, и вам необходимо указать ключ доступа к хранилищу, чтобы загрузить BLOB-объекты из этого контейнера. Вы можете сделать файлы в контейнере доступными для всех пользователей, сделав контейнер общедоступным с помощью следующего кода.

	await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

**ПРИМЕЧАНИЕ.** Вставьте этот код полностью перед кодом из следующих разделов.

#####Отправка BLOB-объекта в контейнер
Для отправки файла большого двоичного объекта в контейнер получите ссылку на контейнер и используйте ее для получения ссылки на большой двоичный объект. Получив ссылку на большой двоичный объект, вы можете отправить в него любой поток данных с помощью метода **UploadFromStreamAsync()**. Эта операция создает большой двоичный объект, если он не существует, или заменяет его, если он существует. В следующем примере показано, как отправить BLOB-объект в контейнер. Предполагается, что контейнер уже был создан.

	// Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");            

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

#####Перечисление BLOB-объектов в контейнере
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

Можно вывести список содержимого контейнера BLOB-объектов и другими способами. Дополнительные сведения см. в статье [Как использовать хранилище больших двоичных объектов из .NET](storage-dotnet-how-to-use-blobs.md/#list-blob).

#####Загрузка BLOB-объектов
Чтобы скачать большой двоичный объект, сначала получите ссылку на него, а затем вызовите метод **DownloadToStreamAsync()**. В следующем примере метод **DownloadToStreamAsync()** используется для переноса содержимого большого двоичного объекта в объект потока, который затем можно сохранить в локальном файле.

	// Get a reference to a blob named "photo1.jpg".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

	// Save the blob contents to a file named “myfile”.
	using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
	{
    	await blockBlob.DownloadToStreamAsync(fileStream);
	}

Вы можете сохранить BLOB-объекты в виде файлов и другими способами. Дополнительные сведения см. в статье [Как использовать хранилище больших двоичных объектов из .NET](storage-dotnet-how-to-use-blobs.md/#download-blobs).

#####Удаление большого двоичного объекта
Чтобы удалить большой двоичный объект, сначала получите ссылку на него, а затем вызовите метод **DeleteAsync()**.

	// Get a reference to a blob named "myblob.txt".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

	// Delete the blob.
	await blockBlob.DeleteAsync();

[Дополнительные сведения о службе хранилища Azure](http://azure.microsoft.com/documentation/services/storage/) См. также статьи [Обзор ресурсов хранения и управление ими в обозревателе серверов](http://msdn.microsoft.com/library/azure/ff683677.aspx) и [ASP.NET 5](http://www.asp.net/vnext).
 

<!---HONumber=58_postMigration-->