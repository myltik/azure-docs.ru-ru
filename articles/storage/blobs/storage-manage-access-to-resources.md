---
title: Включение открытого доступа на чтение к контейнерам и большим двоичным объектам в хранилище BLOB-объектов Azure | Документация Майкрософт
description: Узнайте, как сделать контейнеры и большие двоичные объекты доступными для анонимного доступа и как осуществлять к ним программный доступ.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 04/26/2017
ms.author: tamram
ms.openlocfilehash: 4ddafb095816b5be82a18faa9c60869094e5e4c6
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/24/2018
ms.locfileid: "29557069"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Управление анонимным доступом на чтение к контейнерам и большим двоичным объектам
Можно включить анонимный открытый доступ на чтение к контейнеру и его большим двоичным объектам в хранилище BLOB-объектов Azure. Таким образом можно предоставить доступ только для чтения к этим ресурсам, не передавая ключ учетной записи и не требуя подписанного URL-адреса (SAS).

Открытый доступ на чтение лучше всего подходит для сценариев, когда определенные большие двоичные объекты нужно сделать всегда доступными для анонимного доступа на чтение. Для более точного контроля можно создать подписанный URL-адрес. Подписанные URL-адреса позволяют предоставить ограниченный доступ с различными разрешениями на определенный период времени. Дополнительные сведения о создании подписанных URL-адресов см. в статье [Использование подписанных URL-адресов (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Предоставление анонимным пользователям разрешений для контейнеров и больших двоичных объектов
По умолчанию контейнер и все большие двоичные объекты внутри него доступны только владельцу учетной записи хранения. Чтобы предоставить анонимным пользователями доступ на их чтение, следует разрешить общий доступ к контейнеру. Анонимные пользователи могут считывать данные большого двоичного объекта из общедоступного контейнера, при этом их запросы не будут проходить аутентификацию.

Можно настроить контейнер со следующими разрешениями:

* **Без открытого доступа на чтение.** К контейнеру и его большим двоичным объектам может обращаться только владелец учетной записи хранения. Это способ доступа по умолчанию для всех новых контейнеров.
* **Общий доступ на чтение только для больших двоичных объектов.** Большие двоичные объекты в контейнере можно считать с помощью анонимного запроса, но данные контейнера недоступны. Анонимные клиенты не могут перечислять большие двоичные объекты внутри контейнера.
* **Полный открытый доступ на чтение.** Все данные контейнера и больших двоичных объектов можно считать с помощью анонимного запроса. Клиенты могут перечислять большие двоичные объекты внутри контейнера с помощью анонимного запроса, но не могут перечислять контейнеры в учетной записи хранения.

Чтобы установить разрешения для контейнера, можно использовать:

* [портал Azure](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure CLI 2.0](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* программный метод с помощью одной из клиентских библиотек службы хранилища или REST API.

### <a name="set-container-permissions-in-the-azure-portal"></a>Настройка разрешений контейнера на портале Azure
Чтобы задать разрешения контейнера на [портале Azure](https://portal.azure.com), выполните следующее.

1. Откройте колонку **Учетная запись хранения** на портале. Чтобы найти свою учетную запись хранения, выберите **Учетные записи хранения** в основной колонке меню портала.
1. В разделе **Служба BLOB-объектов** колонки меню выберите **Контейнеры**.
1. Щелкните правой кнопкой мыши строку контейнера или щелкните многоточие, чтобы открыть **контекстное меню** контейнера.
1. Выберите **Политика доступа** в контекстном меню.
1. Из раскрывающегося списка выберите **Тип доступа**.

    ![Диалоговое окно «Изменение метаданных контейнера»](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="set-container-permissions-with-net"></a>Назначение разрешений контейнера с помощью .NET
Чтобы задать разрешения для контейнера с помощью C# и клиентской библиотеки службы хранилища для .NET, сначала нужно получить существующие разрешения контейнера, вызвав метод **GetPermissions**. Затем задайте свойство **PublicAccess** для объекта **BlobContainerPermissions**, который возвращается методом **GetPermissions**. Наконец, вызовите метод **SetPermissions** с обновленными разрешениями.

В следующем примере задаются разрешения контейнера на полный общий доступ на чтение. Чтобы задать общий доступ на чтение только для BLOB-объектов, задайте для свойства **PublicAccess** значение **BlobContainerPublicAccessType.Blob**. Чтобы удалить все разрешения для анонимных пользователей, задайте для этого свойства значение **BlobContainerPublicAccessType.Off**.

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Анонимный доступ к контейнерам и большим двоичным объектам
Клиент, который обращается к контейнерам и большим двоичным объектам анонимно, может использовать конструкторы, не требующие учетных данных. В следующих примерах показано несколько разных способов анонимной ссылки на ресурсы службы BLOB-объектов.

### <a name="create-an-anonymous-client-object"></a>Создание анонимного клиентского объекта
Можно создать новый клиентский объект службы для анонимного доступа, указав конечную точку службы DLOB-объектов для соответствующей учетной записи. Тем не менее необходимо также знать имя контейнера в этой учетной записи, которая доступна для анонимного доступа.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob service endpoint.
    CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>Анонимная ссылка на контейнер
При наличии URL-адреса контейнера, который доступен анонимно, можно воспользоваться им для прямой ссылки на контейнер.

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

### <a name="reference-a-blob-anonymously"></a>Анонимная ссылка на большой двоичный объект
Если имеется URL-адрес большого двоичного объекта, который доступен для анонимного доступа, можно создать прямую ссылку на этот большой двоичный объект, используя URL-адрес.

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>Функции, доступные анонимным пользователям
В следующей таблице приведены операции, которые могут быть вызваны анонимными пользователями, если в списке управления доступом к контейнеру разрешен общий доступ.

| Операция REST | Разрешение на полный общий доступ для чтения | Разрешение на общий доступ для чтения только больших двоичных объектов |
| --- | --- | --- |
| Перечисление контейнеров |Только владелец |Только владелец |
| Create Container (Создание контейнера) |Только владелец |Только владелец |
| Get Container Properties (Получение свойств контейнера) |Все |Только владелец |
| Get Container Metadata (Получение метаданных контейнера) |Все |Только владелец |
| Set Container Metadata (Определение метаданных контейнера) |Только владелец |Только владелец |
| Get Container ACL (Получение списка управления доступом для контейнера) |Только владелец |Только владелец |
| Set Container ACL (Задание списка управления доступом для контейнера) |Только владелец |Только владелец |
| Delete Container (Удаление контейнера) |Только владелец |Только владелец |
| List Blobs (Отображение списка BLOB-объектов) |Все |Только владелец |
| Put BLOB (Вставка BLOB-объекта) |Только владелец |Только владелец |
| Get BLOB (Получение BLOB-объекта) |Все |Все |
| Get BLOB Properties (Получение свойств BLOB-объекта) |Все |Все |
| Set BLOB Properties (Задание свойств службы BLOB-объекта) |Только владелец |Только владелец |
| Get BLOB Metadata (Получение метаданных BLOB-объекта) |Все |Все |
| Set BLOB Metadata (Задание метаданных BLOB-объекта) |Только владелец |Только владелец |
| Put Block (Вставка блокировки) |Только владелец |Только владелец |
| Получение списка блоков (только зафиксированные блоки) |Все |Все |
| Получение списка блоков (только незафиксированные блоки или все блоки) |Только владелец |Только владелец |
| Put Block List (Вставка списка блокировки) |Только владелец |Только владелец |
| Delete BLOB (Удаление BLOB-объекта) |Только владелец |Только владелец |
| Копирование BLOB-объекта |Только владелец |Только владелец |
| Создание моментального снимка большого двоичного объекта |Только владелец |Только владелец |
| Lease Blob (Аренда BLOB-объекта) |Только владелец |Только владелец |
| Put Page (Вставка страницы) |Только владелец |Только владелец |
| Get Page Ranges (Получение диапазона страницы) |Все |Все |
| Добавление больших двоичных объектов |Только владелец |Только владелец |

## <a name="next-steps"></a>Дополнительная информация

* [Проверка подлинности для служб хранилища Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* [Использование подписанных URL-адресов (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Делегирование доступа с помощью подписанного URL-адреса](https://msdn.microsoft.com/library/azure/ee395415.aspx)
