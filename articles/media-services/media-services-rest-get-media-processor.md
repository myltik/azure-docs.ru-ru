---
title: Создание обработчика мультимедиа | Microsoft Docs
description: Узнайте, как создать компонент обработчика мультимедиа для кодирования, преобразования формата, шифрования или расшифровки мультимедийного контента служб мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako

---
# <a name="how-to:-get-a-media-processor-instance"></a>Получение экземпляра процессора мультимедиа
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Обзор
В службах мультимедиа обработчик мультимедиа является компонентом, который работает со специфическими задачами обработки, такими как кодирование, преобразование формата, шифрование или расшифровка мультимедийного контента. Обработчик мультимедиа обычно создается при создании задачи для кодирования, шифрования или преобразования формата мультимедийного контента.

В приведенной ниже таблице указаны имена и описания для всех доступных обработчиков мультимедиа.

| Имя обработчика мультимедиа | Описание | Дополнительные сведения |
| --- | --- | --- |
| Стандартный кодировщик служб мультимедиа |Предоставляет стандартные возможности для кодирования по запросу. |[Обзор и сравнение кодировщиков мультимедиа Azure по запросу](media-services-encode-asset.md) |
| Расширенный рабочий процесс кодировщика мультимедиа |Позволяет выполнять задачи кодирования с использованием расширенного рабочего процесса кодировщика мультимедиа. |[Обзор и сравнение кодировщиков мультимедиа Azure по запросу](media-services-encode-asset.md) |
| Azure Media Indexer |Позволяет сделать мультимедийные файлы и контент доступными для поиска, а также создавать дорожки и ключевые слова для субтитров. |[Azure Media Indexer](media-services-index-content.md) |
| Azure Media Hyperlapse (предварительная версия) |Позволяет сгладить "неровности" видео с помощью стабилизации видео. Также позволяет ускорить содержимое в виде пригодного к использованию клипа. |[Azure Media Hyperlapse](media-services-hyperlapse-content.md) |
| Кодировщик мультимедиа Azure |Цена снижена | |
| Расшифровка хранилища |Цена снижена | |
| Azure Media Packager |Цена снижена | |
| Azure Media Encryptor |Цена снижена | |

## <a name="get-mediaprocessor"></a>Получение MediaProcessor
> [!NOTE]
> При работе с REST API служб мультимедиа следует руководствоваться следующими рекомендациями.
> 
> При доступе к сущностям в службах мультимедиа необходимо задать определенные поля и значения заголовков в HTTP-запросах. Дополнительную информацию см. в статье [Обзор интерфейса REST API служб мультимедиа](media-services-rest-how-to-use.md).
> 
> После успешного подключения к https://media.windows.net вы получите ошибку 301 (перенаправление), в которой будет указан другой URI служб мультимедиа. Последующие вызовы необходимо осуществлять к новому универсальному коду ресурса (URI), как описано в статье [Подключение к службам мультимедиа с помощью REST API](media-services-rest-connect-programmatically.md). 
> 
> 

Следующий вызов REST показывает, как получить экземпляр обработчика мультимедиа по имени (в данном случае это **стандартный кодировщик мультимедиа**). 

Запрос:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.11
    Host: media.windows.net

Ответ:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы знаете, как получить экземпляр обработчика мультимедиа, перейдите в раздел [Кодировка актива](media-services-rest-get-started.md) , в котором будет показано, как использовать Media Encoder Standard для кодирования ресурса-контейнера.

<!--HONumber=Oct16_HO2-->


