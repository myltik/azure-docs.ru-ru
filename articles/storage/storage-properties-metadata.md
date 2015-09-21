
<properties 
  pageTitle="Задание и получение свойств и метаданных для объектов в службе хранилища Azure | Microsoft Azure" 
  description="Хранение пользовательских метаданных для объектов в службе хранилища Azure, а также задание и получение свойств системы." 
  services="storage" 
  documentationCenter="" 
  authors="tamram" 
  manager="adinah" 
  editor=""/>

<tags 
  ms.service="storage" 
  ms.workload="storage" 
  ms.tgt_pltfrm="na" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.date="09/03/2015" 
  ms.author="tamram"/>


# Задание и получение свойств и метаданных #

## Обзор

Помимо данных, которые они содержат, объекты в службе хранилища Azure поддерживают свойства системы и пользовательские метаданные.

*   **Свойства системы.** Свойства системы есть у каждого ресурса хранилища. Некоторые из них можно считать или задать, некоторые — только считать. На самом деле, некоторые свойства системы соответствуют определенным стандартным заголовкам HTTP. Они хранятся в клиентской библиотеке службы хранилища Azure.  

*   **Определяемые пользователем метаданные.** Определяемые пользователем метаданные — это метаданные, которые можно указать для определенного ресурса в виде пары "имя-значение". Вы можете использовать метаданные для хранения дополнительных значений для ресурса хранилища. Эти значения являются пользовательскими и не влияют на поведение ресурса.

## Установка и получение свойств

Получение значений свойств и метаданных ресурса хранилища выполняется в два этапа. Прежде чем можно будет считать эти значения, необходимо получить их, вызвав метод **FetchAttributes** или **FetchAttributesAsync**.

> [AZURE.IMPORTANT]Значения свойств и метаданных для ресурса хранилища заполняются только при вызове одного из методов **FetchAttributes**.

Чтобы задать свойства для большого двоичного объекта, укажите значение свойства, а затем вызовите метод **SetProperties** или **SetPropertiesAsync**.

В следующем примере кода будет создан контейнер, а значения свойств будут выведены в окно консоли.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
	
	//Create the service client object for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it does not already exist.
    container.CreateIfNotExists();

    // Fetch container properties and write out their values.
    container.FetchAttributes();
    Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
    Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
    Console.WriteLine("ETag: {0}", container.Properties.ETag);
    Console.WriteLine();

## Установка и получение метаданных

Метаданные можно указать как одну или несколько пар "имя-значение" для BLOB-ресурса или ресурса контейнера. Чтобы задать метаданные, добавьте пары "имя-значение" в коллекцию **метаданные** для ресурса, затем вызовите метод **SetMetadata** для сохранения значений в службу.

> [AZURE.NOTE]\: имя метаданных должно соответствовать соглашениям об именах для идентификаторов C#.
 
Для получения метаданных вызовите метод **FetchAttributes** для BLOB-объекта или контейнера, чтобы заполнить **метаданные** коллекции, затем считайте значения.

В следующем примере кода будет создан новый контейнер, заданы метаданные для него, а затем считаны значения метаданных.

         
	// Account name and key.  Modify for your account.
	<span style="color:Blue;">string accountName = <span style="color:#A31515;">"myaccount";
	<span style="color:Blue;">string accountKey = <span style="color:#A31515;">"SzlFqgzqhfkj594cFoveYqCuvo8v9EESAnOLcTBeBIo31p16rJJRZx/5vU/oY3ZsK/VdFNaVpm6G8YSD2K48Nw==";

	// Get a reference to the storage account and client with authentication credentials.
	StorageCredentials credentials = <span style="color:Blue;">new StorageCredentials(accountName, accountKey);
	CloudStorageAccount storageAccount = <span style="color:Blue;">new CloudStorageAccount(credentials, <span style="color:Blue;">true);
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve a reference to a container. 
	CloudBlobContainer container = blobClient.GetContainerReference(<span style="color:#A31515;">"mycontainer");

	// Create the container if it does not already exist.
	container.CreateIfNotExists();

	// Set metadata for the container.
	container.Metadata[<span style="color:#A31515;">"category"] = <span style="color:#A31515;">"images";
	container.Metadata[<span style="color:#A31515;">"owner"] = <span style="color:#A31515;">"azure";
	container.SetMetadata();

	// Get container metadata.
	container.FetchAttributes();
	<span style="color:Blue;">foreach (<span style="color:Blue;">string key <span style="color:Blue;">in container.Metadata.Keys)
	{
	   Console.WriteLine(<span style="color:#A31515;">"Metadata key: " + key);
	   Console.WriteLine(<span style="color:#A31515;">"Metadata value: " + container.Metadata[key]);
	}

	//Clean up.
	container.Delete();

## См. также  

- [Справочник по клиентской библиотеке службы хранилища Azure](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [Начало работе с хранилищем больших двоичных объектов для .NET](storage-dotnet-how-to-use-blobs.md)  
 

<!---HONumber=Sept15_HO2-->