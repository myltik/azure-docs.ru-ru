---
title: "Использование Azure Media Encoder Standard для автоматического создания таблицы скоростей и разрешений | Документация Майкрософт"
description: "В этом разделе показано, как использовать Media Encoder Standard (MES) для автоматического создания таблицы скоростей и разрешений на основе разрешения и скорости входных данных. Разрешение и скорость входных данных никогда не превышаются. Например, если указаны входные данные с разрешением 720p и скоростью 3 Мбит/с, то выходные данные будут иметь разрешение не выше 720p, а их скорости начнутся со значений ниже 3 Мбит/с."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 63ed95da-1b82-44b0-b8ff-eebd535bc5c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 244413be8b094605883445bb3cbf675b538b704e
ms.lasthandoff: 03/10/2017


---
#  <a name="use-azure-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Использование стандартной версии кодировщика мультимедиа Azure для автоматического создания схемы скоростей

## <a name="overview"></a>Обзор

В этом разделе показано, как использовать Media Encoder Standard (MES) для автоматического создания таблицы скоростей и разрешений (пар "скорость-разрешение") на основе разрешения и скорости входных данных. Созданная автоматически предустановка никогда не превышает разрешение и скорость входных данных. Например, если указаны входные данные с разрешением 720p и скоростью 3 Мбит/с, то выходные данные будут иметь разрешение не выше 720p, а их скорости начнутся со значений ниже 3 Мбит/с.

Чтобы использовать эту функцию, при создании задачи кодирования необходимо указать предустановку **Adaptive Streaming**. При использовании предустановки **Adaptive Streaming** кодировщик MES интеллектуально ограничит таблицу скоростей и разрешений. Однако вы не сможете контролировать затраты на кодирование, так как служба определяет, сколько уровней использовать и с каким разрешением. Просмотреть примеры выходных данных слоев, созданных MES в результате кодирования с предустановкой **Adaptive Streaming**, можно в [конце](#output) этого раздела.

## <a id="encoding_with_dotnet"></a>Кодирование с помощью пакета SDK служб мультимедиа для .NET

В следующем примере кода пакет SDK служб мультимедиа используется для выполнения следующих задач.

- Создание задания кодирования.
- Получение ссылки на стандартный кодировщик мультимедиа.
- Добавьте задачу кодирования в задание и укажите предустановку **Adaptive Streaming**. 
- Создание выходного ресурса-контейнера, который будет содержать закодированный ресурс-контейнер.
- Добавление обработчика событий для проверки хода выполнения задания.
- Отправка задания.

        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;

        namespace AdaptiveStreamingMESPresest
        {
            class Program
            {
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];

            // Field for service context.
            private static CloudMediaContext _context = null;
            private static MediaServicesCredentials _cachedCredentials = null;
            static void Main(string[] args)
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                        _mediaServicesAccountName,
                        _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Get an uploaded asset.
                var asset = _context.Assets.FirstOrDefault();

                // Encode and generate the output using the "Adaptive Streaming" preset.
                EncodeToAdaptiveBitrateMP4Set(asset);

                Console.ReadLine();
            }

            static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
            {
                // Declare a new job.
                IJob job = _context.Jobs.Create("Media Encoder Standard Job");

                // Get a media processor reference, and pass to it the name of the 
                // processor to use for the specific task.
                IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

                // Create a task
                ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
                processor,
                "Adaptive Streaming",
                TaskOptions.None);

                // Specify the input asset to be encoded.
                task.InputAssets.Add(asset);
                // Add an output asset to contain the results of the job. 
                // This output is specified as AssetCreationOptions.None, which 
                // means the output asset is not encrypted. 
                task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

                job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                job.Submit();
                job.GetExecutionProgressTask(CancellationToken.None).Wait();

                return job.OutputMediaAssets[0];
            }
            private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
            {
                Console.WriteLine("Job state changed event:");
                Console.WriteLine("  Previous state: " + e.PreviousState);
                Console.WriteLine("  Current state: " + e.CurrentState);
                switch (e.CurrentState)
                {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:

                    // Cast sender as a job.
                    IJob job = (IJob)sender;

                    // Display or log error details as needed.
                    break;
                default:
                    break;
                }
            }
            private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

                if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

                return processor;
            }

            }

        }

## <a id="output"></a>Выходные данные

Этот раздел содержит три примера выходных данных слоев, созданных MES в результате кодирования с предустановкой **Adaptive Streaming**. 

### <a name="example-1"></a>Пример 1
При исходных высоте 1080 и частоте кадров 29,970 было создано 6 слоев видео.

|Слой|Высота:|Ширина|Скорость (кбит/с)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4.|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Пример 2
При исходных высоте 720 и частоте кадров 23,970 было создано 5 слоев видео.

|Слой|Высота:|Ширина|Скорость (кбит/с)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4.|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Пример 3
При исходных высоте 360 и частоте кадров 29,970 было создано 3 слоя видео.

|Слой|Высота:|Ширина|Скорость (кбит/с)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>См. также
[Обзор кодирования с помощью служб мультимедиа](media-services-encode-asset.md)


