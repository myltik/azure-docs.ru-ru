---
title: "Приступая к работе с хранилищем очередей Azure и подключенными службами Visual Studio (ASP.NET) | Документация Майкрософт"
description: "Как приступить к работе, используя хранилище очередей Azure в проекте ASP.NET в Visual Studio после подключения к учетной записи хранения с помощью подключенных служб Visual Studio."
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: f58c2b522f81dc2dc86f0d2c6bc4872504cf7377
ms.openlocfilehash: 70875287e79aaf49e1b8802cf2953cd5381b97f4


---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Приступая к работе с хранилищем очередей Azure и подключенными службами Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Обзор

Хранилище очередей Azure — это служба хранения большого количества неструктурированных данных, к которым можно получить доступ по протоколам HTTP или HTTPS. Одно сообщение очереди может быть размером до 64 КБ, а очередь может содержать неограниченное число сообщений до общего ограничения емкости учетной записи хранения.

В этой статье показано, как программно управлять сущностями хранилища очередей Azure, выполнения типичные задачи, таких как создание очереди Azure, добавление, изменение, чтение и удаление сообщений в очереди.

> [!NOTE]
> 
> В разделах кода в этой статье предполагается, что вы уже подключены к учетной записи хранения Azure с помощью подключенных служб. Для настройки подключенных служб откройте обозреватель решений Visual Studio, щелкните проект правой кнопкой мыши и выберите в контекстном меню **Добавить > Подключенная служба**. Затем следуйте инструкциям в диалоговом окне, чтобы подключиться к требуемой учетной записи хранения Azure.      

## <a name="create-a-queue"></a>Создание очереди

Ниже описано, как программным способом создать очередь. В приложении ASP.NET MVC код будет находиться в контроллере.

1. Добавьте следующие директивы *using*:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Получите объект **CloudStorageAccount** , представляющий данные учетной записи хранения. Используйте следующий код, чтобы получить строку подключения и сведения об учетной записи хранения из конфигурации службы Azure. (Замените *<storage-account-name>* именем учетной записи хранения Azure, к которой осуществляется доступ.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Получите объект **CloudQueueClient**, представляющий клиента службы очередей.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Получите объект **CloudQueue**, представляющий ссылку на требуемое имя очереди. (Замените *<queue-name>* именем создаваемой очереди.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Вызовите метод **CloudQueue.CreateIfNotExists**, чтобы создать очередь, если она еще не создана. 

        queue.CreateIfNotExists();


## <a name="add-a-message-to-a-queue"></a>Добавление сообщения в очередь

Ниже описано, как программным способом добавить сообщение в очередь. В приложении ASP.NET MVC код будет находиться в контроллере.

1. Добавьте следующие директивы *using*:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Получите объект **CloudStorageAccount** , представляющий данные учетной записи хранения. Используйте следующий код, чтобы получить строку подключения и сведения об учетной записи хранения из конфигурации службы Azure. (Замените *<storage-account-name>* именем учетной записи хранения Azure, к которой осуществляется доступ.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Получите объект **CloudQueueClient**, представляющий клиента службы очередей.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Получите объект **CloudQueue**, представляющий ссылку на требуемое имя очереди. (Замените *<queue-name>* именем очереди, в которую требуется добавить сообщение.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Создайте объект **CloudQueueMessage**, представляющий сообщение, которое требуется добавить в очередь. Для создания объекта **CloudQueueMessage** можно использовать либо строку (в формате UTF-8), либо массив типа byte. (Замените * <queue-message>* добавляемым сообщением.

        CloudQueueMessage message = new CloudQueueMessage(<queue-message>);

6. Вызовите метод **CloudQueue.AddMessage**, чтобы добавить сообщение в очередь.

        queue.AddMessage(message);

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Чтение сообщения из очереди, не удаляя его

Следующие шаги показывают, как программным способом просмотреть сообщение в очереди (прочитать первое сообщение, не удаляя его). В приложении ASP.NET MVC код будет находиться в контроллере. 

1. Добавьте следующие директивы *using*:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Получите объект **CloudStorageAccount** , представляющий данные учетной записи хранения. Используйте следующий код, чтобы получить строку подключения и сведения об учетной записи хранения из конфигурации службы Azure. (Замените *<storage-account-name>* именем учетной записи хранения Azure, к которой осуществляется доступ.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Получите объект **CloudQueueClient**, представляющий клиента службы очередей.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Получите объект **CloudQueue**, представляющий ссылку на очередь. (Замените *<queue-name>* именем очереди, в которой требуется прочитать сообщение.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Вызовите метод **CloudQueue.PeekMessage**, чтобы прочитать сообщение в начале очереди, не удаляя его из очереди.

        CloudQueueMessage message = queue.PeekMessage();

6. Просмотрите значение объекта **CloudQueueMessage**, используя свойство **CloudQueueMessage.AsBytes** или **CloudQueueMessage.AsString**.

        string messageAsString = message.AsString;
        byte[] messageAsBytes = message.AsBytes;

## <a name="read-and-remove-a-message-from-a-queue"></a>Чтение и удаление сообщения из очереди

Ниже описано, как программным способом прочитать сообщение в очереди, а затем удалить его. В приложении ASP.NET MVC код будет находиться в контроллере. 

1. Добавьте следующие директивы *using*:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Получите объект **CloudStorageAccount** , представляющий данные учетной записи хранения. Используйте следующий код, чтобы получить строку подключения и сведения об учетной записи хранения из конфигурации службы Azure. (Замените *<storage-account-name>* именем учетной записи хранения Azure, к которой осуществляется доступ.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Получите объект **CloudQueueClient**, представляющий клиента службы очередей.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Получите объект **CloudQueue**, представляющий ссылку на очередь. (Замените *<queue-name>* именем очереди, в которой требуется прочитать сообщение.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Вызовите метод **CloudQueue.GetMessage**, чтобы прочитать первое сообщение в очереди. Метод **CloudQueue.GetMessage** на 30 секунд (по умолчанию) делает сообщение невидимым для любого другого кода, считывающего сообщения, чтобы никакой другой код не смог изменить или удалить сообщение, пока вы с ним работаете. Чтобы изменить промежуток времени, в течение которого сообщение остается невидимым, измените параметр **visibilityTimeout**, передаваемый в метод **CloudQueue.GetMessage**.

        // This message will be invisible to other code for 30 seconds.
        CloudQueueMessage message = queue.GetMessage();     

6. Вызовите метод **CloudQueueMessage.Delete**, чтобы удалить сообщение из очереди.

        queue.DeleteMessage(message);

## <a name="get-the-queue-length"></a>Получение длины очереди

Ниже описано, как программным способом получить длину очереди (число сообщений). В приложении ASP.NET MVC код будет находиться в контроллере. 

1. Добавьте следующие директивы *using*:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Получите объект **CloudStorageAccount** , представляющий данные учетной записи хранения. Используйте следующий код, чтобы получить строку подключения и сведения об учетной записи хранения из конфигурации службы Azure. (Замените *<storage-account-name>* именем учетной записи хранения Azure, к которой осуществляется доступ.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Получите объект **CloudQueueClient**, представляющий клиента службы очередей.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Получите объект **CloudQueue**, представляющий ссылку на очередь. (Замените *<queue-name>* именем очереди, длину которой требуется запросить.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Вызовите метод **CloudQueue.FetchAttributes**, чтобы получить атрибуты очереди (включая ее длину). 

        queue.FetchAttributes();

6. Просмотрите свойство **CloudQueue.ApproximateMessageCount**, чтобы узнать длину очереди.
 
        int? nMessages = queue.ApproximateMessageCount;

## <a name="delete-a-queue"></a>Удаление очереди
Ниже описано, как программным способом удалить очередь. 

1. Добавьте следующие директивы *using*:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Получите объект **CloudStorageAccount** , представляющий данные учетной записи хранения. Используйте следующий код, чтобы получить строку подключения и сведения об учетной записи хранения из конфигурации службы Azure. (Замените *<storage-account-name>* именем учетной записи хранения Azure, к которой осуществляется доступ.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Получите объект **CloudQueueClient**, представляющий клиента службы очередей.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Получите объект **CloudQueue**, представляющий ссылку на очередь. (Замените *<queue-name>* именем очереди, длину которой требуется запросить.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Вызовите метод **CloudQueue.Delete**, чтобы удалить очередь, представленную объектом **CloudQueue**.

        messageQueue.Delete();

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]




<!--HONumber=Dec16_HO2-->


