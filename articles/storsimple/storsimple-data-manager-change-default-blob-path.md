---
title: "Изменение пути к большому двоичному объекту по умолчанию | Документация Майкрософт"
description: "Узнайте, как настроить функцию Azure для переименования пути к файлу большого двоичного объекта (закрытая предварительная версия)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
ms.openlocfilehash: 057d4d7370207859617eb63238bf425bfa6d3e16
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="change-a-blob-path-from-the-default-path-private-preview"></a>Изменение пути к большому двоичному объекту по умолчанию (закрытая предварительная версия)

В этой статье описывается настройка функции Azure для переименования пути к файлу большого двоичного объекта по умолчанию. 

## <a name="prerequisites"></a>Предварительные требования

Убедитесь в наличии определения задания, которое правильно настроено в гибридном ресурсе данных в группе ресурсов.

## <a name="create-an-azure-function"></a>Создание функции Azure

Чтобы создать функцию Azure, выполните следующие действия:

1. Перейдите на [портал Azure](http://portal.azure.com/).

2. В верхней части левой области щелкните **Создать**. 

3. В поле **поиска** введите **Приложение-функция** и нажмите клавишу ВВОД.

    ![Введение "Приложение-функция" поле поиска](./media/storsimple-data-manager-change-default-blob-path/goto-function-app-resource.png)

4. В списке **Результаты** щелкните **Приложение-функция**.

    ![Выбор ресурса приложения-функции в списке результатов](./media/storsimple-data-manager-change-default-blob-path/select-function-app-resource.png)

    Откроется окно **Приложение-функция**.

5. Щелкните **Создать**.

    ![Кнопка "Создать" в окне "Приложение-функция"](./media/storsimple-data-manager-change-default-blob-path/create-new-function-app.png)

6. В колонке настройки **Приложение-функция** выполните следующие действия:

    а. В поле **Имя приложения** введите имя приложения.
    
    b. В поле **Подписка** введите имя подписки.

    c. В разделе **Группа ресурсов** щелкните **Создать**, а затем введите имя группы ресурсов.

    d. В поле **План размещения** укажите **План потребления**.

    д. В поле **Расположение** укажите расположение.

    f. В разделе **Учетная запись хранения** выберите существующую учетную запись хранения или создайте новую. Учетная запись хранения используется внутренне для функции.

    ![Введение данных конфигурации нового приложения-функции](./media/storsimple-data-manager-change-default-blob-path/enter-new-funcion-app-data.png)

7. Щелкните **Создать**.  
    Приложение-функция создано.

8. В левой области щелкните **Больше служб**, а затем выполните следующие действия:
    
    а. В поле **Фильтр** введите **Службы приложений**.
    
    b. Щелкните **Службы приложений**. 

    ![Ссылка "Больше служб" в левой области](./media/storsimple-data-manager-change-default-blob-path/more-services.png)

9. В списке служб приложений щелкните имя приложения-функции.  
    Откроется страница этого приложения-функции.

10. В левой области щелкните **Новая функция**, а затем выполните следующие действия: 

    а. В списке **Язык** выберите **C#**.
    
    b. В массиве элементов выберите шаблон **QueueTrigger-CSharp**.

    c. В поле **Присвойте функции имя** введите имя для своей функции.

    d. В поле **Имя очереди** введите имя определения задания преобразования данных.

    д. В разделе **Подключение к учетной записи хранения** щелкните **создать**и выберите учетную запись, соответствующую заданию преобразования данных.  
        Запишите это имя подключения. Оно понадобится позже в функции Azure.

       ![Создание функции C#](./media/storsimple-data-manager-change-default-blob-path/create-new-csharp-function.png)

    f. Щелкните **Создать**.  
    Откроется окно **Функция**.

11. В окне **Функция** выполните _CSX_-файл, а затем выполните следующие действия:

    а. Вставьте следующий код:

    ```
    using System;
    using System.Configuration;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.WindowsAzure.Storage.Queue;
    using Microsoft.WindowsAzure.Storage;
    using System.Collections.Generic;
    using System.Linq;

    public static void Run(QueueItem myQueueItem, TraceWriter log)
    {
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

        string storageAccUriEndswith = "windows.net/";
        string uri = myQueueItem.TargetLocation.Replace("%20", " ");
        log.Info($"Blob Uri: {uri}");

        // Remove storage account uri string
        uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

        string containerName = uri.Substring(0, uri.IndexOf("/")); 

        // Remove container name string
        uri = uri.Substring(containerName.Length + 1);

        // Current blob path
        string blobName = uri; 

        string volumeName = uri.Substring(containerName.Length + 1);
        volumeName = uri.Substring(0, uri.IndexOf("/"));

        // Remove volume name string
        uri = uri.Substring(volumeName.Length + 1);

        string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
        string newBlobName = uri.Substring(newContainerName.Length + 1);

        log.Info($"Container name: {containerName}");
        log.Info($"Volume name: {volumeName}");
        log.Info($"New container name: {newContainerName}");

        log.Info($"Blob name: {blobName}");
        log.Info($"New blob name: {newBlobName}");

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Container reference
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
        newContainer.CreateIfNotExists();

        if(!container.Exists())
        {
            log.Info($"Container - {containerName} not exists");
            return;
        }

        if(!newContainer.Exists())
        {
            log.Info($"Container - {newContainerName} not exists");
            return;
        }

        CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
        if (!blob.Exists())
        {
            // Skip to copy the blob to new container, if source blob doesn't exist
            log.Info($"The specified blob does not exist.");
            log.Info($"Blob Uri: {blob.Uri}");
            return;
        }

        CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
        if (!blobCopy.Exists())
        {
            blobCopy.StartCopy(blob);
            // Delete old blob, after copy to new container
            blob.DeleteIfExists();
            log.Info($"Blob file path renamed completed successfully");
        }
        else
        {
            log.Info($"Blob file path renamed already done");
            // Delete old blob, if already exists.
            blob.DeleteIfExists();
        }
    }

    public class QueueItem
    {
        public string SourceLocation {get;set;}
        public long SizeInBytes {get;set;}
        public string Status {get;set;}
        public string JobID {get;set;}
        public string TargetLocation {get; set;}
    }

    ```

    b. Замените имя **STORAGE_CONNECTIONNAME** в строке 11 именем подключения к учетной записи хранения (см. пункт 8c).

    c. В верхней левом углу нажмите кнопку **Сохранить**.

    ![Сохранение функции](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Чтобы завершить создание функции, добавьте еще один файл, выполнив следующие действия:

    а. Щелкните **Просмотреть файлы**.

       ![Ссылка "Просмотреть файлы"](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    b. Щелкните **Добавить**.
    
    c. Введите **project.json** и нажмите клавишу ВВОД.
    
    d. В файле **project.json** скопируйте следующий код:

    ```
    {
    "frameworks": {
        "net46":{
        "dependencies": {
            "windowsazure.storage": "8.1.1"
        }
        }
    }
    }

    ```

    д. Щелкните **Сохранить**.

Вы создали функцию Azure. Эта функция запускается каждый раз, когда задание преобразования данных создает большой двоичный объект.

## <a name="next-steps"></a>Дальнейшие действия

[Использование пользовательского интерфейса для службы диспетчера данных StorSimple (закрытая предварительная версия)](storsimple-data-manager-ui.md)
