---
title: "Запуск заданий диспетчера данных Microsoft Azure StorSimple с помощью пакета SDK для .NET | Документация Майкрософт"
description: "Узнайте, как запускать задания диспетчера данных StorSimple с помощью пакета SDK для .NET (закрытая предварительная версия)"
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
ms.date: 11/22/2016
ms.author: vidarmsft
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 44d243a034b20b99faf284c8615e470bc6f9d020
ms.contentlocale: ru-ru
ms.lasthandoff: 08/08/2017

---

# <a name="use-the-net-sdk-to-initiate-data-transformation-private-preview"></a>Запуск преобразования данных с помощью пакета SDK для .NET (закрытая предварительная версия)

## <a name="overview"></a>Обзор

В этой статье объясняется, как использовать функцию преобразования данных в службе диспетчера данных StorSimple для преобразования данных устройства StorSimple. Затем преобразованные данные используются другими службами Azure в облаке. Кроме того, в статье содержится пошаговое руководство, которое поможет создать пример консольного приложения .NET для запуска задания преобразования данных и отслеживания его завершения.

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы убедитесь, что у вас есть следующие компоненты:
*   Система, в которой установлена среда Visual Studio 2012, 2013, 2015 или 2017.
*   Azure PowerShell установлен. [Скачать Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Параметры конфигурации для инициализации задания преобразования данных (инструкции для получения этих параметров приведены в этой статье).
*   Определение задания, которое правильно настроено в гибридном ресурсе данных в группе ресурсов.
*   Все необходимые библиотеки DLL. Их можно скачать из [репозитория GitHub](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [Скрипт](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) `Get-ConfigurationParams.ps1` из репозитория GitHub.

## <a name="step-by-step"></a>Пошаговое руководство

Выполните следующие шаги, чтобы запустить задание преобразования данных с помощью .NET.

1. Чтобы получить параметры конфигурации, сделайте следующее:
    1. Скачайте скрипт `Get-ConfigurationParams.ps1` из репозитория GitHub в расположение `C:\DataTransformation`.
    1. Запустите скрипт `Get-ConfigurationParams.ps1` из репозитория GitHub. Введите следующую команду:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Вы можете передать любые значения для ActiveDirectoryKey и AppName.


2. Этот скрипт выводит следующие значения:
    * Идентификатор клиента
    * Tenant ID
    * ключ Active Directory (совпадает с указанным выше);
    * Идентификатор подписки

3. С помощью Visual Studio 2012, 2013 или 2015 создайте консольное приложение C# .NET.

    1. Запустите **Visual Studio 2012, Visual Studio 2013 или Visual Studio 2015**.
    1. Щелкните **Файл**, наведите указатель мыши на пункт **Создать** и щелкните **Проект**.
    2. Разверните раздел **Шаблоны** и выберите **Visual C#**.
    3. Выберите **Консольное приложение** в списке типов проектов справа.
    4. В поле **Имя** введите **DataTransformationApp**.
    5. В поле **Расположение** выберите **C:\DataTransformation**.
    6. Нажмите кнопку **ОК** , чтобы создать проект.

4.  Теперь добавьте все библиотеки DLL из папки [dlls](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) в качестве **ссылочных** в созданный проект. Чтобы скачать DLL-файлы, сделайте следующее:

    1. В Visual Studio выберите **Просмотр > Обозреватель решений**.
    1. Щелкните стрелку слева от проекта приложения преобразования данных. Щелкните **Ссылки**, а затем щелкните этот элемент правой кнопкой мыши и выберите **Добавить ссылку**.
    2. Перейдите в расположение папки с пакетами, выберите все библиотеки DLL, щелкните **Добавить**, а затем нажмите кнопку **ОК**.

5. Добавьте следующие операторы **using** в файл исходного кода (Program.cs) в проекте.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```


6. Представленный ниже код инициализирует экземпляр задания преобразования данных. Добавьте его в **метод Main**. Замените значения параметров конфигурации полученными ранее значениями. Добавьте значения **имени группы ресурсов** и **имени гибридного ресурса данных**. Это **имя группы ресурсов**, которая размещает гибридный ресурс данных, использованный для настройки определения задания.

    ```
    // Setup the configuration parameters.
    var configParams = new ConfigurationParams
    {
        ClientId = "client-id",
        TenantId = "tenant-id",
        ActiveDirectoryKey = "active-directory-key",
        SubscriptionId = "subscription-id",
        ResourceGroupName = "resource-group-name",
        ResourceName = "resource-name"
    };

    // Initialize the Data Transformation Job instance.
    DataTransformationJob dataTransformationJob = new DataTransformationJob(configParams);

    ```

7. Укажите параметры, с которыми необходимо выполнить определение задания.

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);

    ```

    (ИЛИ)

    Если параметры определения задания нужно изменить во время выполнения, добавьте следующий код:

    ```
    string jobDefinitionName = "job-definition-name";
    // Must start with a '\'
    var rootDirectories = new List<string> {@"\root"};

    // Name of the volume on the StorSimple device.
    var volumeNames = new List<string> {"volume-name"};

    var dataTransformationInput = new DataTransformationInput
    {
        // If you require the latest existing backup to be picked else use TakeNow to trigger a new backup.
        BackupChoice = BackupChoice.UseExistingLatest.ToString(),
        // Name of the StorSimple device.
        DeviceName = "device-name",
        // Name of the container in Azure storage where the files will be placed after execution.
        ContainerName = "container-name",
        // File name filter (search pattern) to be applied on files under the root directory. * - Match all files.
        FileNameFilter = "*",
        // List of root directories.
        RootDirectories = rootDirectories,
        // Name of the volume on StorSimple device on which the relevant data is present. 
        VolumeNames = volumeNames
    };
    
    ```

8. После инициализации добавьте следующий код, чтобы активировать задание преобразования данных в определении задания. Добавьте соответствующее **имя определения задания**.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);

    ```

9. Это задание передает соответствующие файлы в корневом каталоге тома StorSimple в указанный контейнер. При отправке файла в очередь передается сообщение (в той же учетной записи хранения, что и контейнер) с тем же именем, что и у определения задания. Это сообщение можно использовать в качестве триггера для запуска дальнейшей обработки файла.

10. После активации задания добавьте следующий код для отслеживания завершения задания.

    ```
    Job jobDetails = null;

    // Poll the job.
    do
    {
        jobDetails = dataTransformationJob.GetJob(jobDefinitionName, jobId);

        // Wait before polling for the status again.
        Thread.Sleep(TimeSpan.FromSeconds(retryAfter));

    } while (jobDetails.Status == JobStatus.InProgress);

    // Completion status of the job.
    Console.WriteLine("JobStatus: {0}", jobDetails.Status);
    
    // To hold the console before exiting.
    Console.Read();

    ```


## <a name="next-steps"></a>Дальнейшие действия

[Преобразуйте данные с помощью пользовательского интерфейса диспетчера данных StorSimple](storsimple-data-manager-ui.md).

