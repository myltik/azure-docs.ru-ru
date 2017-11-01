---
title: "Обеспечение высокой доступности данных приложений в Azure | Документация Майкрософт"
description: "Использование геоизбыточного хранилища с доступом на чтение для обеспечения высокой доступности данных приложений"
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 547ca7843f53bd11fdb922af8e0ae77e38f813d9
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2017
---
# <a name="make-your-application-data-highly-available-with-azure-storage"></a>Обеспечение высокой доступности данных приложений в хранилище Azure

Это руководство представляет первую часть цикла. В этом руководстве показано, как обеспечить высокую доступность данных приложений в Azure. В итоге у вас будет консольное приложение, которое передает и извлекает большие двоичные объекты в учетной записи хранения [геоизбыточного хранилища с доступом на чтение](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS). RA-GRS функционирует, реплицируя транзакции из основного в дополнительный регион. Репликация гарантирует, что данные в дополнительном регионе согласованы в конечном счете. Приложение использует шаблон [размыкателя цепи](/azure/architecture/patterns/circuit-breaker.md), чтобы определить, к какой конечной точке подключаться. При моделировании сбоя приложение переключается на использование вторичной конечной точки.

В первой части цикла вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создайте учетную запись хранения.
> * Скачивание примера приложения
> * Задание строки подключения
> * Запуск консольного приложения.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством:

* Установите [Visual Studio 2017](https://www.visualstudio.com/downloads/) с указанными ниже рабочими нагрузками:
  - **разработка Azure.**

  ![Разработка Azure (в разделе Web & Cloud (Сеть и облако))](media/storage-create-geo-redundant-storage/workloads.png)

* Скачайте и установите [Fiddler](https://www.telerik.com/download/fiddler).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-the-azure-portal"></a>Войдите на портал Azure.

Войдите на [портал Azure](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Создайте учетную запись хранения.

Учетная запись хранения предоставляет уникальное пространство имен для хранения ваших объектов данных в службе хранилища Azure и доступа к ним.

Выполните следующие действия, чтобы создать учетную запись хранения геоизбыточного хранилища с доступом на чтение:

1. Нажмите кнопку **Создать** в верхнем левом углу портала Azure.

2. Щелкните **Хранилище** на странице **Создать** и выберите **Учетная запись хранения — BLOB-объект, файл, таблица, очередь** на вкладке **Избранные**.
3. Заполните форму учетной записи хранения следующей информацией, как показано в изображении ниже, и нажмите кнопку **Создать**:

   | Настройка       | Рекомендуемое значение | Описание |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Имя** | mystorageaccount | Уникальное значение учетной записи хранения. |
   | **Модель развертывания** | Диспетчер ресурсов  | Resource Manager содержит новые функции.  |
   | **Account kind** (Тип учетной записи) | Универсальные | Дополнительные сведения о типах учетных записей см. в разделе [Типы учетных записей хранения](../common/storage-introduction.md#types-of-storage-accounts). |
   | **Производительность** | Standard | Значения Standard достаточно для примера сценария. |
   | **Репликация**| Геоизбыточное хранилище с доступом для чтения (RA-GRS) | Это значение необходимо для работы примера. |
   |**Secure transfer required** (Требуется безопасное перемещение) | Отключено| Безопасное перемещение не является обязательным для этого сценария. |
   |**Подписка** | Ваша подписка |Дополнительные сведения о подписках см. [здесь](https://account.windowsazure.com/Subscriptions). |
   |**ResourceGroup** | myResourceGroup |Допустимые имена групп ресурсов см. в статье о [правилах и ограничениях именования](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   |**Расположение** | Восток США | Выберите расположение. |

![создание учетной записи хранения](media/storage-create-geo-redundant-storage/figure1.png)

## <a name="download-the-sample"></a>Скачивание примера приложения

[Загрузите пример проекта](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip).

Извлеките (распакуйте) файл storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip.
Пример проекта содержит консольное приложение.

## <a name="set-the-connection-string"></a>Задание строки подключения

Откройте консольное приложение *storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs* в Visual Studio.

В разделе узла **appSettings** в файле **App.config** замените значение _StorageConnectionString_ строкой подключения учетной записи хранения. Чтобы получить это значение, выберите пункт **Ключи доступа** в разделе **Параметры** вашей учетной записи хранения на портале Azure. Скопируйте **строку подключения** из основного или вторичного ключа и вставьте ее в файл **App.config**. Нажмите кнопку **Сохранить**, чтобы сохранить файл по завершении.

![Файл app config](media/storage-create-geo-redundant-storage/figure2.png)

## <a name="run-the-console-application"></a>Запуск консольного приложения

В Visual Studio нажмите клавишу **F5** или щелкните **Запустить**, чтобы начать отладку приложения. Visual Studio автоматически восстанавливает отсутствующие пакеты Nuget, если это настроено. Дополнительные сведения см. в разделе [Обзор восстановления пакетов](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview). 

Окно консоли запускается, и приложение начинает работать. Приложение передает изображение **HelloWorld.png** из решения в учетную запись хранения. Приложение проверяет, было ли изображение реплицировано во вторичную конечную точку RA-GRS. Затем оно начинает скачивать изображение (до 999 раз). Каждая операция чтения помечается буквой **P** или **S**. **P** представляет основную конечную точку, а **S** — вторичную конечную точку.

![Консольное приложение выполняется](media/storage-create-geo-redundant-storage/figure3.png)

В примере кода задача `RunCircuitBreakerAsync` в файле `Program.cs` используется для скачивания изображения из учетной записи хранения с помощью метода [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.downloadtofileasync?view=azure-dotnet). Перед скачиванием определяется [OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet). Контекст операции определяет обработчики событий, которые срабатывают при успешном завершении скачивания или при сбое и повторной попытке.

### <a name="retry-event-handler"></a>Обработчик события при повторном запуске

Обработчик события `Operation_context_Retrying` вызывается, когда скачивание завершается с ошибкой и настроено на повторный запуск. Если достигнуто максимальное количество попыток, определенных для приложения, значение параметра [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) запроса изменяется на `SecondaryOnly`. В таком случае приложение принудительно повторно пытается скачать изображение из вторичной конечной точки. Эта конфигурация уменьшает время, затрачиваемое на запрос изображения, так как основная конечная точка не запрашивается бесконечно.

```csharp
private static void Operation_context_Retrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>Обработчик события при успешном выполнении запроса

Обработчик события `Operation_context_RequestCompleted` вызывается при успешном выполнении запроса. Если приложение использует вторичную конечную точку, приложение запрашивает эту конечную точку до 20 раз. После 20 раза приложение обратно устанавливает для параметра [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) значение `PrimaryThenSecondary` и повторно запрашивает основную конечную точку. При успешном выполнении запроса приложение продолжает выполнять чтение из основной конечной точки.

```csharp
private static void Operation_context_RequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times, 
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

В первой части этой серии вы узнали о том, как сделать приложение доступным с помощью учетных записей хранения RA-GRS, в частности как выполнять такие действия:

> [!div class="checklist"]
> * Создайте учетную запись хранения.
> * Скачивание примера приложения
> * Задание строки подключения
> * Запуск консольного приложения.

Перейдите ко второй части серии, чтобы узнать, как имитировать сбой и заставить приложение использовать вторичную конечную точку RA-GRS.

> [!div class="nextstepaction"]
> [Simulate a failure in accessing read-access redundant storage](storage-simulate-failure-ragrs-account-app.md) (Имитация сбоя при подключении к геоизбыточному хранилищу с доступом на чтение)
