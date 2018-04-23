---
title: Приступая к работе с хранилищем очередей Azure и подключенными службами Visual Studio (ASP.NET) | Документация Майкрософт
description: Как приступить к работе, используя хранилище очередей Azure в проекте ASP.NET в Visual Studio после подключения к учетной записи хранения с помощью подключенных служб Visual Studio.
services: storage
author: ghogen
manager: douge
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/23/2016
ms.author: ghogen
ms.openlocfilehash: d06fde0dc6c289a09b9fe4c9e2ffbb50c3027490
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Приступая к работе с хранилищем очередей Azure и подключенными службами Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Обзор

Хранилище очередей Azure — это служба, обеспечивающая обмен сообщениями в облаке между компонентами приложения. При разработке приложений для масштабирования компоненты приложения часто не связаны между собой, так что они могут масштабироваться независимо друг от друга. Хранилище очередей обеспечивает асинхронный обмен сообщениями для взаимодействия между компонентами приложения независимо от того, где они выполняются: в облаке, на рабочем столе, локальном сервере или мобильном устройстве. Хранилище очередей также поддерживает управление асинхронными задачами и создание рабочих процессов.

В этом руководстве показано, как написать код ASP.NET для некоторых распространенных сценариев с использованием сущностей хранилища очередей Azure. Сюда относятся такие задачи, как создание очереди Azure, а также добавление, изменение, чтение и удаление сообщений очереди.

##<a name="prerequisites"></a>предварительным требованиям

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Учетная запись хранения Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Создание контроллера MVC 

1. В **обозревателе решений** щелкните правой кнопкой мыши **Контроллеры** и в контекстном меню выберите **Добавить -> Контроллер**.

    ![Добавление контроллера в приложение ASP.NET MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. В диалоговом окне **Добавление шаблона** выберите **Контроллер MVC5 — пустой** и нажмите кнопку **Добавить**.

    ![Выбор типа контроллера MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. В диалоговом окне **Добавление контроллера** присвойте контроллеру имя *BlobsController* и нажмите кнопку **Добавить**.

    ![Назначение имени для контроллера MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Добавьте в файл `QueuesController.cs` следующие директивы *using*:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
## <a name="create-a-queue"></a>Создание очереди

Чтобы создать очередь, выполните приведенные ниже действия.

> [!NOTE]
> 
> Переходите к этому разделу, только выполнив все действия, описанные в разделе [Настройка среды разработки](#set-up-the-development-environment). 

1. Откройте файл `QueuesController.cs` . 

1. Добавьте метод **CreateQueue**, который возвращает **ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. В методе **CreateQueue** организуйте получение объекта **CloudStorageAccount**, который представляет сведения об учетной записи хранения. Используйте следующий фрагмент кода, чтобы получить строку подключения к хранилищу и сведения об учетной записи хранения из конфигурации службы Azure. (Измените *&lt;storage-account-name>* на имя учетной записи хранения Azure, к которой вы получаете доступ.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Получите объект **CloudQueueClient**, представляющий клиента службы очередей.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Получите объект **CloudQueue**, представляющий ссылку на требуемое имя очереди. Метод **CloudQueueClient.GetQueueReference** не выполняет запрос к хранилищу очередей. Ссылка возвращается всегда, независимо от существования очереди. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Вызовите метод **CloudQueue.CreateIfNotExists**, чтобы создать очередь, если она еще не создана. Метод **CloudQueue.CreateIfNotExists** возвращает значение**true**, если очередь не существовала и была успешно создана. В противном случае возвращается значение **false**.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Занесите имя очереди в **ViewBag**.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. В **обозревателе решений** разверните папку **Представления**, щелкните правой кнопкой мыши **Очереди**, а затем в контекстном меню выберите **Добавить -> Представление**.

1. В диалоговом окне **Добавление представления** введите имя представления **CreateQueue** и выберите **Добавить**.

1. Откройте файл `CreateQueue.cshtml` и измените его таким образом, чтобы он выглядел, как показано ниже.

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Запустите приложение и выберите **Создание очереди**, чтобы увидеть результаты, как на снимке экрана ниже.
  
    ![Создание очереди](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Как упоминалось ранее, метод **CloudQueue.CreateIfNotExists** возвращает значение **true**, только если создается не существовавшая ранее очередь. Если вы запустите приложение, когда очередь уже существует, метод вернет значение **false**. Чтобы запустить приложение несколько раз, вам нужно удалять очередь перед каждым повторным запуском приложения. Чтобы удалить очередь, используйте метод **CloudQueue.Delete**. Ее также можно удалить с помощью [портала Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) или в [обозревателе хранилищ Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Добавление сообщения в очередь

Когда вы закончите [создание очереди](#create-a-queue), можно добавлять в нее сообщения. В этом разделе мы будем добавлять сообщения в очередь с именем *test-queue*. 

> [!NOTE]
> 
> Переходите к этому разделу, только выполнив все действия, описанные в разделе [Настройка среды разработки](#set-up-the-development-environment). 

1. Откройте файл `QueuesController.cs` .

1. Добавьте метод **AddMessage**, который возвращает **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. В рамках метода **AddMessage** получите объект **CloudStorageAccount**, который представляет сведения об учетной записи хранения. Используйте следующий фрагмент кода, чтобы получить строку подключения к хранилищу и сведения об учетной записи хранения из конфигурации службы Azure. (Измените *&lt;storage-account-name>* на имя учетной записи хранения Azure, к которой вы получаете доступ.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Получите объект **CloudQueueClient**, представляющий клиента службы очередей.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Получите объект **CloudQueueContainer**, представляющий ссылку на очередь. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Создайте объект **CloudQueueMessage**, представляющий сообщение, которое требуется добавить в очередь. Для создания объекта **CloudQueueMessage** можно использовать либо строку (в формате UTF-8), либо массив типа byte.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Вызовите метод **CloudQueue.AddMessage**, чтобы добавить сообщение в очередь.

    ```csharp
    queue.AddMessage(message);
    ```

1. Создайте и настройте несколько свойств **ViewBag**, чтобы они отображались в представлении.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. В **обозревателе решений** разверните папку **Представления**, щелкните правой кнопкой мыши **Очереди**, а затем в контекстном меню выберите **Добавить -> Представление**.

1. В диалоговом окне **Добавление представления** введите имя представления **AddMessage** и выберите **Добавить**.

1. Откройте файл `AddMessage.cshtml` и измените его таким образом, чтобы он выглядел, как показано ниже.

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Запустите приложение и выберите **Добавление сообщения**, чтобы увидеть результаты, как на снимке экрана ниже.
  
    ![Добавление сообщения](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

Чтение сообщений демонстрируется в двух разделах: [Чтение сообщения из очереди, не удаляя его](#read-a-message-from-a-queue-without-removing-it) и [Чтение и удаление сообщения из очереди](#read-and-remove-a-message-from-a-queue).    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Чтение сообщения из очереди, не удаляя его

В следующем разделе объясняется, как программным способом просмотреть (прочитать, не удаляя) первое сообщение в очереди.  

> [!NOTE]
> 
> Переходите к этому разделу, только выполнив все действия, описанные в разделе [Настройка среды разработки](#set-up-the-development-environment). 

1. Откройте файл `QueuesController.cs` .

1. Добавьте метод **PeekMessage**, который возвращает **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. В рамках метода **PeekMessage** получите объект **CloudStorageAccount**, который представляет сведения об учетной записи хранения. Используйте следующий фрагмент кода, чтобы получить строку подключения к хранилищу и сведения об учетной записи хранения из конфигурации службы Azure. (Измените *&lt;storage-account-name>* на имя учетной записи хранения Azure, к которой вы получаете доступ.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Получите объект **CloudQueueClient**, представляющий клиента службы очередей.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Получите объект **CloudQueueContainer**, представляющий ссылку на очередь. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Вызовите метод **CloudQueue.PeekMessage**, чтобы прочитать первое сообщение из начала очереди, не удаляя его. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Занесите в **ViewBag** два значения: имя очереди и прочитанное сообщение. Объект **CloudQueueMessage**, предоставляет два свойства для получения значений объекта: **CloudQueueMessage.AsBytes** и **CloudQueueMessage.AsString**. **AsString** (который используется в этом примере) возвращает строку, а **AsBytes** возвращает массив байтов.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. В **обозревателе решений** разверните папку **Представления**, щелкните правой кнопкой мыши **Очереди**, а затем в контекстном меню выберите **Добавить -> Представление**.

1. В диалоговом окне **Добавление представления** введите имя представления **PeekMessage** и выберите **Добавить**.

1. Откройте файл `PeekMessage.cshtml` и измените его таким образом, чтобы он выглядел, как показано ниже.

    ```csharp
    @{
        ViewBag.Title = "PeekMessage";
    }
    
    <h2>Peek Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Peeked Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>    
    ```

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Запустите приложение и выберите **Просмотр сообщения**, чтобы увидеть результаты, как на снимке экрана ниже.
  
    ![Просмотр сообщения](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Чтение и удаление сообщения из очереди

В этом разделе вы узнаете, как можно прочитать и удалить сообщение из очереди.   

> [!NOTE]
> 
> Переходите к этому разделу, только выполнив все действия, описанные в разделе [Настройка среды разработки](#set-up-the-development-environment). 

1. Откройте файл `QueuesController.cs` .

1. Добавьте метод **ReadMessage**, который возвращает **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. В рамках метода **ReadMessage** получите объект **CloudStorageAccount**, который представляет сведения об учетной записи хранения. Используйте следующий фрагмент кода, чтобы получить строку подключения к хранилищу и сведения об учетной записи хранения из конфигурации службы Azure. (Измените *&lt;storage-account-name>* на имя учетной записи хранения Azure, к которой вы получаете доступ.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Получите объект **CloudQueueClient**, представляющий клиента службы очередей.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Получите объект **CloudQueueContainer**, представляющий ссылку на очередь. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Вызовите метод **CloudQueue.GetMessage**, чтобы прочитать первое сообщение в очереди. Метод **CloudQueue.GetMessage** на 30 секунд (по умолчанию) делает сообщение невидимым для любого другого кода, считывающего сообщения, чтобы никакой другой код не смог изменить или удалить сообщение, пока вы с ним работаете. Чтобы изменить промежуток времени, в течение которого сообщение остается невидимым, измените параметр **visibilityTimeout**, передаваемый в метод **CloudQueue.GetMessage**.

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Вызовите метод **CloudQueueMessage.Delete**, чтобы удалить сообщение из очереди.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Занесите во **ViewBag** удаленное сообщение и имя очереди.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. В **обозревателе решений** разверните папку **Представления**, щелкните правой кнопкой мыши **Очереди**, а затем в контекстном меню выберите **Добавить -> Представление**.

1. В диалоговом окне **Добавление представления** введите имя представления **ReadMessage** и выберите **Добавить**.

1. Откройте файл `ReadMessage.cshtml` и измените его таким образом, чтобы он выглядел, как показано ниже.

    ```csharp
    @{
        ViewBag.Title = "ReadMessage";
    }
    
    <h2>Read Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Read (and Deleted) Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>
    ```

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Запустите приложение и выберите **Чтение и удаление сообщения**, чтобы увидеть результаты, как на снимке экрана ниже.
  
    ![Чтение и удаление сообщения](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Получение длины очереди

В этом разделе показано, как узнать длину очереди (количество сообщений). 

> [!NOTE]
> 
> Переходите к этому разделу, только выполнив все действия, описанные в разделе [Настройка среды разработки](#set-up-the-development-environment). 

1. Откройте файл `QueuesController.cs` .

1. Добавьте метод **GetQueueLength**, который возвращает **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. В рамках метода **ReadMessage** получите объект **CloudStorageAccount**, который представляет сведения об учетной записи хранения. Используйте следующий фрагмент кода, чтобы получить строку подключения к хранилищу и сведения об учетной записи хранения из конфигурации службы Azure. (Измените *&lt;storage-account-name>* на имя учетной записи хранения Azure, к которой вы получаете доступ.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Получите объект **CloudQueueClient**, представляющий клиента службы очередей.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Получите объект **CloudQueueContainer**, представляющий ссылку на очередь. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Вызовите метод **CloudQueue.FetchAttributes**, чтобы получить атрибуты очереди (включая ее длину). 

    ```csharp
    queue.FetchAttributes();
    ```

6. Просмотрите свойство **CloudQueue.ApproximateMessageCount**, чтобы узнать длину очереди.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Занесите во **ViewBag** имя и длину очереди.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. В **обозревателе решений** разверните папку **Представления**, щелкните правой кнопкой мыши **Очереди**, а затем в контекстном меню выберите **Добавить -> Представление**.

1. В диалоговом окне **Добавление представления** введите **GetQueueLength** для имени представления и выберите **Добавить**.

1. Откройте файл `GetQueueLengthMessage.cshtml` и измените его таким образом, чтобы он выглядел, как показано ниже.

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Запустите приложение и выберите **Получение длины очереди**, чтобы увидеть результаты, как на снимке экрана ниже.
  
    ![Получение длины очереди](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Удаление очереди
В этом разделе показано, как удалить очередь. 

> [!NOTE]
> 
> Переходите к этому разделу, только выполнив все действия, описанные в разделе [Настройка среды разработки](#set-up-the-development-environment). 

1. Откройте файл `QueuesController.cs` .

1. Добавьте метод **DeleteQueue**, который возвращает **ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. В методе **DeleteQueue** организуйте получение объекта **CloudStorageAccount**, который представляет сведения об учетной записи хранения. Используйте следующий фрагмент кода, чтобы получить строку подключения к хранилищу и сведения об учетной записи хранения из конфигурации службы Azure. (Измените *&lt;storage-account-name>* на имя учетной записи хранения Azure, к которой вы получаете доступ.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Получите объект **CloudQueueClient**, представляющий клиента службы очередей.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Получите объект **CloudQueueContainer**, представляющий ссылку на очередь. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Вызовите метод **CloudQueue.Delete**, чтобы удалить очередь, представленную объектом **CloudQueue**.

    ```csharp
    queue.Delete();
    ```

1. Занесите во **ViewBag** имя и длину очереди.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. В **обозревателе решений** разверните папку **Представления**, щелкните правой кнопкой мыши **Очереди**, а затем в контекстном меню выберите **Добавить -> Представление**.

1. В диалоговом окне **Добавление представления** введите имя представления **DeleteQueue** и выберите **Добавить**.

1. Откройте файл `DeleteQueue.cshtml` и измените его таким образом, чтобы он выглядел, как показано ниже.

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. В **обозревателе решений** разверните папку **Представления -> Общие** и откройте `_Layout.cshtml`.

1. После последней ссылки **Html.ActionLink** добавьте следующую ссылку **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Запустите приложение и выберите **Получение длины очереди**, чтобы увидеть результаты, как на снимке экрана ниже.
  
    ![Удаление очереди.](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Дополнительная информация
Просмотрите дополнительные руководства, чтобы изучить дополнительные возможности хранения данных в Azure.

  * [Приступая к работе с хранилищем BLOB-объектов Azure и подключенными службами Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Начало работы с хранилищем таблиц Azure и подключенными службами Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
