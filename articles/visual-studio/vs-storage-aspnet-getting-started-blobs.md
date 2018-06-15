---
title: Приступая к работе с хранилищем BLOB-объектов Azure и подключенными службами Visual Studio (ASP.NET) | Документация Майкрософт
description: Как приступить к работе, используя хранилище BLOB-объектов Azure в проекте ASP.NET в Visual Studio после подключения к учетной записи хранения с помощью подключенных служб Visual Studio.
services: storage
author: ghogen
manager: douge
ms.assetid: b3497055-bef8-4c95-8567-181556b50d95
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: ghogen
ms.openlocfilehash: 55b083ac5384e749098338d2f3b7b24bfe16fe8f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31799555"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Приступая к работе с хранилищем BLOB-объектов Azure и подключенными службами Visual Studio (ASP.NET)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Хранилище BLOB-объектов Azure — это служба, которая хранит неструктурированные данные в облаке в качестве объектов или больших двоичных объектов. В хранилище BLOB-объектов могут храниться текстовые или двоичные данные любого типа, например документы, файлы мультимедиа или установщики приложений. Хранилище BLOB-объектов иногда также называют хранилищем объектов.

В этом руководстве показано, как написать код ASP.NET для некоторых распространенных сценариев с использованием хранилища BLOB-объектов. В частности, здесь рассматриваются такие сценарии, как создание контейнера больших двоичных объектов, а также скачивание, получение списка, скачивание и удаление больших двоичных объектов.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>предварительным требованиям

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]


[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

## <a name="create-an-mvc-controller"></a>Создание контроллера MVC 

1. В **обозревателе решений** щелкните правой кнопкой мыши **Контроллеры**.

2. В контекстном меню выберите **Добавить** > **Контроллер**.

    ![Снимок экрана обозревателя решений, в котором выделены пункты "Добавить" и "Контроллер"](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. В диалоговом окне **Добавление шаблона** выберите **Контроллер MVC 5 — пустой** и нажмите кнопку **Добавить**.

    ![Снимок экрана с диалоговым окном "Добавление шаблона"](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. В диалоговом окне **Добавление контроллера** назовите контроллер *BlobsController* и нажмите кнопку **Добавить**.

    ![Снимок экрана с диалоговым окном "Добавление контроллера"](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Добавьте в файл `BlobsController.cs` следующие директивы `using`.

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Подключение к учетной записи хранения и получение ссылки на контейнер

Контейнер больших двоичных объектов — это вложенная иерархия больших двоичных объектов и папок. Остальные шаги в этом документе требуют наличия ссылки на контейнер больших двоичных объектов, поэтому этот код следует разместить в собственном методе, чтобы обеспечить многократное использование.

Ниже приведены инструкции по созданию метода для подключения к учетной записи хранения с помощью строки подключения в файле **Web.config**. Кроме того, создается ссылка на контейнер.  Параметр строки подключения в **Web.config** имеет имя в формате `<storageaccountname>_AzureStorageConnectionString`. 

1. Откройте файл `BlobsController.cs` .

1. Добавьте метод **GetCloudBlobContainer**, возвращающий **CloudBlobContainer**.  Обязательно замените `<storageaccountname>_AzureStorageConnectionString` фактическим именем ключа в файле **Web.config**.
    
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
> Хотя контейнер *test-blob-container* пока не существует, этот код создает ссылку на него. Это позволит создать контейнер с помощью метода `CreateIfNotExists`, который показан на следующем шаге.

## <a name="create-a-blob-container"></a>Создание контейнера BLOB-объектов

Чтобы создать контейнер больших двоичных объектов, выполните следующие действия:

1. Добавьте метод `CreateBlobContainer`, возвращающий `ActionResult`.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Получите объект `CloudBlobContainer`, который представляет ссылку на нужное имя контейнера больших двоичных объектов. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Вызовите метод `CloudBlobContainer.CreateIfNotExists` для создания контейнера, если он еще не существует. Метод `CloudBlobContainer.CreateIfNotExists` возвращает значение **true**, если контейнер не существует и если он успешно создан. В противном случае метод возвращает значение **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. Обновите `ViewBag`, указав имя контейнера больших двоичных объектов.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Ниже приведен готовый метод `CreateBlobContainer`.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExists();
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. В **обозревателе решений** щелкните правой кнопкой мыши папку **Представления**.

2. В контекстном меню выберите **Добавить** > **Новая папка**. Назовите новую папку *Blobs*. 
 
1. В **обозревателе решений** разверните папку **Представления** и щелкните правой кнопкой мыши папку **Blobs**.

4. В контекстном меню выберите **Добавить** > **Представление**.

1. В диалоговом окне **Добавление представления** введите **CreateBlobContainer** в качестве имени представления и нажмите кнопку **Добавить**.

1. Откройте файл `CreateBlobContainer.cshtml` и измените его таким образом, чтобы он выглядел, как показано ниже.

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. В **обозревателе решений** разверните папку **Представления** > **Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Запустите приложение и выберите **Создать контейнер BLOB-объектов**, чтобы увидеть результаты, как на снимке экрана ниже.
  
    ![Снимок экрана с диалоговым окном "Создать контейнер BLOB-объектов"](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Как упоминалось ранее, метод `CloudBlobContainer.CreateIfNotExists` возвращает значение **true**, только если контейнер не существовал и был создан. Таким образом, если приложение запустится, когда контейнер существует, этот метод вернет значение **false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Отправка большого двоичного объекта в контейнер

После [создания контейнера больших двоичных объектов](#create-a-blob-container) вы можете передать в него файлы. В этом разделе показано, как отправить локальные файлы в контейнер больших двоичных объектов. В приведенных инструкциях предполагается наличие контейнера больших двоичных объектов *test-blob-container*. 

1. Откройте файл `BlobsController.cs` .

1. Добавьте метод `UploadBlob`, возвращающий строку.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. В методе `UploadBlob` получите объект `CloudBlobContainer`, который представляет ссылку на нужное имя контейнера больших двоичных объектов. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Служба хранилища Azure поддерживает различные типы больших двоичных объектов. В этом учебнике используются BLOB-блоки. Чтобы получить ссылку на блочный BLOB-объект, вызовите метод `CloudBlobContainer.GetBlockBlobReference`.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Имя большого двоичного объекта является частью URL-адреса, используемого для получения большого двоичного объекта, и может быть любой строкой, включая имя файла.

1. Получив ссылку на большой двоичный объект, вы можете передать в него любой поток данных, вызвав метод `UploadFromStream` объекта ссылки на большой двоичный объект. Метод `UploadFromStream` создает большой двоичный объект, если он еще не существует, или заменяет его, если он существует. (Измените *&lt;file-to-upload>* на абсолютный путь к файлу, который вы хотите передать.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStream(fileStream);
    }
    ```
    
    В следующем примере показан готовый метод `UploadBlob` (с абсолютным путем к передаваемому файлу).

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

1. В **обозревателе решений** разверните папку **Представления** > **Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Запустите приложение и выберите **Передать BLOB-объект**.  Должно появиться сообщение *success!* (Успешно выполнено) .
    
    ![Снимок экрана успешной проверки](./media/vs-storage-aspnet-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Получение списка больших двоичных объектов в контейнере

В этом разделе вы узнаете, как получить список больших двоичных объектов в контейнере. Примеры кода приведены для контейнера *test-blob-container*, созданного при работе с разделом [Создание контейнера больших двоичных объектов](#create-a-blob-container).

1. Откройте файл `BlobsController.cs` .

1. Добавьте метод `ListBlobs`, возвращающий `ActionResult`.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. В методе `ListBlobs` получите объект `CloudBlobContainer`, который представляет ссылку на контейнер больших двоичных объектов. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Чтобы вывести список больших двоичных объектов в контейнере, используйте метод `CloudBlobContainer.ListBlobs`. Метод `CloudBlobContainer.ListBlobs` возвращает объект `IListBlobItem`, который может быть приведен к объекту `CloudBlockBlob`, `CloudPageBlob` или `CloudBlobDirectory`. В следующем фрагменте кода перечисляются все большие двоичные объекты в контейнере. Каждый большой двоичный объект приводится к соответствующему объекту в зависимости от типа. Его имя (или универсальный код ресурса (URI) в случае **CloudBlobDirectory**) добавляется в список.

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

    Помимо больших двоичных объектов контейнеры могут также содержать каталоги. Предположим, имеется контейнер больших двоичных объектов *test-blob-container* со следующей иерархией.

        foo.png
        dir1/bar.png
        dir2/baz.png

    Исходя из приведенного выше примера кода, список строк **blobs** содержит значения, аналогичные следующим:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Как видно, список включает в себя сущности только верхнего уровня и не содержит вложенные сущности (*bar.png* и *baz.png*). Чтобы получить список всех сущностей в контейнере больших двоичных объектов, измените код. Необходимо, чтобы метод **CloudBlobContainer.ListBlobs** передавал значение **true** для параметра **useFlatBlobListing**.    

    ```csharp
    //...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    //...
    ```

    Если установить для параметра **useFlatBlobListing** значение **true**, будет возвращен неструктурированный список всех сущностей в контейнере больших двоичных объектов. Мы получаем следующие результаты.

        foo.png
        dir1/bar.png
        dir2/baz.png
    
    Ниже приведен готовый метод **ListBlobs**.

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

1. В **обозревателе решений** разверните папку **Представления** и щелкните правой кнопкой мыши папку **Blobs**.

2. В контекстном меню выберите **Добавить** > **Представление**.

1. В диалоговом окне **Добавление представления** введите `ListBlobs` в качестве имени представления и нажмите кнопку **Добавить**.

1. Откройте файл `ListBlobs.cshtml` и замените его содержимое кодом, приведенным ниже.

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

1. В **обозревателе решений** разверните папку **Представления** > **Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Запустите приложение и выберите **Список BLOB-объектов**, чтобы увидеть результаты, как на снимке экрана ниже.
  
    ![Снимок экрана со списком больших двоичных объектов](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Скачивание больших двоичных объектов

В этом разделе показано, как скачать большой двоичный объект. Его можно сохранить в локальном хранилище или считать содержимое в строку. Примеры кода приведены для контейнера *test-blob-container*, созданного при работе с разделом [Создание контейнера больших двоичных объектов](#create-a-blob-container).

1. Откройте файл `BlobsController.cs` .

1. Добавьте метод `DownloadBlob`, возвращающий строку.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. В методе `DownloadBlob` получите объект `CloudBlobContainer`, который представляет ссылку на контейнер больших двоичных объектов.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Получите объект ссылки большого двоичного объекта, вызвав метод `CloudBlobContainer.GetBlockBlobReference`. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Чтобы скачать большой двоичный объект, используйте метод `CloudBlockBlob.DownloadToStream`. Следующий код передает содержимое большого двоичного объекта в объект потока. Затем этот объект сохраняется в локальном файле. (Измените *&lt;local-file-name>* на полное имя файла, в который будет сохранен скачанный большой двоичный объект.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```
    
    В следующем примере показан готовый метод `ListBlobs` (с абсолютным путем к создаваемому локальному файлу).
    
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

1. В **обозревателе решений** разверните папку **Представления** > **Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Запустите приложение и выберите **Скачать BLOB-объект**. Большой двоичный объект, указанный в вызове метода `CloudBlobContainer.GetBlockBlobReference`, скачивается в расположение, указанное в вызове метода `File.OpenWrite`.  В браузере должно отобразиться сообщение *success!* (Успешно выполнено) . 

## <a name="delete-blobs"></a>Удаление blob-объектов

Чтобы удалить большой двоичный объект, выполните следующие действия:

1. Откройте файл `BlobsController.cs` .

1. Добавьте метод `DeleteBlob`, возвращающий строку.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. В методе `DeleteBlob` получите объект `CloudBlobContainer`, который представляет ссылку на контейнер больших двоичных объектов.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Получите объект ссылки большого двоичного объекта, вызвав метод `CloudBlobContainer.GetBlockBlobReference`. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Чтобы удалить большой двоичный объект, используйте метод `Delete`.

    ```csharp
    blob.Delete();
    ```
    
    Готовый метод `DeleteBlob` должен выглядеть следующим образом.
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.Delete();
        return "success!";
    }
    ```

1. В **обозревателе решений** разверните папку **Представления** > **Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Запустите приложение и выберите **Удалить большой двоичный объект**, чтобы удалить большой двоичный объект, указанный в вызове метода `CloudBlobContainer.GetBlockBlobReference`. В браузере должно отобразиться сообщение *success!* (Успешно выполнено) . Нажмите кнопку **Назад** в браузере и выберите **Список BLOB-объектов**, чтобы удостовериться, что большой двоичный объект отсутствует в контейнере.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы узнали, как сохранять и извлекать большие двоичные объекты, а также выводить их список в службе хранилища Azure с помощью ASP.NET. Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.

  * [Начало работы с хранилищем таблиц Azure и подключенными службами Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Начало работы с хранилищем очередей Azure и подключенными службами Visual Studio](vs-storage-aspnet-getting-started-queues.md)
