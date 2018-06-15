---
title: Обеспечение высокой доступности данных приложений в Azure | Документация Майкрософт
description: Использование геоизбыточного хранилища с доступом на чтение для обеспечения высокой доступности данных приложений
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.topic: tutorial
ms.date: 03/26/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: 8cf96059b1bbfbad24bf28fec9ddb0aa930adbad
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32193780"
---
# <a name="make-your-application-data-highly-available-with-azure-storage"></a>Обеспечение высокой доступности данных приложений в хранилище Azure

Эта статья входит в серию руководств, в которой показано, как обеспечить высокую доступность данных приложений в Azure. В итоге у вас будет консольное приложение, которое передает и извлекает большие двоичные объекты в учетной записи хранения [геоизбыточного хранилища с доступом на чтение](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (RA-GRS). RA-GRS функционирует, реплицируя транзакции из основного в дополнительный регион. Репликация гарантирует, что данные в дополнительном регионе согласованы в конечном счете. Приложение использует шаблон [размыкателя цепи](/azure/architecture/patterns/circuit-breaker), чтобы определить, к какой конечной точке подключаться. При моделировании сбоя приложение переключается на использование вторичной конечной точки.

В первой части цикла вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создайте учетную запись хранения.
> * Скачивание примера приложения
> * Задание строки подключения
> * Запуск консольного приложения

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством:
 
# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)
* Установите [Visual Studio 2017](https://www.visualstudio.com/downloads/) с указанными ниже рабочими нагрузками:
  - **разработка Azure.**

  ![Разработка Azure (в разделе Web & Cloud (Сеть и облако))](media/storage-create-geo-redundant-storage/workloads.png)

* Загрузите и установите [Fiddler](https://www.telerik.com/download/fiddler) (необязательно).
 
# <a name="python-tabpython"></a>[Python] (#tab/python) 

* установите [Python](https://www.python.org/downloads/);
* Загрузите и установите [пакет SDK службы хранилища Azure для Python](https://github.com/Azure/azure-storage-python).
* Загрузите и установите [Fiddler](https://www.telerik.com/download/fiddler) (необязательно).

# <a name="java-tabjava"></a>[Java] (#tab/java)

* Установите и настройте [Maven](http://maven.apache.org/download.cgi) для работы из командной строки.
* Установите и настройте [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

---

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-the-azure-portal"></a>Войдите на портал Azure.

Войдите на [портал Azure](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Создайте учетную запись хранения.

Учетная запись хранения предоставляет уникальное пространство имен для хранения ваших объектов данных в службе хранилища Azure и доступа к ним.

Выполните следующие действия, чтобы создать учетную запись хранения геоизбыточного хранилища с доступом на чтение:

1. Нажмите кнопку **Создать ресурс** в верхнем левом углу окна портала Azure.

2. Щелкните **Хранилище** на странице **Создать** и выберите **Учетная запись хранения — BLOB-объект, файл, таблица, очередь** на вкладке **Избранные**.
3. Заполните форму учетной записи хранения следующей информацией, как показано в изображении ниже, и нажмите кнопку **Создать**:

   | Параметр       | Рекомендуемое значение | Описание |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Имя** | mystorageaccount | Уникальное значение учетной записи хранения. |
   | **Модель развертывания** | Диспетчер ресурсов  | Resource Manager содержит новые функции.|
   | **Account kind** (Тип учетной записи) | Хранилище версии 2 | Дополнительные сведения о типах учетных записей см. в разделе [Типы учетных записей хранения](../common/storage-introduction.md#types-of-storage-accounts). |
   | **Производительность** | Стандартная | Значения Standard достаточно для примера сценария. |
   | **Репликация**| Геоизбыточное хранилище с доступом для чтения (RA-GRS) | Это значение необходимо для работы примера. |
   |**Secure transfer required** (Требуется безопасное перемещение) | Отключено| Безопасное перемещение не является обязательным для этого сценария. |
   |**Подписка** | Ваша подписка |Дополнительные сведения о подписках см. [здесь](https://account.windowsazure.com/Subscriptions). |
   |**ResourceGroup** | myResourceGroup |Допустимые имена групп ресурсов см. в статье о [правилах и ограничениях именования](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   |**Местоположение.** | Восток США | Выберите расположение. |

![создание учетной записи хранения](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>Скачивание примера приложения

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

[Загрузите пример проекта](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) и извлеките (распакуйте) файл storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Вы также можете использовать команду [git](https://git-scm.com/), чтобы загрузить копию приложения в среду разработки. Пример проекта содержит консольное приложение.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git 
```
# <a name="python-tabpython"></a>[Python] (#tab/python) 

[Загрузите пример проекта](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) и извлеките (распакуйте) файл storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Вы также можете использовать команду [git](https://git-scm.com/), чтобы загрузить копию приложения в среду разработки. Пример проекта содержит основное приложение Python.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="java-tabjava"></a>[Java] (#tab/java)
[Скачайте пример проекта](https://github.com/Azure-Samples/storage-java-ha-ra-grs) и извлеките файл storage-java-ragrs.zip. Вы также можете использовать команду [git](https://git-scm.com/), чтобы загрузить копию приложения в среду разработки. Пример проекта содержит простое приложение Java.

```bash
git clone https://github.com/Azure-Samples/storage-java-ha-ra-grs.git
```
---


## <a name="set-the-connection-string"></a>Задание строки подключения

В приложении необходимо указать строку подключения для учетной записи хранения. Рекомендуем хранить эту строку подключения в переменной среды на локальном компьютере, где выполняется приложение. Чтобы создать переменную среды, выполните один из приведенных ниже примеров в зависимости от операционной системы.

Войдите в свою учетную запись хранения на портале Azure. В меню учетной записи хранения в разделе **Параметры** выберите **Ключи доступа**. Скопируйте **строку подключения** из основного или вторичного ключа. Замените \<yourconnectionstring\> фактической строкой подключения, выполнив одну из следующих команд в зависимости от операционной системы. Эта команда сохраняет переменную среды на локальном компьютере. В Windows переменная среды доступна только после перезагрузки **командной строки** или используемой оболочки. Замените **\<storageConnectionString\>** в примере ниже.

# <a name="linux-tablinux"></a>[Linux] (#tab/linux) 
export storageconnectionstring=\<yourconnectionstring\> 

# <a name="windows-tabwindows"></a>[Windows] (#tab/windows) 
setx storageconnectionstring "\<yourconnectionstring\>"

---


## <a name="run-the-console-application"></a>Запуск консольного приложения

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)
В Visual Studio нажмите клавишу **F5** или щелкните **Запустить**, чтобы начать отладку приложения. Visual Studio автоматически восстанавливает отсутствующие пакеты NuGet, если это настроено. Дополнительные сведения см. в разделе [Обзор восстановления пакетов](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview).

Окно консоли запускается, и приложение начинает работать. Приложение передает изображение **HelloWorld.png** из решения в учетную запись хранения. Приложение проверяет, было ли изображение реплицировано во вторичную конечную точку RA-GRS. Затем оно начинает скачивать изображение (до 999 раз). Каждая операция чтения помечается буквой **P** или **S**. **P** представляет основную конечную точку, а **S** — вторичную конечную точку.

![Консольное приложение выполняется](media/storage-create-geo-redundant-storage/figure3.png)

В примере кода задача `RunCircuitBreakerAsync` в файле `Program.cs` используется для скачивания изображения из учетной записи хранения с помощью метода [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.downloadtofileasync?view=azure-dotnet). Перед скачиванием определяется [OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet). Контекст операции определяет обработчики событий, которые срабатывают при успешном завершении скачивания или при сбое и повторной попытке.

# <a name="python-tabpython"></a>[Python] (#tab/python) 
Чтобы запустить приложение в терминале или в командной строке, перейдите к каталогу **circuitbreaker.py**, а затем введите `python circuitbreaker.py`. Приложение передает изображение **HelloWorld.png** из решения в учетную запись хранения. Приложение проверяет, было ли изображение реплицировано во вторичную конечную точку RA-GRS. Затем оно начинает скачивать изображение (до 999 раз). Каждая операция чтения помечается буквой **P** или **S**. **P** представляет основную конечную точку, а **S** — вторичную конечную точку.

![Консольное приложение выполняется](media/storage-create-geo-redundant-storage/figure3.png)

В примере кода метод `run_circuit_breaker` в файле `circuitbreaker.py` используется для скачивания изображения из учетной записи хранения с помощью метода [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html). 

Для функции повтора объекта хранилища задана линейная политика повтора. Функция повтора определяет, следует ли повторять запрос, и указывает время ожидания (в секундах) перед повторным выполнением запроса. Задайте для **retry\_to\_secondary** значение true, если запрос следует повторять в случае сбоя исходного запроса к основной конечной точке. В примере приложения пользовательская политика повтора определяется в функции `retry_callback` объекта хранилища.
 
Перед скачиванием определяется функция объекта службы [retry_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) и [response_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python). Эти функции определяют обработчики событий, которые срабатывают при успешном завершении скачивания или при сбое и повторной попытке.  

# <a name="java-tabjava"></a>[Java] (#tab/java)
Вы можете запустить приложение, открыв терминал или командную строку в папке скачанного приложения. После этого введите `mvn compile exec:java`, чтобы запустить приложение. Затем приложение передаст изображение **HelloWorld.png** из каталога в вашу учетную запись хранения и проверит, реплицировано ли оно во вторичную конечную точку RA-GRS. По завершении проверки приложение начнет скачивать изображение и передавать результат в конечную точку, из которой выполняется скачивание.

Для функции повтора объекта хранилища задана линейная политика повтора. Функция повтора определяет, следует ли повторять запрос, и указывает время ожидания (в секундах) между повторными выполнениями запроса. Для свойства **LocationMode** класса **BlobRequestOptions** задано значение **PRIMARY\_THEN\_SECONDARY**. Это позволяет приложению автоматически переключаться на вторичное расположение, если не удается перейти в первичное расположение при попытке скачать файл **HelloWorld.png**.

---

## <a name="understand-the-sample-code"></a>Разбор примера кода

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

### <a name="retry-event-handler"></a>Обработчик события при повторном запуске

Обработчик событий `OperationContextRetrying` вызывается, когда скачивание завершается с ошибкой и настроено на повторный запуск. Если достигнуто максимальное количество попыток, определенных для приложения, значение параметра [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) запроса изменяется на `SecondaryOnly`. В таком случае приложение принудительно повторно пытается скачать изображение из вторичной конечной точки. Эта конфигурация уменьшает время, затрачиваемое на запрос изображения, так как основная конечная точка не запрашивается бесконечно.
 
```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
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

Обработчик события `OperationContextRequestCompleted` вызывается при успешном выполнении запроса. Если приложение использует вторичную конечную точку, приложение запрашивает эту конечную точку до 20 раз. После 20 раза приложение обратно устанавливает для параметра [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) значение `PrimaryThenSecondary` и повторно запрашивает основную конечную точку. При успешном выполнении запроса приложение продолжает выполнять чтение из основной конечной точки.
 
```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
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

# <a name="python-tabpython"></a>[Python] (#tab/python) 

### <a name="retry-event-handler"></a>Обработчик события при повторном запуске

Обработчик событий `retry_callback` вызывается, когда скачивание завершается с ошибкой и настроено на повторный запуск. Если достигнуто максимальное количество попыток, определенных для приложения, значение параметра [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) запроса изменяется на `SECONDARY`. В таком случае приложение принудительно повторно пытается скачать изображение из вторичной конечной точки. Эта конфигурация уменьшает время, затрачиваемое на запрос изображения, так как основная конечная точка не запрашивается бесконечно.  

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write("\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>Обработчик события при успешном выполнении запроса

Обработчик события `response_callback` вызывается при успешном выполнении запроса. Если приложение использует вторичную конечную точку, приложение запрашивает эту конечную точку до 20 раз. После 20 раза приложение обратно устанавливает для параметра [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) значение `PRIMARY` и повторно запрашивает основную конечную точку. При успешном выполнении запроса приложение продолжает выполнять чтение из основной конечной точки.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

# <a name="java-tabjava"></a>[Java] (#tab/java)

В Java не нужно определять обработчики обратных вызовов, если для свойства **LocationMode** класса **BlobRequestOptions** задано значение **PRIMARY\_THEN\_SECONDARY**. Это позволяет приложению автоматически переключаться на вторичное расположение, если не удается перейти в первичное расположение при попытке скачать файл **HelloWorld.png**.

```java
    BlobRequestOptions myReqOptions = new BlobRequestOptions();
    myReqOptions.setRetryPolicyFactory(new RetryLinearRetry(deltaBackOff, maxAttempts));
    myReqOptions.setLocationMode(LocationMode.PRIMARY_THEN_SECONDARY);
    blobClient.setDefaultRequestOptions(myReqOptions);

    blob.downloadToFile(downloadedFile.getAbsolutePath(),null,blobClient.getDefaultRequestOptions(),opContext);
```
---


## <a name="next-steps"></a>Дополнительная информация

В первой части этой серии вы узнали о том, как сделать приложение доступным с помощью учетных записей хранения RA-GRS, в частности как выполнять такие действия:

> [!div class="checklist"]
> * Создайте учетную запись хранения.
> * Скачивание примера приложения
> * Задание строки подключения
> * Запуск консольного приложения

Перейдите ко второй части серии, чтобы узнать, как имитировать сбой и заставить приложение использовать вторичную конечную точку RA-GRS.

> [!div class="nextstepaction"]
> [Simulate a failure in accessing read-access redundant storage](storage-simulate-failure-ragrs-account-app.md) (Имитация сбоя при подключении к геоизбыточному хранилищу с доступом на чтение)
