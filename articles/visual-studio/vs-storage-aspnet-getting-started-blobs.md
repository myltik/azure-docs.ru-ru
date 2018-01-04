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
ms.date: 12/07/2017
ms.author: kraig
ms.openlocfilehash: f0d0815e71149749cb52efe21e1f0af3cabae21c
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Приступая к работе с хранилищем BLOB-объектов Azure и подключенными службами Visual Studio (ASP.NET)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Хранилище BLOB-объектов Azure — это служба, которая хранит неструктурированные данные в облаке в качестве объектов или больших двоичных объектов. В хранилище BLOB-объектов могут храниться текстовые или двоичные данные любого типа, например документы, файлы мультимедиа или установщики приложений. Хранилище BLOB-объектов иногда также называют хранилищем объектов.

В этом руководстве показано, как написать код ASP.NET для некоторых распространенных сценариев с использованием хранилища BLOB-объектов Azure. В частности, здесь рассматриваются такие сценарии, как создание контейнера больших двоичных объектов, а также скачивание, получение списка, скачивание и удаление больших двоичных объектов.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Технические условия

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]


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
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Подключитесь к учетной записи хранилища и получить ссылку на контейнер

Контейнер больших двоичных объектов — это вложенная иерархия больших двоичных объектов и папок.  Остальные шаги в этом документе требуют наличия ссылки на контейнер больших двоичных объектов, чтобы код следует разместить собственного метода для многократного использования.

Следующие шаги создания метода для подключения к учетной записи хранилища с помощью строки подключения в **Web.config** и создать ссылку на контейнер.  Строку подключения в **Web.config** будет иметь имя в формате `<storageaccountname>_AzureStorageConnectionString`. 

1. Откройте файл `BlobsController.cs` .

1. Добавьте метод с именем **GetCloudBlobContainer** , возвращающий **CloudBlobContainer**.  Обязательно замените `<storageaccountname>_AzureStorageConnectionString` на фактическое имя ключа в **Web.config**.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
                CloudConfigurationManager.GetSetting("<storageaccountname>_AzureStorageConnectionString"));
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> Несмотря на то что *тест контейнер больших двоичных объектов* не существует, этот код создает ссылку на него, чтобы контейнер можно создать с помощью `CreateIfNotExists` метода, который показан на следующем шаге.

## <a name="create-a-blob-container"></a>Создание контейнера BLOB-объектов

Чтобы создать контейнер больших двоичных объектов, выполните следующие действия:

1. Добавьте метод с именем `CreateBlobContainer` , возвращающий `ActionResult`.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Получить `CloudBlobContainer` , представляющий ссылку на имя нужного большого двоичного объекта контейнера. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Вызовите `CloudBlobContainer.CreateIfNotExists` для создания контейнера, если он еще не существует. `CloudBlobContainer.CreateIfNotExists` Возвращает **true** Если контейнер не существует и успешно создан. В противном случае возвращается значение **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Обновление `ViewBag` с именем контейнера BLOB-объектов.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    В следующем примере показаны завершенный `CreateBlobContainer` метод:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExists();
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. В **обозревателе решений**, щелкните правой кнопкой мыши **представления** папки и в контекстном меню выберите **Добавить -> Новая папка**. Имя новой папки *большие двоичные объекты*. 
 
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

    Как упоминалось ранее, `CloudBlobContainer.CreateIfNotExists` возвращает **true** только когда контейнер не существует и будет создан. Таким образом, если приложение выполняется, когда контейнер существует, метод возвращает **false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Отправка большого двоичного объекта в контейнер

Один раз [создается контейнер больших двоичных объектов](#create-a-blob-container), отправьте файлы в этом контейнере. В этом разделе описывается передача локального файла в контейнер больших двоичных объектов. Предполагается, что имеется контейнер больших двоичных объектов с именем *тест контейнер больших двоичных объектов*. 

1. Откройте файл `BlobsController.cs` .

1. Добавьте метод с именем `UploadBlob` , возвращающее строку.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. В пределах `UploadBlob` метод, получить `CloudBlobContainer` , представляющий ссылку на имя нужного большого двоичного объекта контейнера. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Как объяснялось ранее, служба хранилища Azure поддерживает различные типы больших двоичных объектов. В этом учебнике используются BLOB-блоки.  Чтобы получить ссылку на блочный BLOB-объект, вызовите `CloudBlobContainer.GetBlockBlobReference` метод.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Имя BLOB-объект является частью URL-адрес, используемый для получения большого двоичного объекта и может быть любой строкой, включая имя файла.

1. Как только ссылку на большой двоичный объект, передать любого потока данных его путем вызова большой двоичный объект ссылки `UploadFromStream` метод. `UploadFromStream` Метод создает BLOB-объекта, если он не существует, или перезаписывает его, если он существует. (Изменение  *&lt;передачу файла >* в полный путь к файлу для отправки.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStream(fileStream);
    }
    ```
    
    В следующем примере показаны завершенный `UploadBlob` метод (полный путь к загружаемому файлу):

    ```csharp
    public string UploadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenRead(@"c:\src\sample.txt"))
        {
            blob.UploadFromStream(fileStream);
        }
        return "success!";
    }
    ```

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Запустите приложение и выберите **Передать BLOB-объект**.  Слово «success!» отображать.
    
    ![Проверка успешности](./media/vs-storage-aspnet-getting-started-blobs/upload-blob.png)
  
Сведения о том, как получить список больших двоичных объектов в контейнере, см. в разделе [Получение списка больших двоичных объектов в контейнере](#list-the-blobs-in-a-blob-container).    

## <a name="list-the-blobs-in-a-blob-container"></a>Получение списка больших двоичных объектов в контейнере

В этом разделе вы узнаете, как получить список больших двоичных объектов в контейнере. Примеры кода приведены для контейнера *test-blob-container*, созданного при работе с разделом [Создание контейнера больших двоичных объектов](#create-a-blob-container).

1. Откройте файл `BlobsController.cs` .

1. Добавьте метод с именем `ListBlobs` , возвращающий `ActionResult`.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. В пределах `ListBlobs` метод, получить `CloudBlobContainer` , представляющий ссылку на контейнер больших двоичных объектов. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Чтобы получить список больших двоичных объектов в контейнере больших двоичных объектов, `CloudBlobContainer.ListBlobs` метод. `CloudBlobContainer.ListBlobs` Возвращает `IListBlobItem` объект, который может быть приведен к `CloudBlockBlob`, `CloudPageBlob`, или `CloudBlobDirectory` объекта. В следующем фрагменте кода перечисляются все большие двоичные объекты в контейнере. Каждый большой двоичный объект преобразовывается в соответствующий объект в зависимости от его типа, и в список добавляется его имя (или универсальный код ресурса (URI), если это объект **CloudBlobDirectory**).

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs())
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

    Помимо больших двоичных объектов контейнеры могут также содержать каталоги. Допустим, существует контейнер BLOB-объекта с именем *тест контейнер больших двоичных объектов* с следующая иерархия:

        foo.png
        dir1/bar.png
        dir2/baz.png

    Исходя из приведенного выше примера кода, список строк **blobs** содержит значения, аналогичные следующим:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Как показано, список содержит только верхнего уровня сущности; не вложенные те (*bar.png* и *baz.png*). Чтобы вывести список всех сущностей в контейнер больших двоичных объектов, измените код, чтобы **CloudBlobContainer.ListBlobs** методу передается **true** для **useFlatBlobListing** параметр.    

    ```csharp
    //...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    //...
    ```

    Если установить для параметра **useFlatBlobListing** значение **true**, будет возвращен неструктурированный список всех сущностей в контейнере:

        foo.png
        dir1/bar.png
        dir2/baz.png
    
    В следующем примере показаны завершенный **ListBlobs** метод:

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing: true))
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
    }
    ```

1. В **обозревателе решений** разверните папку **Представления**, щелкните правой кнопкой мыши **BLOB-объекты**, а затем в контекстном меню выберите **Добавить -> Представление**.

1. На **добавить представление** диалоговое окно, введите `ListBlobs` имя представления, а затем выберите **добавить**.

1. Откройте `ListBlobs.cshtml`и замените содержимое следующим кодом:

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

1. Добавьте метод с именем `DownloadBlob` , возвращающее строку.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. В пределах `DownloadBlob` метод, получить `CloudBlobContainer` , представляющий ссылку на контейнер больших двоичных объектов.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Получение большого двоичного объекта ссылочного объекта путем вызова `CloudBlobContainer.GetBlockBlobReference` метод. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Чтобы загрузить большой двоичный объект, используйте `CloudBlockBlob.DownloadToStream` метод. Следующий код передает содержимое большого двоичного объекта в объект потока, который сохраняется в локальном файле (изменение  *&lt;локального файла >* для имени полным путем к файлу, представляющий где большого двоичного объекта для загрузки.): 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```
    
    В следующем примере показаны завершенный `ListBlobs` метод (полный путь для создаваемого файла локального):
    
    ```csharp
    public string DownloadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenWrite(@"c:\src\downloadedBlob.txt"))
        {
            blob.DownloadToStream(fileStream);
        }
        return "success!";
    }
    ```

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Запустите приложение и выберите **Скачать BLOB-объект**. Большой двоичный объект, указанный в `CloudBlobContainer.GetBlockBlobReference` загружает местоположения, указанного в вызове метода `File.OpenWrite` вызова метода.  Текст «success!» должны отображаться в браузере. 

## <a name="delete-blobs"></a>Удаление blob-объектов

Чтобы удалить большой двоичный объект, выполните следующие действия:

1. Откройте файл `BlobsController.cs` .

1. Добавьте метод с именем `DeleteBlob` , возвращающее строку.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. В пределах `DeleteBlob` метод, получить `CloudBlobContainer` , представляющий ссылку на контейнер больших двоичных объектов.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Получение большого двоичного объекта ссылочного объекта путем вызова `CloudBlobContainer.GetBlockBlobReference` метод. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Чтобы удалить большой двоичный объект, используйте `Delete` метод.

    ```csharp
    blob.Delete();
    ```
    
    Завершенный `DeleteBlob` метод должен выглядеть следующим образом:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.Delete();
        return "success!";
    }
    ```

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Запустите приложение и выберите **удаления большого двоичного объекта** удалить большой двоичный объект, указанный в `CloudBlobContainer.GetBlockBlobReference` вызова метода.  Текст «success!» должны появиться в браузере.  Нажмите кнопку браузера **обратно** , а затем выберите **перечисление больших двоичных объектов** для проверки, большой двоичный объект больше не находится в контейнере.

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы узнали, как для хранения, список и извлекать большие двоичные объекты в хранилище Azure с помощью ASP.NET.  Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.

  * [Начало работы с хранилищем таблиц Azure и подключенными службами Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Начало работы с хранилищем очередей Azure и подключенными службами Visual Studio](vs-storage-aspnet-getting-started-queues.md)
