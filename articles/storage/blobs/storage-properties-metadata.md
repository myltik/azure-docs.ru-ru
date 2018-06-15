---
title: Задание и получение свойств и метаданных объектов в службе хранилища Azure | Документация Майкрософт
description: Хранение пользовательских метаданных для объектов в службе хранилища Azure, а также задание и получение свойств системы.
services: storage
documentationcenter: ''
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 036f9006-273e-400b-844b-3329045e9e1f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.openlocfilehash: a3eb598b2dabd4986c72b8814926eb0944707050
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23056059"
---
# <a name="set-and-retrieve-properties-and-metadata"></a>Задание и получение свойств и метаданных

Кроме данных, которые они содержат, объекты в службе хранилища Azure поддерживают свойства системы и пользовательские метаданные. В этой статье описывается управление свойствами системы и определяемыми пользователем метаданными с помощью [клиентской библиотеки хранилища Azure для .NET](https://www.nuget.org/packages/WindowsAzure.Storage/).

* **Свойства системы.** Свойства системы есть у каждого ресурса хранилища. Некоторые из них можно считать или задать, некоторые — только считать. На самом деле, некоторые свойства системы соответствуют определенным стандартным заголовкам HTTP. Они хранятся в клиентской библиотеке службы хранилища Azure.

* **Определяемые пользователем метаданные.** Метаданные, которые можно указать для определенного ресурса в виде пары "имя — значение". Вы можете использовать метаданные для хранения дополнительных значений с использованием ресурса хранилища. Эти значения являются пользовательскими и не влияют на поведение ресурса.

Получение значений свойств и метаданных ресурса хранилища выполняется в два этапа. Прежде чем считывать эти значения, необходимо получить их, вызвав метод **FetchAttributesAsync**.

> [!IMPORTANT]
> Значения свойств и метаданных для ресурса хранилища заполняются только при вызове одного из методов **FetchAttributesAsync**.
>
> Если любая из пар имен и значений содержит знаки не из набора ASCII, возвратится ошибка с кодом `400 Bad Request`. Пары имен и значений метаданных являются действительными HTTP-заголовками, поэтому они должны соответствовать всем ограничениям для HTTP-заголовков. Для имен и значений, содержащих знаки не из набора ASCII, мы рекомендуем использовать кодировку URL или кодировку Base64.
>

## <a name="setting-and-retrieving-properties"></a>Установка и получение свойств
Чтобы получить значения свойств, вызовите метод **FetchAttributesAsync** для BLOB-объекта или контейнера, а затем считайте значения.

Чтобы задать свойства объекта, укажите значение свойства, а затем вызовите метод **SetProperties** .

В следующем примере кода будет создан контейнер, а значения некоторых свойств будут выведены в окно консоли.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

//Create the service client object for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it does not already exist.
container.CreateIfNotExists();

// Fetch container properties and write out their values.
await container.FetchAttributesAsync();
Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
Console.WriteLine("ETag: {0}", container.Properties.ETag);
Console.WriteLine();
```

## <a name="setting-and-retrieving-metadata"></a>Установка и получение метаданных
Метаданные можно указать как одну или несколько пар "имя-значение" для BLOB-ресурса или ресурса контейнера. Чтобы задать метаданные, добавьте пары "имя — значение" в коллекцию **Metadata** ресурса, а затем вызовите метод **SetMetadata**, чтобы сохранить значения в службе.

> [!NOTE]
> Имя метаданных должно соответствовать соглашениям об именовании идентификаторов C#.
>
>

В следующем примере кода задаются метаданные для контейнера. Одно значение задается с помощью метода коллекции **Add** . Другое значение задается с помощью неявного синтаксиса «ключ/значение». Можно использовать любой из способов.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    // Add some metadata to the container.
    container.Metadata.Add("docType", "textDocuments");
    container.Metadata["category"] = "guidance";

    // Set the container's metadata.
    await container.SetMetadataAsync();
}
```

Чтобы получить метаданные, вызовите метод **FetchAttributes** для BLOB-объекта или контейнера (для заполнения коллекции **Metadata**), а затем считайте значения, как показано в примере ниже.

```csharp
public static async Task ListContainerMetadataAsync(CloudBlobContainer container)
{
    // Fetch container attributes in order to populate the container's properties and metadata.
    await container.FetchAttributesAsync();

    // Enumerate the container's metadata.
    Console.WriteLine("Container metadata:");
    foreach (var metadataItem in container.Metadata)
    {
        Console.WriteLine("\tKey: {0}", metadataItem.Key);
        Console.WriteLine("\tValue: {0}", metadataItem.Value);
    }
}
```

## <a name="next-steps"></a>Дополнительная информация
* [Справочные материалы клиентской библиотеки хранилища Azure для .NET](/dotnet/api/?term=Microsoft.WindowsAzure.Storage)
* [Клиентская библиотека хранилища Azure для пакетов NuGet .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
