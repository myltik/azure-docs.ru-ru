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
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 8a06e4b3f482943f55e998c3c68857d3530ff98a
ms.lasthandoff: 03/21/2017

---

# <a name="change-blob-path-from-the-default-private-preview"></a>Изменение пути к большому двоичному объекту по умолчанию (закрытая предварительная версия)

В этой статье описывается настройка функции Azure для переименования пути к файлу большого двоичного объекта по умолчанию. 

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы убедитесь, что у вас есть следующие компоненты:
* Определение задания, которое правильно настроено в гибридном ресурсе данных в группе ресурсов.

## <a name="create-an-azure-function"></a>Создание функции Azure

Выполните следующие действия для создания функции Azure.

#### <a name="to-create-an-azure-function"></a>Создание функции Azure

1. Перейдите на [портал Azure](http://portal.azure.com/).

2. В верхнем левом углу щелкните **+ Создать**. Введите "приложение-функция" в текстовом поле **Поиск** и нажмите клавишу **ВВОД**.

    ![Перейдите к ресурсу приложения-функции](./media/storsimple-data-manager-change-default-blob-path/goto-function-app-resource.png)

3. Щелкните **Приложение-функция** в результатах.

    ![Выберите ресурс приложения-функции](./media/storsimple-data-manager-change-default-blob-path/select-function-app-resource.png)

4. Откройте окно **Приложение-функция** и нажмите кнопку **Создать**.

    ![Создание приложения-функции](./media/storsimple-data-manager-change-default-blob-path/create-new-function-app.png)

5. Введите требуемые сведения в колонке **Конфигурация** и нажмите кнопку **Создать**.

    1. Имя приложения.
    2. Подписки
    3. Группа ресурсов
    4. План размещения — **План потребления**.
    5. Расположение
    6. Учетная запись хранения. Используйте существующую учетную запись хранения или создайте ее. Учетная запись хранения используется внутренне для функции.

        ![Введите данные конфигурации нового приложения-функции](./media/storsimple-data-manager-change-default-blob-path/enter-new-funcion-app-data.png)

6. Создав приложение-функцию, выберите **Больше служб >** в нижней части страницы слева. В текстовом поле **Фильтр** введите "службы приложений" и щелкните **Службы приложений**.

    ![Пункт "Больше служб >"](./media/storsimple-data-manager-change-default-blob-path/more-services.png)

7. В списке служб приложений щелкните **Function app name** (Имя приложения-функции).

8. Щелкните **+ New Function** (+ Создать функцию). В раскрывающемся списке **Язык** выберите **C#**. В списке шаблонов выберите параметр **QueueTrigger-CSharp**. Введите все входные данные.

   1. Имя: укажите имя функции.
   2. Имя очереди: введите **имя определения задания преобразования данных**.
   3. Подключение к учетной записи хранения: щелкните **Создать**. Выберите учетную запись, соответствующую заданию преобразования данных.
      
      Запишите `Connection name`. Это имя понадобится в функции Azure позже.

   4. Нажмите кнопку **Создать**.

       ![Создание функции C Sharp >](./media/storsimple-data-manager-change-default-blob-path/create-new-csharp-function.png)

9. В окне **функции** выполните файл _CSX_. Скопируйте следующий код и вставьте в Блокнот:

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
            log.Info($"Blob Uri: {myQueueItem.TargetLocation}");
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            string uriString = "windows.net/";
            string containerName = myQueueItem.TargetLocation;
            containerName = containerName.Substring(containerName.IndexOf(uriString) + uriString.Length);
            containerName = containerName.Substring(0, containerName.IndexOf("/"));
            //log.Info($"Container name: {containerName}");

            CloudBlobContainer container = blobClient.GetContainerReference(containerName);

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            string containerUri = string.Format("{0}/", container.Uri.ToString());  // Reading container Uri
            string blobUri =  myQueueItem.TargetLocation;
            string blobName = blobUri.Replace(containerUri, string.Empty).Replace("%20", " "); // Reading existing file path after container name
            string newBlobName = blobName.Substring(blobName.IndexOf(string.Format("{0}/", container.Name)) + container.Name.Length + 1); // Reading actual File path after container name

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            CloudBlockBlob blobCopy = container.GetBlockBlobReference(newBlobName);
            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy the blob
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

   1. Замените точное имя **STORAGE_CONNECTIONNAME** в строке 12 подключением учетной записи хранения (см. пункт 8в).
   2. В верхнем левом углу нажмите кнопку **Сохранить**.

       ![Функция сохранения >](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

10.  В правом углу щелкните **Просмотреть файлы**.

    ![Просмотр файлов](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

   1. Щелкните **+ Добавить**. Введите **project.json** и нажмите клавишу **ВВОД**.
   2. Скопируйте и вставьте следующий код в файл **project.json**.

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

   2. Щелкните **Сохранить**.

Вы создали функцию Azure. Эта функция запускается каждый раз, когда задание преобразования данных создает большой двоичный объект.

## <a name="next-steps"></a>Дальнейшие действия

[Преобразуйте данные с помощью пользовательского интерфейса диспетчера данных StorSimple](storsimple-data-manager-ui.md).

