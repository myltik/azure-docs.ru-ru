---
title: "Приступая к работе с Azure BLOB-объекта хранилища и Visual Studio подключенные службы (ASP.NET Core) | Документы Microsoft"
description: "Начало работы с помощью хранилища больших двоичных объектов Azure в проекте ASP.NET Core в Visual Studio после подключения к учетной записи хранилища с помощью Visual Studio подключенные службы"
services: storage
documentationcenter: 
author: camsoper
manager: wpickett
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: casoper
ms.openlocfilehash: 42390effd6a2d2a8afe9350e0a77d3c0a17b6129
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2018
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Приступая к работе с хранилищем BLOB-объектов Azure и подключенными службами Visual Studio (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Хранилище больших двоичных объектов Azure — это служба, хранит неструктурированные данные в облаке как объектов или больших двоичных объектов. В хранилище BLOB-объектов могут храниться текстовые или двоичные данные любого типа, например документы, файлы мультимедиа или установщики приложений. Хранилище BLOB-объектов иногда также называют хранилищем объектов.

Этот учебник показывает, как писать код ASP.NET Core для некоторых типичных сценариев, используйте хранилище больших двоичных объектов. В частности, здесь рассматриваются такие сценарии, как создание контейнера больших двоичных объектов, а также скачивание, получение списка, скачивание и удаление больших двоичных объектов.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Технические условия

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

## <a name="set-up-the-development-environment"></a>Настройка среды разработки

В этом разделе описывается настройка среды разработки. Это включает создание приложения ASP.NET Model-View-Controller (MVC), Добавление подключения подключенных служб, добавление контроллера и Указание директивы требуемое пространство имен.

### <a name="create-an-aspnet-mvc-app-project"></a>Создание проекта приложения ASP.NET MVC

1. Откройте Visual Studio.

1. В главном меню выберите **файл** > **New** > **проекта**.

1. В **новый проект** выберите **Web** > **веб-приложения ASP.NET Core** > **AspNetCoreStorage**. Нажмите кнопку **ОК**.

    ![Снимок экрана из Visual Studio новый проект-диалоговое окно](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. В **нового веб-приложения ASP.NET Core** выберите **.NET Core** > **ASP.NET Core 2.0** > **(веб-приложения Model-View-Controller)**. Нажмите кнопку **ОК**.

    ![Снимок экрана из новой основной веб-приложение ASP.NET-диалоговое окно](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Использовать для подключения к учетной записи хранилища Azure подключенных служб

1. В **обозревателе решений** щелкните проект правой кнопкой мыши.

2. В контекстном меню выберите **добавить** > **подключенной службы**.

1. В **подключенные службы** установите флажок **облачного хранилища с хранилищем Azure**, а затем выберите **Настройка**.

    ![Снимок экрана подключенные службы-диалоговое окно](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. В **хранилища Azure** установите флажок учетной записи хранилища Azure для использования в этом учебнике. Чтобы создать новую учетную запись хранилища Azure, выберите **создать новую учетную запись хранения**и заполните форму. После выбора существующей учетной записи хранения или создать новую, выберите **добавить**. Visual Studio устанавливает пакет NuGet для хранилища Azure и строки подключения хранилища для **appsettings.json**.

> [!TIP]
> Чтобы узнать, как создать учетную запись хранилища с [портал Azure](https://portal.azure.com), в разделе [создать учетную запись хранилища](../storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> Можно также создать учетную запись хранилища с помощью [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [Azure CLI](../storage/common/storage-azure-cli.md), или [оболочки облако Azure](../cloud-shell/overview.md).


### <a name="create-an-mvc-controller"></a>Создание контроллера MVC 

1. В **обозревателе решений**, щелкните правой кнопкой мыши **контроллеров**.

2. В контекстном меню выберите **добавить** > **контроллера**.

    ![Снимок экрана обозревателя решений](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. В **Добавление формирования шаблонов** выберите **контроллер MVC - пустой**и выберите **добавить**.

    ![Снимок экрана из Добавление формирования шаблонов-диалоговое окно](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. В **добавьте пустой контроллер MVC** диалоговое окно, имя контроллера *BlobsController*и выберите **добавить**.

    ![Снимок экрана для добавления пустой контроллер MVC-диалоговое окно](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Добавьте следующие `using` директивы `BlobsController.cs` файла:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Подключитесь к учетной записи хранилища и получить ссылку на контейнер

Контейнер больших двоичных объектов — это вложенная иерархия больших двоичных объектов и папок. Остальные шаги в этом документе требуют наличия ссылки на контейнер больших двоичных объектов, чтобы код следует разместить в свой собственный метод для многократного использования.

Следующие шаги создания метода для подключения к учетной записи хранилища с помощью строки подключения в **appsettings.json**. Действия также создать ссылку на контейнер. Строку подключения в **appsettings.json** именем в формате `<storageaccountname>_AzureStorageConnectionString`. 

1. Откройте файл `BlobsController.cs` .

1. Добавьте метод с именем **GetCloudBlobContainer** , возвращающий **CloudBlobContainer**. Обязательно замените `<storageaccountname>_AzureStorageConnectionString` на фактическое имя ключа в **Web.config**.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        var builder = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json");
        IConfigurationRoot Configuration = builder.Build();
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.Parse(Configuration["ConnectionStrings:aspnettutorial_AzureStorageConnectionString"]);
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> Несмотря на то что *тест контейнер больших двоичных объектов* не существует, этот код создает ссылку на него. Это может быть создан контейнер с `CreateIfNotExists` метода, который показан на следующем шаге.

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

1. Вызовите `CloudBlobContainer.CreateIfNotExists` для создания контейнера, в том случае, если он еще не существует. `CloudBlobContainer.CreateIfNotExists` Возвращает **true** Если контейнер не существует и успешно создан. В противном случае метод возвращает **false**.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
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
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. В **обозревателе решений**, щелкните правой кнопкой мыши **представления** папки.

2. В контекстном меню выберите **добавить** > **новую папку**. Имя новой папки *большие двоичные объекты*. 

1. В **обозревателе решений**, разверните **представления** папку и щелкните правой кнопкой мыши **большие двоичные объекты**.

4. В контекстном меню выберите **добавить** > **представление**.

1. В **добавить представление** диалогового окна введите **CreateBlobContainer** имя представления, а затем выберите **добавить**.

1. Откройте файл `CreateBlobContainer.cshtml` и измените его таким образом, чтобы он выглядел, как показано ниже.

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>
    
    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. В **обозревателе решений**, разверните **представления** > **Shared** , а откройте `_Layout.cshtml`.

1. Найдите неупорядоченный список, который выглядит следующим образом: `<ul class="nav navbar-nav">`.  После последнего `<li>` элемент в списке, добавьте следующий код HTML, чтобы добавить другой элемент меню навигации:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Запустите приложение и выберите **создать контейнер больших двоичных объектов** чтобы увидеть результаты, аналогичные следующим образом:
  
    ![Снимок экрана создания контейнера больших двоичных объектов](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

    Как упоминалось ранее, `CloudBlobContainer.CreateIfNotExists` возвращает **true** только когда контейнер не существует и будет создан. Таким образом, если приложение выполняется, когда контейнер существует, метод возвращает **false**.

## <a name="upload-a-blob-into-a-blob-container"></a>Отправка большого двоичного объекта в контейнер

Когда [создается контейнер больших двоичных объектов](#create-a-blob-container), отправьте файлы в этом контейнере. В этом разделе описывается передача локального файла в контейнер больших двоичных объектов. Предполагается, что имеется контейнер больших двоичных объектов с именем *тест контейнер больших двоичных объектов*. 

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

1. Хранилище Azure поддерживает типы различных больших двоичных объектов. В этом учебнике используются BLOB-блоки. Чтобы получить ссылку на блочный BLOB-объект, вызовите `CloudBlobContainer.GetBlockBlobReference` метод.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Имя BLOB-объект является частью URL-адрес, используемый для получения большого двоичного объекта и может быть любой строкой, включая имя файла.

1. После ссылку на большой двоичный объект, можно передать любой поток данных в его путем вызова большой двоичный объект ссылки `UploadFromStream` метод. `UploadFromStream` Метод создает BLOB-объекта, если он не существует, или перезаписывает его, если он существует. (Изменение  *&lt;передачу файла >* в полный путь к файлу для отправки.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStreamAsync(fileStream).Wait();
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
            blob.UploadFromStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. В **обозревателе решений**, разверните **представления** > **Shared** , а откройте `_Layout.cshtml`.

1. После последнего `<li>` элемент в списке, добавьте следующий код HTML, чтобы добавить другой элемент меню навигации:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Запустите приложение и выберите **Передать BLOB-объект**. Слово *успех!* должно отображаться.
    
    ![Снимок экрана: успех проверки](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
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
   
1. Чтобы получить список больших двоичных объектов в контейнере больших двоичных объектов, `CloudBlobContainer.ListBlobsSegmentedAsync` метод. `CloudBlobContainer.ListBlobsSegmentedAsync` Возвращает `BlobResultSegment`. Это поле содержит `IListBlobItem` объектов, которые могут быть приведены к `CloudBlockBlob`, `CloudPageBlob`, или `CloudBlobDirectory` объектов. В следующем фрагменте кода перечисляются все большие двоичные объекты в контейнере. Каждый BLOB-объект приведен к соответствующему объекту, в зависимости от его типа. Его имя (или URI в случае использования `CloudBlobDirectory`) добавляется в список.

    ```csharp
    List<string> blobs = new List<string>();
    BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
    foreach (IListBlobItem item in resultSegment.Results)
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
    В следующем примере показаны завершенный `ListBlobs` метод:

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
        foreach (IListBlobItem item in resultSegment.Results)
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

1. В **обозревателе решений**, разверните **представления** папку и щелкните правой кнопкой мыши **большие двоичные объекты**.

2. В контекстном меню выберите **добавить** > **представление**.

1. В **добавить представление** диалогового окна введите `ListBlobs` имя представления, а затем выберите **добавить**.

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

1. В **обозревателе решений**, разверните **представления** > **Shared** , а откройте `_Layout.cshtml`.

1. После последнего `<li>` элемент в списке, добавьте следующий код HTML, чтобы добавить другой элемент меню навигации:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Запустите приложение и выберите **перечисление больших двоичных объектов** чтобы увидеть результаты, аналогичные следующим образом:
  
    ![Снимок экрана перечисление больших двоичных объектов](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Скачивание больших двоичных объектов

В этом разделе показано, как загрузить большой двоичный объект. Можно сохранить его в локальное хранилище или считывать содержимое в строку. Примеры кода приведены для контейнера *test-blob-container*, созданного при работе с разделом [Создание контейнера больших двоичных объектов](#create-a-blob-container).

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

1. Чтобы загрузить большой двоичный объект, используйте `CloudBlockBlob.DownloadToStream` метод. Следующий код передает содержимое большого двоичного объекта в объект потока. Этот объект сохраняется в локальном файле. (Изменение  *&lt;локального файла >* для имени полным путем к файлу, представляющий где большого двоичного объекта для загрузки.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStreamAsync(fileStream).Wait();
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
            blob.DownloadToStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. В **обозревателе решений**, разверните **представления** > **Shared** , а откройте `_Layout.cshtml`.

1. После последнего `<li>` элемент в списке, добавьте следующий код HTML, чтобы добавить другой элемент меню навигации:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Запустите приложение и выберите **Скачать BLOB-объект**. Большой двоичный объект, указанный в `CloudBlobContainer.GetBlockBlobReference` загружает местоположения, указанного в вызове метода `File.OpenWrite` вызова метода. Текст *успех!* должны появиться в браузере. 

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
    blob.DeleteAsync().Wait();
    ```
    
    Завершенный `DeleteBlob` метод должен выглядеть следующим образом:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. В **обозревателе решений**, разверните **представления** > **Shared** , а откройте `_Layout.cshtml`.

1. После последнего `<li>` элемент в списке, добавьте следующий код HTML, чтобы добавить другой элемент меню навигации:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Запустите приложение и выберите **удаления большого двоичного объекта** удалить большой двоичный объект, указанный в `CloudBlobContainer.GetBlockBlobReference` вызова метода. Текст *успех!* должны появиться в браузере. Выберите браузер **обратно** и затем выберите **перечисление больших двоичных объектов** для проверки, больше не является большой двоичный объект в контейнере.

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы узнали, как для хранения, список и извлекать большие двоичные объекты в хранилище Azure с помощью ASP.NET Core. Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.

  * [Приступая к работе с Azure таблицы хранилища и Visual Studio подключенные службы (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Начало работы с очередью Azure хранилища и Visual Studio подключенные службы (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
