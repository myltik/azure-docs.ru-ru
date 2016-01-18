
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
  ms.date="01/05/2016" 
  ms.author="tamram"/>


# Задание и получение свойств и метаданных #

## Обзор

Помимо данных, которые они содержат, объекты в службе хранилища Azure поддерживают свойства системы и пользовательские метаданные.

*   **Свойства системы.** Свойства системы есть у каждого ресурса хранилища. Некоторые из них можно считать или задать, некоторые — только считать. На самом деле, некоторые свойства системы соответствуют определенным стандартным заголовкам HTTP. Они хранятся в клиентской библиотеке службы хранилища Azure.  

*   **Определяемые пользователем метаданные.** Определяемые пользователем метаданные — это метаданные, которые можно указать для определенного ресурса в виде пары "имя-значение". Вы можете использовать метаданные для хранения дополнительных значений для ресурса хранилища. Эти значения являются пользовательскими и не влияют на поведение ресурса.

Получение значений свойств и метаданных ресурса хранилища выполняется в два этапа. Прежде чем считывать эти значения, необходимо получить их, вызвав метод **FetchAttributes**.

> [AZURE.IMPORTANT]Значения свойств и метаданных для ресурса хранилища заполняются только при вызове одного из методов **FetchAttributes**.

## Установка и получение свойств

Чтобы получить значения свойств, вызовите метод **FetchAttributes** для BLOB-объекта или контейнера, а затем считайте значения.

Чтобы задать свойства объекта, укажите значение свойства, а затем вызовите метод **SetProperties**.

В следующем примере кода будет создан контейнер, а значения некоторых свойств будут выведены в окно консоли.

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
 
В следующем примере кода задаются метаданные для контейнера. Одно значение задается с помощью метода коллекции **Add**. Другое значение задается с помощью неявного синтаксиса «ключ/значение». Можно использовать любой из способов.

    public static void AddContainerMetadata(CloudBlobContainer container)
    {
        //Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        //Set the container's metadata.
        container.SetMetadata();
    }

Для получения метаданных вызовите метод **FetchAttributes** для BLOB-объекта или контейнера, чтобы заполнить **метаданные** коллекции, затем считайте значения, как показано в приведенном примере.

    public static void ListContainerMetadata(CloudBlobContainer container)
    {
        //Fetch container attributes in order to populate the container's properties and metadata.
        container.FetchAttributes();

        //Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }

## См. также  

- [Справочные материалы клиентской библиотеки хранилища Azure для .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [Клиентская библиотека хранилища Azure для .NET](https://www.nuget.org/packages/WindowsAzure.Storage/) 

<!---HONumber=AcomDC_0107_2016-->