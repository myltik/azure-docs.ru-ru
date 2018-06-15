---
title: Обработка файлов мультимедиа с помощью технологии Hyperlapse служб мультимедиа Azure | Документация Майкрософт
description: Azure Media Hyperlapse создает плавное замедленное видео от первого лица или содержимое, характерное для экшн-камер. В этом разделе показано, как использовать индексатор мультимедийных данных.
services: media-services
documentationcenter: ''
author: asolanki
manager: johndeu
editor: ''
ms.assetid: 37d54db6-9cf3-4ae9-b3c6-0d29c744e965
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2018
ms.author: adsolank
ms.openlocfilehash: ed64a616538ed4699abc03225a2dcf27d164521f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783173"
---
# <a name="hyperlapse-media-files-with-azure-media-hyperlapse"></a>Файлы мультимедиа Hyperlapse с Azure Media Hyperlapse
Azure Media Hyperlapse представляет собой обработчик мультимедиа, создающий плавное замедленное видео от первого лица или содержимое, характерное для экшн-камер.  Microsoft Hyperlapse для служб мультимедиа Azure, облачный аналог [настольной системы Hyperlapse Pro и телефонной системы Hyperlapse Mobile от Microsoft Research](http://aka.ms/hyperlapse), использует обширные возможности масштабирования платформы обработки мультимедиа служб мультимедиа Azure, чтобы реализовать горизонтальное масштабирование и параллелизовать массовую обработку Hyperlapse.

> [!IMPORTANT]
> Продукт Microsoft Hyperlapse оптимально подходит для работы с содержимым от первого лица с перемещающейся камерой. Хотя материал с неподвижной камеры может обрабатываться, производительность и качество обработчика мультимедиа Azure Media Hyperlapse не позволяет гарантировать работу с другими типами содержимого.
> 
> 

Задание Azure Media Hyperlapse принимает в качестве входных данных файлы ресурсов MP4, MOV или WMV вместе с файлом конфигурации, который определяет, какие кадры видео должны быть замедлены и на какой скорости (например, первые 10 000 кадров на скорости 2x).  Результатом является стабилизированное и замедленное представление входного видео.

## <a name="hyperlapse-an-asset"></a>Использование Hyperlapse для обработки ресурса-контейнера
Сначала необходимо загрузить требуемый входной файл для служб мультимедиа Azure.  Дополнительные сведения об основных понятиях, связанных с загрузкой содержимого и управлением им, см. в статье [об управлении содержимым](media-services-portal-vod-get-started.md).

### <a id="configuration"></a>Предустановка конфигурации для Hyperlapse
После помещения содержимого в учетную запись служб мультимедиа необходимо создать предустановку вашей конфигурации.  В следующей таблице описаны поля, задаваемые пользователем:

| Поле | ОПИСАНИЕ |
| --- | --- |
| StartFrame |Кадр, с которого должна начинаться обработка Microsoft Hyperlapse. |
| NumFrames |Число обрабатываемых кадров. |
| Speed |Коэффициент ускорения входного видео. |

Ниже приведен пример соответствующего файла конфигурации для XML и JSON:

**Предустановка XML:**
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
        <Sources>
            <Source StartFrame="0" NumFrames="10000" />
        </Sources>
        <Options>
            <Speed>12</Speed>
        </Options>
    </Preset>
```

**Предустановка JSON:**
```json
    {
        "Version":1.0,
        "Sources": [
            {
                "StartFrame":0,
                "NumFrames":2147483647
            }
        ],
        "Options": {
            "Speed":1,
            "Stabilize":false
        }
    }
```

### <a id="sample_code"></a> Microsoft Hyperlapse с пакетом AMS .NET SDK
Следующий метод передает файл мультимедиа как ресурс и создает задание с помощью обработчика мультимедиа Azure Media Hyperlapse.

> [!NOTE]
> Для работы этого кода в области с именем "context" уже должен находиться CloudMediaContext.  Дополнительные сведения об этом см. в [статье об управлении содержимым](media-services-dotnet-get-started.md).
> 
> [!NOTE]
> Строковый аргумент "hyperConfig" должен быть соответствующей предустановкой конфигурации для JSON или XML, как описано выше.
> 
> 

```csharp
        static bool RunHyperlapseJob(string input, string output, string hyperConfig)
        {
            // create asset with input file
            IAsset asset = context
            .Assets
            .CreateAssetAndUploadSingleFile(input, "My Hyperlapse Input", AssetCreationOptions.None);

            // grab instances of Azure Media Hyperlapse MP
            IMediaProcessor mp = context
            .MediaProcessors
            .GetLatestMediaProcessorByName("Azure Media Hyperlapse");

            // create Job with Hyperlapse task
            IJob job = context
            .Jobs
            .Create(String.Format("Hyperlapse {0}", input));

            if (String.IsNullOrEmpty(hyperConfig))
            {
            // config cannot be empty
            return false;
            }

            hyperConfig = File.ReadAllText(hyperConfig);

            ITask hyperlapseTask = job.Tasks.AddNew("Hyperlapse task",
            mp,
            hyperConfig,
            TaskOptions.None);
            hyperlapseTask.InputAssets.Add(asset);
            hyperlapseTask.OutputAssets.AddNew("Hyperlapse output",
            AssetCreationOptions.None);

            job.Submit();

            // Create progress printing and querying tasks
            Task progressPrintTask = new Task(() =>
            {

            IJob jobQuery = null;
            do
            {
                var progressContext = context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}\t{2}",
                DateTime.Now,
                jobQuery.State,
                jobQuery.Tasks[0].Progress));
                Thread.Sleep(10000);
            }
            while (jobQuery.State != JobState.Finished &&
                                   jobQuery.State != JobState.Error &&
                                   jobQuery.State != JobState.Canceled);
                });
                
            progressPrintTask.Start();

            Task progressJobTask = job.GetExecutionProgressTask(
                                                 CancellationToken.None);
            progressJobTask.Wait();

            // If job state is Error, the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                Console.WriteLine(string.Format("Error: {0}. {1}",
                                                error.Code,
                                                error.Message));  
                return false;                  
            }

        DownloadAsset(job.OutputMediaAssets.First(), output);
        return true;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }


    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }
```

### <a id="file_types"></a>Поддерживаемые типы файлов
* MP4
* MOV
* WMV

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Связанные ссылки
[Общие сведения об аналитике служб мультимедиа Azure](media-services-analytics-overview.md)

[Демонстрационные материалы для медиааналитики Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

