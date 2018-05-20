---
title: Анализ видео с помощью Служб мультимедиа Azure | Документация Майкрософт
description: Используя инструкции этого руководства, вы сможете проанализировать видео с помощью Служб мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/09/2018
ms.author: juliako
ms.openlocfilehash: a228443a787db2cc71a7700b978d8f52edb59e14
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-analyze-videos-with-azure-media-services"></a>Руководство. Анализ видео с помощью Служб мультимедиа Azure 

Это руководство содержит сведения об анализе видео с помощью Служб мультимедиа Azure. Имеется множество сценариев, в которых может потребоваться получить подробные сведения о видео- и аудиозаписях. Например, чтобы повысить удовлетворенность клиентов, организации могут выполнить преобразование речи в текст. Таким образом можно сделать записи клиентов доступным для поиска каталогом с индексами и панелями мониторинга. Затем они могут получить информативные бизнес-сведения, например список распространенных жалоб, источники этих жалоб и т. д.

В этом учебнике описаны следующие процедуры.    

> [!div class="checklist"]
> * Запуск Azure Cloud Shell
> * Создание учетной записи служб мультимедиа
> * Доступ к API Служб мультимедиа.
> * Настройка примера приложения
> * Тщательный анализ примера кода.
> * Запуск приложения
> * Изучение выходных данных.
> * Очистка ресурсов

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>предварительным требованиям

Вы можете скачать [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15) бесплатно, если у вас нет Visual Studio.

## <a name="download-the-sample"></a>Скачивание примера приложения

Клонируйте репозиторий GitHub, содержащий пример .NET, на компьютер с помощью следующей команды:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-sample-code-in-detail"></a>Тщательный анализ примера кода

В этом разделе рассматриваются функции, определенные в файле [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) проекта *AnalyzeVideos*.

### <a name="start-using-media-services-apis-with-net-sdk"></a>Начало использования API Служб мультимедиа с пакетом SDK для .NET

Чтобы начать использование API Служб мультимедиа с .NET, создайте объект **AzureMediaServicesClient**. Чтобы создать объект, введите учетные данные, необходимые клиенту для подключения к Azure с помощью Azure AD. Сначала необходимо получить маркер, а затем создать объект **ClientCredential** из возвращенного токена. В коде, который был клонирован в начале статьи, объект **ArmClientCredential** используется для получения токена.  

```csharp
private static IAzureMediaServicesClient CreateMediaServicesClient(ConfigWrapper config)
{
    ArmClientCredentials credentials = new ArmClientCredentials(config);

    return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
    {
        SubscriptionId = config.SubscriptionId,
    };
}
```

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Создание выходного ресурса для хранения результатов задания 

Выходной ресурс сохраняет результаты задания. Проект определяет функцию **DownloadResults**, которая скачивает результаты из выходного ресурса в папку output, чтобы вы могли просмотреть их.

```csharp
private static Asset CreateOutputAsset(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string assetName)
{
    // Check if an Asset already exists
    Asset outputAsset = client.Assets.Get(resourceGroupName, accountName, assetName);
    Asset asset = new Asset();
    string outputAssetName = assetName;

    if (outputAsset != null)
    {
        // Name collision! In order to get the sample to work, let's just go ahead and create a unique asset name
        // Note that the returned Asset can have a different name than the one specified as an input parameter.
        // You may want to update this part to throw an Exception instead, and handle name collisions differently.
        string uniqueness = @"-" + Guid.NewGuid().ToString();
        outputAssetName += uniqueness;
    }

    return client.Assets.CreateOrUpdate(resourceGroupName, accountName, outputAssetName, asset);
}
```
### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Создание преобразования и задания, которое анализирует видео

При кодировании или обработке содержимого в Службах мультимедиа параметры кодировки часто задают в качестве набора инструкций. Затем необходимо отправить **задание**, чтобы применить этот набор к видео. Отправляя новые задания для каждого нового видео, вы применяете этот набор ко всем видео в своей библиотеке. Набор инструкций в Службах мультимедиа называется **Преобразование**. Дополнительные сведения см. в статье о [преобразованиях и заданиях](transform-concept.md). Пример кода, описанный в этом руководстве, определяет набор инструкций, которые анализируют указанное видео. 

#### <a name="transform"></a>Преобразование

При создании нового экземпляра **преобразования** необходимо указать, что требуется создать в качестве выходных данных. Обязательный параметр — это объект **TransformOutput**, как показано в приведенном выше примере кода. Каждый объект **TransformOutput** содержит **предустановку** (Preset). **Предустановка** описывает пошаговые инструкции для операций обработки видео и звука, которые будут использоваться для создания нужного объекта **TransformOutput**. В этом примере используется предустановка **VideoAnalyzerPreset** и язык (ru_RU) передается в конструктор. Эта предустановка позволяет извлечь из видео множество аналитических сведений об аудио и видео. Если необходимо извлечь из видео множество аналитических сведений об аудио, используйте предустановку **AudioAnalyzerPreset**. 

При создании **преобразования** сначала проверьте, существует ли оно, с помощью метода **Get**, как показано в следующем коде.  В Службах мультимедиа версии 3 методы **Get**, отправленные к сущностям, возвращают значение **NULL**, если сущность не существует (проверка по имени без учета регистра).

```csharp
private static Transform EnsureTransformExists(IAzureMediaServicesClient client, 
    string resourceGroupName, 
    string accountName, 
    string transformName, 
    Preset preset)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Start by defining the desired outputs.
        TransformOutput[] outputs = new TransformOutput[]
        {
            new TransformOutput(preset),
        };

        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs);
    }

    return transform;
}
```

#### <a name="job"></a>Задание

Как было указано выше, объект **преобразования** является набором инструкций, а **задание** — фактическим запросом к Службам мультимедиа для применения этого **преобразования** к данному видео и аудио. **Задание** указывает такую информацию, как расположение входного и выходного видео. Вы можете указать расположение видео с помощью URL-адресов HTTP, URL-адресов SAS или ресурсов в учетной записи Службы мультимедиа. 

В этом примере входные видеоданные передаются с указанного URL-адреса HTTP.  

```csharp
private static Job SubmitJob(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string transformName, string jobName, string outputAssetName)
{
    JobInputHttp jobInput =
        new JobInputHttp(files: new[] { "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4" });
            
    string uniqueJobName = jobName;
    Job job = client.Jobs.Get(resourceGroupName, accountName, transformName, jobName);

    if (job != null)
    {
        // Job already exists with the same name, so let's append a GUID
        string uniqueness = @"-" + Guid.NewGuid().ToString();
        uniqueJobName += uniqueness;
    }

    JobOutput[] jobOutputs =
    {
        new JobOutputAsset(outputAssetName),
    };

    job = client.Jobs.Create(
                resourceGroupName,
                accountName,
                transformName,
                uniqueJobName,
                new Job
                {
                    Input = jobInput,
                    Outputs = jobOutputs,
                });

    return job;
}
```

### <a name="wait-for-the-job-to-complete"></a>Ожидание завершения задания

Выполнение задания занимает некоторое время. По его завершению вы будете уведомлены. Получить уведомление о завершении задания можно несколькими способами. Самый простой (который показан тут) — использовать опрос. 

Опрос не рекомендуется для приложений рабочей среды из-за потенциальной задержки. Его можно регулировать при чрезмерном использовании в учетной записи. Вместо этого разработчики должны использовать службу "Сетка событий".

Служба "Сетка событий" предназначена для обеспечения высокого уровня доступности, стабильной производительности и динамического масштабирования. С помощью службы "Сетка событий Azure" приложения могут ожидать передачи данных и реагировать на события, поступающие буквально из всех служб Azure и пользовательских источников. Простая реактивная обработка событий на основе HTTP позволяет создавать эффективные решения с использованием интеллектуальной фильтрации и маршрутизации событий. Дополнительные сведения см. в статье [Route Azure Media Services events to a custom web endpoint using CLI](job-state-events-cli-how-to.md) (Маршрутизация событий Служб мультимедиа в пользовательскую конечную точку с помощью CLI).

**Задание** обычно проходит через такие состояния: **Запланировано**, **В очереди**, **Обработка**, **Завершено** (конечное состояние). Если в задании обнаружена ошибка, вы получите состояние **Ошибка**. Если задание находится в процессе отмены, вы получите состояние **Выполнение отмены** и **Отменено** по завершении.

```csharp
private static Job WaitForJobToFinish(IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName)
{
    int SleepInterval = 60 * 1000;

    Job job = null;

    while (true)
    {
        job = client.Jobs.Get(resourceGroupName, accountName, transformName, jobName);

        if (job.State == JobState.Finished || job.State == JobState.Error || job.State == JobState.Canceled)
        {
            break;
        }

        Console.WriteLine($"Job is {job.State}.");
        for (int i = 0; i < job.Outputs.Count; i++)
        {
            JobOutput output = job.Outputs[i];
            Console.Write($"\tJobOutput[{i}] is {output.State}.");
            if (output.State == JobState.Processing)
            {
                Console.Write($"  Progress: {output.Progress}");
            }
            Console.WriteLine();
        }
        System.Threading.Thread.Sleep(SleepInterval);
    }

    return job;
}
```

### <a name="download-the-result-of-the-job"></a>Скачивание результатов задания

 С помощью следующей функции можно скачать результаты задания из выходного ресурса контейнера в папку output, чтобы проанализировать их. 

```csharp
private static void DownloadResults(IAzureMediaServicesClient client,
    string resourceGroup,
    string accountName,
    string assetName,
    string resultsFolder)
{
    AssetContainerSas assetContainerSas = client.Assets.ListContainerSas(
            resourceGroup,
            accountName,
            assetName,
            permissions: AssetContainerPermission.Read,
            expiryTime: DateTime.UtcNow.AddHours(1).ToUniversalTime()
            );

    Uri containerSasUrl = new Uri(assetContainerSas.AssetContainerSasUrls.FirstOrDefault());
    CloudBlobContainer container = new CloudBlobContainer(containerSasUrl);

    string directory = Path.Combine(resultsFolder, assetName);
    Directory.CreateDirectory(directory);

    Console.WriteLine("Downloading results to {0}.", directory);

    foreach (IListBlobItem blobItem in container.ListBlobs(null, true, BlobListingDetails.None))
    {
        if (blobItem is CloudBlockBlob)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            string filename = Path.Combine(directory, blob.Name);

            blob.DownloadToFile(filename, FileMode.Create);
        }
    }

    Console.WriteLine("Download complete.");
}
```

### <a name="clean-up-resource-in-your-media-services-account"></a>Очистка ресурсов в учетной записи служб мультимедиа

Как правило, необходимо очистить все, кроме объектов, которые вы планируете использовать повторно. Обычно повторно используются преобразования и будут сохранены StreamingLocators и т. д. Если учетную запись требуется очистить после эксперимента, следует удалить ресурсы, которые не планируется использовать повторно. Например, следующий код удаляет задания.

```csharp
static void CleanUp(IAzureMediaServicesClient client,
        string resourceGroupName,
        string accountName, 
        string transformName)
{
    foreach (var job in client.Jobs.List(resourceGroupName, accountName, transformName))
    {
        client.Jobs.Delete(resourceGroupName, accountName, transformName, job.Name);
    }

    foreach (var asset in client.Assets.List(resourceGroupName, accountName))
    {
        client.Assets.Delete(resourceGroupName, accountName, asset.Name);
    }
}
```

## <a name="run-the-sample-app"></a>Запуск примера приложения

Для запуска приложения *AnalyzeVideos* нажмите клавиши CTRL+F5.

При запуске программы задание создает эскизы каждого лица, обнаруженного в видео. Оно также создает файл insights.json.

## <a name="examine-the-output"></a>Изучение выходных данных

Выходной файл анализированных видео называется insights.json. Этот файл содержит аналитические сведения о видео. Описание элементов находятся в JSON-файле статьи об [аналитике мультимедиа](intelligence-concept.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вам больше не нужны какие-либо ресурсы в группе ресурсов, включая Службы мультимедиа и учетные записи хранения, созданные для этого руководства, удалите группу ресурсов, созданную ранее. Для этого можно использовать средство **CloudShell**.

В **CloudShell** выполните следующую команду:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Многопоточность

Пакеты SDK для Служб мультимедиа Azure версии 3 не являются потокобезопасными. При работе с многопоточным приложением необходимо создать объект AzureMediaServicesClient для каждого потока.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство. Отправка, кодирование и потоковая передача видео с помощью API](stream-files-tutorial-with-api.md)
