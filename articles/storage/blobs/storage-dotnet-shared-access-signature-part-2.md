---
title: Создание и использование подписанного URL-адреса (SAS) с хранилищем BLOB-объектов Azure | Документация Майкрософт
description: В этом руководстве показано, как создать подписанные URL-адреса для использования с хранилищем больших двоичных объектов и как использовать их в клиентских приложениях.
services: storage
documentationcenter: ''
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 491e0b3c-76d4-4149-9a80-bbbd683b1f3e
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.openlocfilehash: 9dde12acde748c48b56f9f96ee772fca49954358
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23056799"
---
# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>Подписанные URL-адреса. Часть 2: создание и использование подписанного URL-адреса в службе BLOB-объектов

[В части 1](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) этого учебника приведен обзор подписей общего доступа (SAS), а также даны советы и рекомендации по их использованию. В части 2 показано, как создать и затем использовать подписи общего доступа с помощью хранилища BLOB-объектов. Примеры написаны на C# и используют библиотеку клиента хранения Azure для .NET. В примерах этого руководства выполняются следующие задачи:

* Создание подписанного URL-адреса для контейнера.
* Создание подписанного URL-адреса для большого двоичного объекта.
* Создание хранимой политики доступа для управления подписями в ресурсах контейнера.
* Проверка подписанных URL-адресов в клиентском приложении.

## <a name="about-this-tutorial"></a>О данном учебнике
В этом руководстве мы создадим два консольных приложения, демонстрирующих создание и использование подписанных URL-адресов для контейнеров и больших двоичных объектов.

**Приложение 1.** Приложение управления. Создает подписанный URL-адрес для контейнера и большого двоичного объекта. Включает ключ доступа учетной записи в исходном коде.

**Приложение 2.** Клиентское приложение. Обращается к ресурсам контейнера и большого двоичного объекта с использованием подписанных URL-адресов, созданных с помощью первого приложения. Использует только подписанные URL-адреса для доступа к ресурсам контейнера и большого двоичного объекта. Оно *не* включает ключ доступа к учетной записи хранения.

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>Часть 1. Создание консольного приложения для создания подписанных URL-адресов
Прежде всего убедитесь, что установлена библиотека клиента хранения Azure для .NET. Вы можете установить [пакет NuGet](http://nuget.org/packages/WindowsAzure.Storage/ "пакет NuGet"), содержащий самые новые сборки для клиентской библиотеки. Так вы будете получать все последние исправления. Клиентскую библиотеку также можно скачать в составе последней версии [пакета Azure SDK для .NET](https://azure.microsoft.com/downloads/).

В Visual Studio создайте новое консольное приложение Windows и назовите его **GenerateSharedAccessSignatures**. Добавьте ссылки на [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) и [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/), используя один из следующих методов.

* Воспользуйтесь [диспетчером пакетов NuGet](https://docs.nuget.org/consume/installing-nuget) в Visual Studio. Выберите **Проект** > **Управление пакетами NuGet**, выполните поиск в Интернете для каждого пакета (Microsoft.WindowsAzure.ConfigurationManager и WindowsAzure.Storage) и установите их.
* Вы также можете найти нужные сборки в установленных файлах пакета SDK Azure и добавить ссылки на них:
  * Microsoft.WindowsAzure.Configuration.dll.
  * Microsoft.WindowsAzure.Storage.dll

Добавьте в начало файла Program.cs следующие директивы **using**:

```csharp
using System.IO;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

Измените файл app.config, содержащий параметр конфигурации со строкой подключения, которая указывает на вашу учетную запись хранения. Файл app.config должен иметь примерно такой вид:

```xml
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
  </appSettings>
</configuration>
```

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>Создание универсального кода ресурса (URI) подписанного URL-адреса для контейнера
Для начала мы добавим метод для создания подписанного URL-адреса для нового контейнера. В этом случае подпись не связана с хранимой политикой доступа, поэтому содержит сведения URI с указанием времени окончания срока действия подписи и предоставляемых ей разрешений.

Во-первых, добавьте код для метода **Main()** , чтобы проверить подлинности доступа к вашей учетной записи хранения и создать новый контейнер:

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Insert calls to the methods created below here...

    //Require user input before closing the console window.
    Console.ReadLine();
}
```

Затем добавьте метод, который создает подписанный URL-адрес для контейнера и возвращает URI подписи:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
    //Set the expiry time and permissions for the container.
    //In this case no start time is specified, so the shared access signature becomes valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

В нижнюю часть метода **Main()** перед вызовом **Console.ReadLine()** добавьте следующие строки для вызова метода **GetContainerSasUri()** и вывода URI подписи в окно консоли:

```csharp
//Generate a SAS URI for the container, without a stored access policy.
Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
Console.WriteLine();
```

Скомпилируйте и запустите выходные данные URI подписи общего доступа для нового контейнера. Этот URI будет похож на следующий:

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D
```

После выполнения кода созданный для контейнера подписанный URL-адрес будет действителен в течение следующих 24 часов. Эта подпись предоставляет клиенту разрешение на перечисление больших двоичных объектов в контейнере и на запись новых больших двоичных объектов в контейнер.

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>Создание URI подписанного URL-адреса для большого двоичного объекта
Далее мы напишем аналогичный код, создающий большой двоичный объект в контейнере и подписанный URL-адрес для него. Этот подписанный URL-адрес не связан с хранимой политикой доступа, поэтому он включает в себя время начала действия, время окончания действия и разрешения в виде URI.

Добавьте новый метод, который создает большой двоичный объект и записывает туда какой-либо текст, а затем создает подписанный URL-адрес и возвращает URI подписи:

```csharp
static string GetBlobSasUri(CloudBlobContainer container)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature (SAS).";
    blob.UploadText(blobContent);

    //Set the expiry time and permissions for the blob.
    //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
    //The shared access signature will be valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

Добавьте следующие строки в нижнюю часть метода **Main()**, чтобы вызвать метод **GetBlobSasUri()** перед вызовом **Console.ReadLine()** и вывести подписанный URL-адрес в окно консоли:

```csharp
//Generate a SAS URI for a blob within the container, without a stored access policy.
Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
Console.WriteLine();
```

Скомпилируйте и запустите выходные данные URI подписи общего доступа для нового BLOB-объекта. Этот URI будет похож на следующий:

```
https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D
```

### <a name="create-a-stored-access-policy-on-the-container"></a>Создание хранимой политики доступа для контейнера
Теперь давайте создадим хранимую политику доступа для контейнера, которая будет определять ограничения для всех связанных с ней подписей общего доступа.

В предыдущих примерах мы указали время начала действия (явно или косвенно), время истечения срока действия, а также разрешения в самом URI подписи общего доступа. В следующих примерах мы укажем эти сведения в хранимой политике доступа, а не в подписанном URL-адресе. Это позволяет нам изменять такие ограничения без повторного выпуска подписи общего доступа.

В подписанном URL-адресе можно использовать одно или несколько ограничений, а остальные ограничения задать в хранимой политике доступа. Однако время начала действия, время окончания срока действия и разрешения можно указать только в одном из этих двух ресурсов. Например, нельзя задать разрешения в подписанном URL-адресе, а также в хранимой политике доступа.

При добавлении хранимой политики доступа к контейнеру, необходимо получить имеющиеся разрешения контейнера, добавить новую политику доступа, а потом задать разрешения контейнера.

Добавьте новый метод, который создает новую хранимую политику доступа в контейнере и возвращает имя политики.

```csharp
static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
    string policyName)
{
    //Get the container's existing permissions.
    BlobContainerPermissions permissions = container.GetPermissions();

    //Create a new shared access policy and define its constraints.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    //Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    container.SetPermissions(permissions);
}
```

В нижнюю часть метода **Main()** перед вызовом **Console.ReadLine()** добавьте следующие строки, чтобы сначала удалить все имеющиеся политики доступа, а потом вызвать метод **CreateSharedAccessPolicy()**:

```csharp
//Clear any existing access policies on container.
BlobContainerPermissions perms = container.GetPermissions();
perms.SharedAccessPolicies.Clear();
container.SetPermissions(perms);

//Create a new access policy on the container, which may be optionally used to provide constraints for
//shared access signatures on the container and the blob.
string sharedAccessPolicyName = "tutorialpolicy";
CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);
```

Когда вы удаляете политики доступа в контейнере, необходимо сначала получить его имеющиеся разрешения, удалить их, а затем снова задать.

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>Создание URI подписанного URL-адреса для контейнера, использующего политику доступа
Далее мы создадим другой подписанный URL-адрес для созданного ранее контейнера, но на этот раз мы свяжем подпись с хранимой политикой доступа, созданной в предыдущем примере.

Добавьте новый метод для создания другого подписанного URL-адреса для контейнера:

```csharp
static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Generate the shared access signature on the container. In this case, all of the constraints for the
    //shared access signature are specified on the stored access policy.
    string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

В нижнюю часть метода **Main()** перед вызовом **Console.ReadLine()** добавьте следующие строки, чтобы вызвать метод **GetContainerSasUriWithPolicy**:

```csharp
//Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>Создание URI подписи общего доступа для BLOB-объекта, использующей политику доступа
Наконец мы добавим аналогичный метод для создания другого большого двоичного объекта и подписанного URL-адреса, сопоставленного с хранимой политикой доступа.

Добавьте новый метод для создания BLOB-объекта и подписи общего доступа:

```csharp
static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature. " +
    "A stored access policy defines the constraints for the signature.";
    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    ms.Position = 0;
    using (ms)
    {
        blob.UploadFromStream(ms);
    }

    //Generate the shared access signature on the blob.
    string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

В нижнюю часть метода **Main()** перед вызовом **Console.ReadLine()** добавьте следующие строки, чтобы вызвать метод **GetBlobSasUriWithPolicy**:

```csharp
//Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

Теперь метод **Main()** должен выглядеть следующим образом. Запустите его, чтобы записать URI подписей общего доступа в окне консоли, а затем скопируйте и вставьте их в текстовый файл для использования во второй части учебника.

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    Console.ReadLine();
}
```

При запуске консольного приложения GenerateSharedAccessSignatures его результат должен быть аналогичен приведенному ниже. Это подписанные URL-адреса, которые используются во второй части руководства.

```
Container SAS URI: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=pFlEZD%2F6sJTNLxD%2FQ26Hh85j%2FzYPxZav6mP1KJwnvJE%3D&se=2017-05-16T16%3A16%3A47Z&sp=wl

Blob SAS URI: https://storagesample.blob.core.windows.net/sascontainer/sasblob.txt?sv=2016-05-31&sr=b&sig=%2FiBWAZbXESzCMvRcm7JwJBK0gT0BtPSWEq4pRwmlBRI%3D&st=2017-05-15T16%3A11%3A48Z&se=2017-05-16T16%3A16%3A48Z&sp=rw

Container SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&si=tutorialpolicy&sig=aMb6rKDvvpfiGVsZI2rCmyUra6ZPpq%2BZ%2FLyTgAeec%2Bk%3D

Blob SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer/sasblobpolicy.txt?sv=2016-05-31&sr=b&si=tutorialpolicy&sig=%2FkTWkT23SS45%2FoF4bK2mqXkN%2BPKs%2FyHuzkfQ4GFoZVU%3D
```

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>Часть 2. Создание консольного приложения для проверки подписанных URL-адресов
Чтобы проверить подписанные URL-адреса, созданные в предыдущих примерах, мы создадим второе консольное приложение, использующее подписи для выполнения операций с контейнером и большим двоичным объектом.

> [!NOTE]
> Если с момента прохождения первой части учебника прошло больше 24 часов, созданные подписи будут недействительными. В этом случае следует запустить код в первом консольном приложении для создания новых подписей общего доступа, которые можно использовать во второй части учебника.
>

В Visual Studio создайте новое консольное приложение Windows и назовите его **ConsumeSharedAccessSignatures**. Как и раньше, добавьте ссылки на [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) и [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/).

Добавьте в начало файла Program.cs следующие директивы **using**:

```csharp
using System.IO;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

В тело метода **Main()** добавьте следующие строковые константы и измените их значения на подписанные URL-адреса, созданные в первой части руководства.

```csharp
static void Main(string[] args)
{
    const string containerSAS = "<your container SAS>";
    const string blobSAS = "<your blob SAS>";
    const string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    const string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
}
```

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>Добавление метода для оценки операций с контейнером с использованием подписанного URL-адреса
Далее мы добавим метод, который проверяет некоторые операции с контейнером, используя подписанный URL-адрес для этого контейнера. Подписанный URL-адрес используется для возврата ссылки на контейнер, обеспечивая проверку подлинности доступа к контейнеру на основании одной подписи.

Добавьте в класс Program.cs следующий метод:

```csharp
static void UseContainerSAS(string sas)
{
    //Try performing container operations with the SAS provided.

    //Return a reference to the container using the SAS URI.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

    //Create a list to store blob URIs returned by a listing operation on the container.
    List<ICloudBlob> blobList = new List<ICloudBlob>();

    //Write operation: write a new blob to the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
        string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
        blob.UploadText(blobContent);

        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //List operation: List the blobs in the container.
    try
    {
        foreach (ICloudBlob blob in container.ListBlobs())
        {
            blobList.Add(blob);
        }
        Console.WriteLine("List operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("List operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Get a reference to one of the blobs in the container and read it.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        MemoryStream msRead = new MemoryStream();
        msRead.Position = 0;
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            Console.WriteLine(msRead.Length);
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    Console.WriteLine();

    //Delete operation: Delete a blob in the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

Измените метод **Main()** для вызова **UseContainerSAS()** с обоими подписанными URL-адресами, созданными в контейнере:

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    Console.ReadLine();
}
```

### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>Добавление метода для оценки операций с большим двоичным объектом с использованием подписанного URL-адреса
Наконец, мы добавим метод, который проверяет некоторые операции с большим двоичным объектом, используя подписанный URL-адрес для этого большого двоичного объекта. В этом случае мы используем конструктор **CloudBlockBlob(String)**, передавая подписанный URL-адрес, чтобы возвратить ссылку на большой двоичный объект. Никакая другая проверка подлинности не требуется, она основана только на подписи.

Добавьте в класс Program.cs следующий метод:

```csharp
static void UseBlobSAS(string sas)
{
    //Try performing blob operations using the SAS provided.

    //Return a reference to the blob using the SAS URI.
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

    //Write operation: Write a new blob to the container.
    try
    {
        string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
        MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        msWrite.Position = 0;
        using (msWrite)
        {
            blob.UploadFromStream(msWrite);
        }
        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Read the contents of the blob.
    try
    {
        MemoryStream msRead = new MemoryStream();
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            msRead.Position = 0;
            using (StreamReader reader = new StreamReader(msRead, true))
            {
                string line;
                while ((line = reader.ReadLine()) != null)
                {
                    Console.WriteLine(line);
                }
            }
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Delete operation: Delete the blob.
    try
    {
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

Измените метод **Main()** для вызова **UseBlobSAS()** с обоими подписанными URL-адресами, созданными для BLOB-объекта:

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
    UseBlobSAS(blobSAS);
    UseBlobSAS(blobSASWithAccessPolicy);

    Console.ReadLine();
}
```

Запустите консольное приложение и просмотрите выходные данные, чтобы узнать, какие операции разрешены для разных подписей. Выходные данные в окне консоли будут выглядеть следующим образом:

```
Write operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

List operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

Read operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

Delete operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

...
```

## <a name="next-steps"></a>Дальнейшие действия

* [Подписанные URL-адреса. Часть 1: общие сведения о модели SAS](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Управление анонимным доступом на чтение к контейнерам и большим двоичным объектам](storage-manage-access-to-resources.md)
* [Delegating Access with a Shared Access Signature](http://msdn.microsoft.com/library/azure/ee395415.aspx) (Делегирование доступа с помощью подписанного URL-адреса)
* [Введение в использование SAS таблиц и очередей](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
