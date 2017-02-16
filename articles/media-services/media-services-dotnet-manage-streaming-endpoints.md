---

title: "Управление конечными точками потоковой передачи с помощью пакета SDK для .NET | Документация Майкрософт"
description: "В этой статье рассказывается, как управлять конечными точками потоковой передачи с помощью портала Azure."
services: media-services
documentationcenter: 
author: Juliako
writer: juliako
manager: erikre
editor: 
ms.assetid: 0da34a97-f36c-48d0-8ea2-ec12584a2215
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 4f61f7c0fd50065d341ae1ce182a033395857579
ms.openlocfilehash: 68011ef634b1f3bdeb7c219a46e1307698a12f7e


---


# <a name="manage-streaming-endpoints-with-net-sdk"></a>Управление конечными точками потоковой передачи с помощью пакета SDK для .NET

>[!NOTE]
>Обязательно просмотрите [обзорную статью](media-services-streaming-endpoints-overview.md). Просмотрите также статью [Конечная точка потоковой передачи](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint).

В этой статье приведен код, в котором показано, как выполнить следующие задачи с помощью пакета SDK служб мультимедиа Azure для .NET:

- изучить конечную точку потоковой передачи по умолчанию;
- создать и добавить конечную точку потоковой передачи.

    Может потребоваться наличие нескольких конечных точек потоковой передачи, если вы планируете использовать разные сети CDN (или сеть CDN) и прямой доступ.

    > [!NOTE]
    > Плата взимается, только когда конечная точка потоковой передачи используется.
    
- Обновите конечную точку потоковой передачи.
    
    Обязательно вызовите функцию Update().

- Удалите конечную точку потоковой передачи.

    >[!NOTE]
    >Конечную точку потоковой передачи по умолчанию удалить нельзя.

Сведения о том, как масштабировать конечную точку потоковой передачи, см. [здесь](media-services-portal-scale-streaming-endpoints.md).


###<a name="set-up-the-visual-studio-project"></a>Установка проекта Visual Studio

1. Создайте в Visual Studio 2015 консольное приложение C#.  
2. Выполните сборку решения.
3. Используйте **NuGet** для установки [последней версии пакета SDK служб мультимедиа Azure для .NET](https://www.nuget.org/packages/windowsazure.mediaservices/).   
4. Добавьте раздел appSettings в CONFIG-файл и обновите имя служб мультимедиа и значения ключей. 
    
        <appSettings>
          <add key="MediaServicesAccountName" value="Media-Services-Account-Name"/>
          <add key="MediaServicesAccountKey" value="Media-Services-Account-Key"/>
        </appSettings>

###<a name="add-code-that-manages-streaming-endpoints"></a>Добавление кода, который управляет конечными точками потоковой передачи
    
Замените код файла Program.cs на код, приведенный ниже.

    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using Microsoft.WindowsAzure.MediaServices.Client.Live;
    
    namespace AMSStreamingEndpoint
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
                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);
    
                var defaultStreamingEndpoint = _context.StreamingEndpoints.Where(s=>s.Name.Contains("default")).FirstOrDefault();
                ExamineStreamingEndpoint(defaultStreamingEndpoint);
    
                IStreamingEndpoint newStreamingEndpoint = AddStreamingEndpoint();
                UpdateStreamingEndpoint(newStreamingEndpoint);
                DeleteStreamingEndpoint(newStreamingEndpoint);
            }
    
            static public void ExamineStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
            {
                Console.WriteLine(streamingEndpoint.Name);
                Console.WriteLine(streamingEndpoint.StreamingEndpointVersion);
                Console.WriteLine(streamingEndpoint.FreeTrialEndTime);
                Console.WriteLine(streamingEndpoint.ScaleUnits);
                Console.WriteLine(streamingEndpoint.CdnProvider);
                Console.WriteLine(streamingEndpoint.CdnProfile);
                Console.WriteLine(streamingEndpoint.CdnEnabled);
            }
    
            static public IStreamingEndpoint AddStreamingEndpoint()
            {
                var name = "StreamingEndpoint" + DateTime.UtcNow.ToString("hhmmss");
                var option = new StreamingEndpointCreationOptions(name, 1)
                {
                    StreamingEndpointVersion = new Version("2.0"),
                    CdnEnabled = true,
                    CdnProfile = "CdnProfile",
                    CdnProvider = CdnProviderType.PremiumVerizon
                };
    
                var streamingEndpoint = _context.StreamingEndpoints.Create(option);
    
                return streamingEndpoint;
            }
    
            static public void UpdateStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
            {
                if(streamingEndpoint.StreamingEndpointVersion == "1.0")
                    streamingEndpoint.StreamingEndpointVersion = "2.0";
    
                streamingEndpoint.CdnEnabled = false;
                streamingEndpoint.Update();
            }
    
            static public void DeleteStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
            {
                streamingEndpoint.Delete();
            }
        }
    }
    

## <a name="next-steps"></a>Дальнейшие действия
Просмотрите схемы обучения работе со службами мультимедиа.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO2-->


