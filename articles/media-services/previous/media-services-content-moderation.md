---
title: Использование Azure Media Content Moderator для обнаружения материалов, потенциально предназначенных для взрослых и носящих непристойный характер | Документация Майкрософт
description: Модерация видео помогает обнаруживать материалы, потенциально предназначенные для взрослых и носящие непристойный характер.
services: media-services
documentationcenter: ''
author: sanjeev3
manager: mikemcca
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/06/2018
ms.author: sajagtap
ms.openlocfilehash: e44308f38a138c0e186e41fc8310f8b480cd4e09
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783433"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Использование Azure Media Content Moderator для обнаружения материалов, потенциально предназначенных для взрослых и носящих непристойный характер

## <a name="overview"></a>Обзор
Обработчик мультимедиа **Azure Media Content Moderator** позволяет использовать автоматические средства модерации видео. Например, вы хотите, чтобы ваша команда модераторов определила в видеороликах материалы, потенциально предназначенные для взрослых и носящие непристойный характер, а затем проверила помеченное содержимое.

Сейчас обработчик мультимедиа **Azure Media Content Moderator** доступен в предварительной версии.

В этой статье приведены подробные сведения об обработчике **Azure Media Content Moderator** и показаны способы его применения с пакетом SDK Служб мультимедиа для .NET.

## <a name="content-moderator-input-files"></a>Входные файлы Content Moderator
Видеофайлы. Сейчас поддерживаются следующие форматы: MP4, MOV и WMV.

## <a name="content-moderator-output-files"></a>Выходные файлы Content Moderator
Прошедшие модерацию выходные данные в формате JSON включают автоматически обнаруженные снимки и опорные кадры. Опорные кадры возвращаются с оценками достоверности для материалов, потенциально предназначенных для взрослых и носящих непристойный характер. Кроме того, они содержат логический флаг, указывающий на необходимость проверки. Флаг рекомендации относительно проверки — это присваиваемое значение, которое рассчитывается на основе внутренних пороговых значений оценок на предмет содержания материалов, потенциально предназначенных для взрослых или носящих непристойный характер.

## <a name="elements-of-the-output-json-file"></a>Элементы выходного JSON-файла

Задание создает выходной файл JSON, который содержит метаданные обнаруженных снимков и опорных кадров. Кроме того, он указывает, есть ли на снимках и опорных кадрах содержимое для взрослых или непристойное содержимое.

Выходной файл JSON включает следующие элементы:

### <a name="root-json-elements"></a>Элементы корневого JSON

| Элемент | ОПИСАНИЕ |
| --- | --- |
| версия |Версия Content Moderator. |
| timescale |Количество тактов в секунду видео. |
| offset |Смещение времени для меток времени. В API поиска видео версии 1.0 это значение всегда равно 0. В будущем оно может измениться. |
| framerate |Количество кадров в секунду видео. |
| width |Ширина выходного видеокадра в пикселях.|
| height |Высота выходного видеокадра в пикселях.|
| totalDuration |Длительность входного видео в тактах. |
| [fragments](#fragments-json-elements) |Метаданные разделены на разные сегменты, называемые фрагментами. Каждый фрагмент является автоматически обнаруженным снимком с указанием начала, длительности, номера интервала и событий. |

### <a name="fragments-json-elements"></a>Элементы JSON фрагментов

|Элемент|ОПИСАНИЕ|
|---|---|
| start |Время начала первого события в тактах. |
| длительность |Продолжительность фрагмента в тактах. |
| interval |Интервал каждой записи события внутри фрагмента в тактах. |
| [events](#events-json-elements) |Каждое событие представляет собой клип, который состоит из опорных кадров, обнаруженных и отслеженных в пределах заданного периода времени. Оно представляет собой массив событий. Внешний массив представляет один интервал времени. Внутренний массив состоит из нуля или более событий, которые выполнялись в этот момент времени.|

### <a name="events-json-elements"></a>Элементы JSON событий

|Элемент|ОПИСАНИЕ|
|---|---|
| reviewRecommended | `true` или `false` в зависимости от того, превышают ли значения **adultScore** и **racyScore** внутренние пороговые значения. |
| adultScore | Оценка достоверности для материалов, потенциально предназначенных для взрослых. Рассчитывается по шкале от 0,00 до 0,99. |
| racyScore | Оценка достоверности для материалов, потенциально носящих непристойный характер. Рассчитывается по шкале от 0,00 до 0,99. |
| index | Индекс кадра по шкале от первого до последнего кадра. |
| timestamp | Расположение кадра в тактах. |
| shotIndex | Индекс родительского снимка. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Краткое руководство по использованию Content Moderator и пример выходных данных

### <a name="task-configuration-preset"></a>Конфигурация задачи (предустановка)
При создании задачи с использованием **Azure Media Content Moderator** необходимо задать предустановку конфигурации. Следующая предустановка конфигурации предназначена только для модерации содержимого.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>Пример кода .NET

В следующем примере кода .NET используется пакет SDK Служб мультимедиа для .NET, с помощью которого выполняется задание Content Moderator. В качестве входных данных принимается файл Служб мультимедиа, содержащий видео для модерации.
Полный исходный код и проект Visual Studio см. в [кратком видеоруководстве по Content Moderator](../../cognitive-services/Content-Moderator/video-moderation-api.md).


```csharp
    /// <summary>
    /// Run the Content Moderator job on the designated Asset from local file or blob storage
    /// </summary>
    /// <param name="asset"></param>
    static void RunContentModeratorJob(IAsset asset)
    {
        // Grab the presets
        string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

        // grab instance of Azure Media Content Moderator MP
        IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

        // create Job with Content Moderator task
        IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
                asset.AssetFiles.First() + "_" + Guid.NewGuid()));

        ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
                mp, configuration,
                TaskOptions.None);
        contentModeratorTask.InputAssets.Add(asset);
        contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
            AssetCreationOptions.None);

        job.Submit();


        // Create progress printing and querying tasks
        Task progressPrintTask = new Task(() =>
        {
            IJob jobQuery = null;
            do
            {
                var progressContext = _context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                    .First();
                    Console.WriteLine(string.Format("{0}\t{1}",
                    DateTime.Now,
                    jobQuery.State));
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
        }

        DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
    }

For the full source code and the Visual Studio project, check out the [Content Moderator video quickstart](../../cognitive-services/Content-Moderator/video-moderation-api.md).

### JSON output

The following example of a Content Moderator JSON output was truncated.

> [!NOTE]
> Location of a keyframe in seconds = timestamp/timescale

    {
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]
    }
    ]
    }
```

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Связанные ссылки
[Общие сведения об аналитике служб мультимедиа Azure](media-services-analytics-overview.md)

[Демонстрационные материалы для медиааналитики Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Дополнительная информация

См. дополнительные сведения о [решении для модерации и проверки видео](../../cognitive-services/Content-Moderator/video-moderation-human-review.md) с помощью Content Moderator.

Полный исходный код и проект Visual Studio см. в [кратком руководстве по модерации видео](../../cognitive-services/Content-Moderator/video-moderation-api.md). 

Узнайте, как создать [проверки видео](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) на основе прошедших модерацию выходных данных и [модерировать стенограммы](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) с помощью .NET.

См. подробное [руководство по модерации и проверке видео](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md) для .NET. 
