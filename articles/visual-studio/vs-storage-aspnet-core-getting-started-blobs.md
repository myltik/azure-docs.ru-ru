---
title: "Начало работы с BLOB-объектов Azure и Visual Studio подключенные службы (ASP.NET Core) | Документы Microsoft"
description: "Начало работы с помощью хранилища BLOB-объектов Azure в проекте ASP.NET Core в Visual Studio после подключения к учетной записи хранилища с помощью Visual Studio подключенные службы"
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
ms.openlocfilehash: 889afa471eeb556662cddf8eb383a905f08b1f01
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Начало работы с BLOB-объектов Azure и Visual Studio подключенные службы (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Хранилище BLOB-объектов Azure — это служба, которая хранит неструктурированные данные в облаке в качестве объектов или больших двоичных объектов. В хранилище BLOB-объектов могут храниться текстовые или двоичные данные любого типа, например документы, файлы мультимедиа или установщики приложений. Хранилище BLOB-объектов иногда также называют хранилищем объектов.

Этот учебник показывает способы написания кода ASP.NET Core для некоторых типичных сценариев, с помощью хранилища BLOB-объектов Azure. В частности, здесь рассматриваются такие сценарии, как создание контейнера больших двоичных объектов, а также скачивание, получение списка, скачивание и удаление больших двоичных объектов.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Технические условия

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

## <a name="set-up-the-development-environment"></a>Настройка среды разработки

В этом разделе описывается настройка среды разработки, включая создание приложения ASP.NET MVC, Добавление подключения подключенные службы, добавление контроллера и Указание директивы требуемое пространство имен.

### <a name="create-an-aspnet-mvc-app-project"></a>Создание проекта приложения ASP.NET MVC

1. Откройте Visual Studio.

1. В главном меню последовательно выберите **Файл -> Создать -> Проект**.

1. В диалоговом окне **Создание проекта** задайте параметры, как показано на следующем рисунке.

    ![Создание проекта ASP.NET Core](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. Нажмите кнопку **ОК**.

1. В диалоговом окне **Новый проект ASP.NET** задайте параметры, как показано на следующем рисунке.

    ![Указание параметров для проекта MVC](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

1. Нажмите кнопку **ОК**.

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Подключение к учетной записи хранения Azure с помощью подключенных служб

1. В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите в контекстном меню **Добавить > Подключенная служба**.

1. На **добавление подключенной службы** диалогового окна выберите **облачного хранилища с хранилищем Azure**, а затем выберите **Настройка**.

    ![Диалоговое окно подключенной службы](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. На **хранилища Azure** диалогового окна выберите учетную запись хранилища Azure, используемый для этого учебника.  Чтобы создать новую учетную запись хранилища Azure, щелкните **создать новую учетную запись хранения** и заполните форму.  После выбора существующей учетной записи хранения или создавать новый, нажмите кнопку **добавить**.  Visual Studio установит пакет NuGet для хранилища Azure и строки подключения хранилища для **appsettings.json**.

> [!TIP]
> Чтобы узнать, как создать учетную запись хранилища с [портал Azure](https://portal.azure.com), в разделе [создать учетную запись хранилища](../storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> Учетная запись хранилища Azure можно также создать с помощью [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [Azure CLI](../storage/common/storage-azure-cli.md), или [оболочки облако Azure](../cloud-shell/overview.md).


### <a name="create-an-mvc-controller"></a>Создание контроллера MVC 

1. В **обозревателе решений** щелкните правой кнопкой мыши **Контроллеры** и в контекстном меню выберите **Добавить -> Контроллер**.

    ![Добавление контроллера приложения ASP.NET Core MVC](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. На **Добавление формирования шаблонов** диалогового окна выберите **контроллер MVC - пустой**и выберите **добавить**.

    ![Выбор типа контроллера MVC](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. В диалоговом окне **Добавление контроллера** назовите контроллер *BlobsController* и нажмите кнопку **Добавить**.

    ![Назначение имени для контроллера MVC](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Добавьте в файл `BlobsController.cs` следующие директивы *using*:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Подключитесь к учетной записи хранилища и получить ссылку на контейнер

Контейнер больших двоичных объектов — это вложенная иерархия больших двоичных объектов и папок.  Остальные шаги в этом документе требуют наличия ссылки на контейнер больших двоичных объектов, чтобы код следует разместить собственного метода для многократного использования.

Следующие шаги создания метода для подключения к учетной записи хранилища с помощью строки подключения в **appsettings.json** и создать ссылку на контейнер.  Строку подключения в **appsettings.json** будет иметь имя в формате `<storageaccountname>_AzureStorageConnectionString`. 

1. Откройте файл `BlobsController.cs` .

1. Добавьте метод с именем **GetCloudBlobContainer** , возвращающий **CloudBlobContainer**.  Обязательно замените `<storageaccountname>_AzureStorageConnectionString` на фактическое имя ключа в **Web.config**.
    
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

1. Найдите неупорядоченный список, который выглядит следующим образом: `<ul class="nav navbar-nav">`.  После последнего `<li>` элемент в списке, добавьте следующий код HTML, чтобы добавить другой элемент меню навигации:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Запустите приложение и выберите **Create Blob Container** (Создать контейнер больших двоичных объектов), чтобы увидеть результаты, как на снимке экрана ниже.
  
    ![Создание контейнера больших двоичных объектов](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

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

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последнего `<li>` элемент в списке, добавьте следующий код HTML, чтобы добавить другой элемент меню навигации:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Запустите приложение и выберите **Передать BLOB-объект**.  Слово «success!» отображать.
    
    ![Проверка успешности](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
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
   
1. Чтобы получить список больших двоичных объектов в контейнере больших двоичных объектов, `CloudBlobContainer.ListBlobsSegmentedAsync` метод. `CloudBlobContainer.ListBlobsSegmentedAsync` Возвращает `BlobResultSegment` , содержащий `IListBlobItem` объектов, которые могут быть приведены к `CloudBlockBlob`, `CloudPageBlob`, или `CloudBlobDirectory` объектов. В следующем фрагменте кода перечисляются все большие двоичные объекты в контейнере. Каждый BLOB-объект приведен к соответствующему объекту в зависимости от его типа и его имя (или URI в случае использования `CloudBlobDirectory`) добавляется в список.

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

1. После последнего `<li>` элемент в списке, добавьте следующий код HTML, чтобы добавить другой элемент меню навигации:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Запустите приложение и выберите **List Blobs** (Вывод списка больших двоичных объектов	), чтобы увидеть результаты, как на снимке экрана ниже.
  
    ![Получение списка больших двоичных объектов](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

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

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последнего `<li>` элемент в списке, добавьте следующий код HTML, чтобы добавить другой элемент меню навигации:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
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

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последнего `<li>` элемент в списке, добавьте следующий код HTML, чтобы добавить другой элемент меню навигации:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Запустите приложение и выберите **удаления большого двоичного объекта** удалить большой двоичный объект, указанный в `CloudBlobContainer.GetBlockBlobReference` вызова метода.  Текст «success!» должны появиться в браузере.  Нажмите кнопку браузера **обратно** , а затем выберите **перечисление больших двоичных объектов** для проверки, большой двоичный объект больше не находится в контейнере.

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы узнали, как для хранения, список и извлекать большие двоичные объекты в хранилище Azure с помощью ASP.NET Core.  Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.

  * [Начало работы с хранилищем таблиц Azure и подключенными службами Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Начало работы с хранилищем очередей Azure и подключенными службами Visual Studio](vs-storage-aspnet-getting-started-queues.md)
