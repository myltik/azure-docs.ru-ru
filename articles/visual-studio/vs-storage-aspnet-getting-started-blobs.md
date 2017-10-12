---
title: "Приступая к работе с хранилищем BLOB-объектов Azure и подключенными службами Visual Studio (ASP.NET) | Документация Майкрософт"
description: "Как приступить к работе, используя хранилище BLOB-объектов Azure в проекте ASP.NET в Visual Studio после подключения к учетной записи хранения с помощью подключенных служб Visual Studio."
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: b3497055-bef8-4c95-8567-181556b50d95
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: kraig
ms.openlocfilehash: e953c7978705379a28581213e8f1c665473ddd60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Приступая к работе с хранилищем BLOB-объектов Azure и подключенными службами Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Обзор

Хранилище BLOB-объектов Azure — это служба, которая хранит неструктурированные данные в облаке в качестве объектов или больших двоичных объектов. В хранилище BLOB-объектов могут храниться текстовые или двоичные данные любого типа, например документы, файлы мультимедиа или установщики приложений. Хранилище BLOB-объектов иногда также называют хранилищем объектов.

В этом руководстве показано, как написать код ASP.NET для некоторых распространенных сценариев с использованием хранилища BLOB-объектов Azure. В частности, здесь рассматриваются такие сценарии, как создание контейнера больших двоичных объектов, а также скачивание, получение списка, скачивание и удаление больших двоичных объектов.

##<a name="prerequisites"></a>Предварительные требования

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Учетная запись хранения Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Создание контроллера MVC 

1. В **обозревателе решений** щелкните правой кнопкой мыши **Контроллеры** и в контекстном меню выберите **Добавить -> Контроллер**.

    ![Добавление контроллера в приложение ASP.NET MVC](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. В диалоговом окне **Добавление шаблона** выберите **Контроллер MVC5 — пустой** и нажмите кнопку **Добавить**.

    ![Выбор типа контроллера MVC](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. В диалоговом окне **Добавление контроллера** назовите контроллер *BlobsController* и нажмите кнопку **Добавить**.

    ![Назначение имени для контроллера MVC](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Добавьте в файл `BlobsController.cs` следующие директивы *using*:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="create-a-blob-container"></a>Создание контейнера BLOB-объектов

Контейнер больших двоичных объектов — это вложенная иерархия больших двоичных объектов и папок. Чтобы создать контейнер больших двоичных объектов, выполните следующие действия:

> [!NOTE]
> 
> Прежде чем работать с кодом в этом разделе, выполните все действия, описанные в разделе [Настройка среды разработки](#set-up-the-development-environment). 

1. Откройте файл `BlobsController.cs` .

1. Добавьте метод **CreateBlobContainer**, который возвращает **ActionResult**.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. В рамках метода **CreateBlobContainer** получите объект **CloudStorageAccount**, представляющий сведения об учетной записи хранения. Используйте следующий код, чтобы получить строку подключения и сведения об учетной записи хранения из конфигурации службы Azure. (Замените *&lt;storage-account-name>* на имя учетной записи хранения Azure, к которой осуществляется доступ.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Получите объект **CloudBlobClient**, который представляет клиента службы BLOB-объектов.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Получите объект **CloudBlobContainer**, который представляет ссылку на нужное имя контейнера больших двоичных объектов. Метод **CloudBlobClient.GetContainerReference** не выполняет запрос в отношении хранилища BLOB-объектов. Ссылка возвращается вне зависимости от того, существует ли контейнер больших двоичных объектов. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Вызовите метод **CloudBlobContainer.CreateIfNotExists**, чтобы создать контейнер, если он еще не существует. Метод **CloudBlobContainer.CreateIfNotExists** возвращает значение **true**, если контейнер не существует и если он успешно создан. В противном случае возвращается значение **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Обновите **ViewBag** именем контейнера больших двоичных объектов.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```

1. В **обозревателе решений** разверните папку **Представления**, щелкните правой кнопкой мыши **BLOB-объекты**, а затем в контекстном меню выберите **Добавить -> Представление**.

1. В диалоговом окне **Добавление представления** введите **CreateBlobContainer** для имени представления и выберите **Добавить**.

1. Откройте файл `CreateBlobContainer.cshtml` и измените его таким образом, чтобы он выглядел, как показано ниже.

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Запустите приложение и выберите **Create Blob Container** (Создать контейнер больших двоичных объектов), чтобы увидеть результаты, как на снимке экрана ниже.
  
    ![Создание контейнера больших двоичных объектов](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Как упоминалось ранее, метод **CloudBlobContainer.CreateIfNotExists** возвращает значение **true**, только если контейнер не существует или же уже был создан. Таким образом, если вы запустите приложение, когда контейнер существует, метод вернет значение **false**. Чтобы запустить приложение несколько раз, вам нужно удалить контейнер перед повторным запуском. Удалить контейнер можно с помощью метода **CloudBlobContainer.Delete**. Его также можно удалить на [портале Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) или в [обозревателе хранилищ Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="upload-a-blob-into-a-blob-container"></a>Отправка большого двоичного объекта в контейнер

После того как вы создали [контейнер больших двоичных объектов](#create-a-blob-container), вы можете передать в него файлы. В этом разделе показано, как отправить локальные файлы в контейнер больших двоичных объектов. В описанных здесь действиях предполагается, что вы уже создали контейнер больших двоичных объектов с именем *test-blob-container*. 

> [!NOTE]
> 
> Прежде чем работать с кодом в этом разделе, выполните все действия, описанные в разделе [Настройка среды разработки](#set-up-the-development-environment). 

1. Откройте файл `BlobsController.cs` .

1. Добавьте метод **UploadBlob**, который возвращает **EmptyResult**.

    ```csharp
    public EmptyResult UploadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. В рамках метода **CreateBlobContainer** получите объект **CloudStorageAccount**, который представляет сведения об учетной записи хранения. Используйте следующий фрагмент кода, чтобы получить строку подключения к хранилищу и сведения об учетной записи хранения из конфигурации службы Azure. (Измените *&lt;storage-account-name>* на имя учетной записи хранения Azure, к которой вы получаете доступ.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Получите объект **CloudBlobClient**, который представляет клиента службы BLOB-объектов.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Получите объект **CloudBlobContainer**, который представляет ссылку на имя контейнера больших двоичных объектов. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Как объяснялось ранее, служба хранилища Azure поддерживает различные типы больших двоичных объектов. Чтобы получить ссылку на страничный BLOB-объект, используйте метод **CloudBlobContainer.GetPageBlobReference**. Чтобы получить ссылку на блочный BLOB-объект, используйте метод **CloudBlobContainer.GetBlockBlobReference**. В большинстве случаев рекомендуется использовать блочные BLOB-объекты. (Измените <blob-name>* на имя, которое нужно присвоить загруженному большому двоичному объекту.)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. Получив ссылку на большой двоичный объект, вы можете отправить в него любой поток данных, используя метод **UploadFromStream** для объекта ссылки. Метод **UploadFromStream** создает большой двоичный объект, если он еще не существует, или заменяет уже существующий. (Измените *&lt;file-to-upload>* на полный путь к файлу, который вы хотите передать.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(<file-to-upload>))
    {
        blob.UploadFromStream(fileStream);
    }
    ```

1. В **обозревателе решений** разверните папку **Представления**, щелкните правой кнопкой мыши **BLOB-объекты**, а затем в контекстном меню выберите **Добавить -> Представление**.

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Запустите приложение и выберите **Передать BLOB-объект**.  
  
Сведения о том, как получить список больших двоичных объектов в контейнере, см. в разделе [Получение списка больших двоичных объектов в контейнере](#list-the-blobs-in-a-blob-container).    

## <a name="list-the-blobs-in-a-blob-container"></a>Получение списка больших двоичных объектов в контейнере

В этом разделе вы узнаете, как получить список больших двоичных объектов в контейнере. Примеры кода приведены для контейнера *test-blob-container*, созданного при работе с разделом [Создание контейнера больших двоичных объектов](#create-a-blob-container).

> [!NOTE]
> 
> Прежде чем работать с кодом в этом разделе, выполните все действия, описанные в разделе [Настройка среды разработки](#set-up-the-development-environment). 

1. Откройте файл `BlobsController.cs` .

1. Добавьте метод **ListBlobs**, который возвращает **ActionResult**.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. В рамках метода **ListBlobs** получите объект **CloudStorageAccount**, который представляет сведения об учетной записи хранения. Используйте следующий фрагмент кода, чтобы получить строку подключения к хранилищу и сведения об учетной записи хранения из конфигурации службы Azure. (Измените *&lt;storage-account-name>* на имя учетной записи хранения Azure, к которой вы получаете доступ.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Получите объект **CloudBlobClient**, который представляет клиента службы BLOB-объектов.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Получите объект **CloudBlobContainer**, который представляет ссылку на имя контейнера больших двоичных объектов. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Чтобы получить список больших двоичных объектов в контейнере, используйте метод **CloudBlobContainer.ListBlobs**. Метод **CloudBlobContainer.ListBlobs** возвращает объект **IListBlobItem**, который вы можете преобразовать в **CloudBlockBlob**, **CloudPageBlob** или объект **CloudBlobDirectory**. В следующем фрагменте кода перечисляются все большие двоичные объекты в контейнере. Каждый большой двоичный объект преобразовывается в соответствующий объект в зависимости от его типа, и в список добавляется его имя (или универсальный код ресурса (URI), если это объект **CloudBlobDirectory**).

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```

    Помимо больших двоичных объектов контейнеры могут также содержать каталоги. Предположим, у вас есть контейнер больших двоичных объектов с именем *test-blob-container* со следующей иерархией:

        foo.png
        dir1/bar.png
        dir2/baz.png

    Исходя из приведенного выше примера кода, список строк **blobs** содержит значения, аналогичные следующим:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Как видно, список включает сущности только верхнего уровня и не содержит вложенные сущности (*bar.png* и *baz.png*). Чтобы получить список всех сущностей, которые содержит контейнер больших двоичных объектов, нужно вызвать метод **CloudBlobContainer.ListBlobs** и установить значение **true** для параметра **useFlatBlobListing**.    

    ```csharp
    ...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    ...
    ```

    Если установить для параметра **useFlatBlobListing** значение **true**, будет возвращен неструктурированный список всех сущностей в контейнере:

        foo.png
        dir1/bar.png
        dir2/baz.png

1. В **обозревателе решений** разверните папку **Представления**, щелкните правой кнопкой мыши **BLOB-объекты**, а затем в контекстном меню выберите **Добавить -> Представление**.

1. В диалоговом окне **Добавление представления** введите **ListBlobs** для имени представления и выберите **Добавить**.

1. Откройте файл `ListBlobs.cshtml` и измените его таким образом, чтобы он выглядел, как показано ниже.

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
        <li>@item</li>
        }
    </ul>
    ```

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Запустите приложение и выберите **List Blobs** (Вывод списка больших двоичных объектов	), чтобы увидеть результаты, как на снимке экрана ниже.
  
    ![Получение списка больших двоичных объектов](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Скачивание больших двоичных объектов

В этом разделе рассказывается, как скачать большие двоичные объекты и сохранить их в локальном хранилище или преобразовать содержимое в строку. Примеры кода приведены для контейнера *test-blob-container*, созданного при работе с разделом [Создание контейнера больших двоичных объектов](#create-a-blob-container).

1. Откройте файл `BlobsController.cs` .

1. Добавьте метод **DownloadBlob**, который возвращает **ActionResult**.

    ```csharp
    public EmptyResult DownloadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. В рамках метода **DownloadBlob** получите объект **CloudStorageAccount**, который представляет сведения об учетной записи хранения. Используйте следующий фрагмент кода, чтобы получить строку подключения к хранилищу и сведения об учетной записи хранения из конфигурации службы Azure. (Измените *&lt;storage-account-name>* на имя учетной записи хранения Azure, к которой вы получаете доступ.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Получите объект **CloudBlobClient**, который представляет клиента службы BLOB-объектов.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Получите объект **CloudBlobContainer**, который представляет ссылку на имя контейнера больших двоичных объектов. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Получить объект большого двоичного объекта можно, вызвав следующие методы: **CloudBlobContainer.GetBlockBlobReference** или **CloudBlobContainer.GetPageBlobReference**. (Измените *&lt;blob-name>* на имя большого двоичного объекта, который вы скачиваете.)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. Чтобы скачать большой двоичный объект, используйте методы **CloudBlockBlob.DownloadToStream** или **CloudPageBlob.DownloadToStream** в зависимости от типа большого двоичного объекта. В следующем фрагменте кода используется метод **CloudBlockBlob.DownloadToStream**, чтобы передать содержимое большого двоичного объекта в объект потока, который затем сохраняется в локальный файл. (Измените *local-file-name>&lt;* на полное имя файла, куда будет скачан большой двоичный объект.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Запустите приложение и выберите **Скачать BLOB-объект**. Большой двоичный объект, указанный в вызове метода **CloudBlobContainer.GetBlockBlobReference**, скачивается в расположение, которое вы указали в вызове метода **File.OpenWrite**. 

## <a name="delete-blobs"></a>Удаление blob-объектов

Чтобы удалить большой двоичный объект, выполните следующие действия:

> [!NOTE]
> 
> Прежде чем работать с кодом в этом разделе, выполните все действия, описанные в разделе [Настройка среды разработки](#set-up-the-development-environment). 

1. Откройте файл `BlobsController.cs` .

1. Добавьте метод **DeleteBlob**, который возвращает **ActionResult**.

    ```csharp
    public EmptyResult DeleteBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```

1. Получите объект **CloudStorageAccount** , представляющий данные учетной записи хранения. Используйте следующий фрагмент кода, чтобы получить строку подключения к хранилищу и сведения об учетной записи хранения из конфигурации службы Azure. (Измените *&lt;storage-account-name>* на имя учетной записи хранения Azure, к которой вы получаете доступ.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Получите объект **CloudBlobClient**, который представляет клиента службы BLOB-объектов.
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. Получите объект **CloudBlobContainer**, который представляет ссылку на имя контейнера больших двоичных объектов. 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. Получить объект большого двоичного объекта можно, вызвав следующие методы: **CloudBlobContainer.GetBlockBlobReference** или **CloudBlobContainer.GetPageBlobReference**. (Измените *&lt;blob-name>* на имя большого двоичного объекта, который вы удаляете.)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
        ```

1. To delete a blob, use the **Delete** method.

    ```csharp
    blob.Delete();
    ```

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Запустите приложение и выберите **Удалить большой двоичный объект**, чтобы удалить объект, указанный в вызове метода **CloudBlobContainer.GetBlockBlobReference**. 

## <a name="next-steps"></a>Дальнейшие действия
Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.

  * [Начало работы с хранилищем таблиц Azure и подключенными службами Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Начало работы с хранилищем очередей Azure и подключенными службами Visual Studio](vs-storage-aspnet-getting-started-queues.md)
