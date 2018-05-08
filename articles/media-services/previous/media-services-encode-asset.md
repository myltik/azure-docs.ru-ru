---
title: Обзор и сравнение кодировщиков мультимедиа Azure по запросу | Документация Майкрософт
description: В этой статье приводится обзор и сравнение кодировщиков мультимедиа Azure по запросу.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: e6bfc068-fa46-4d68-b1ce-9092c8f3a3c9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: juliako
ms.openlocfilehash: 6e44cff930f943f5cf30fb5335ad6a7fd1d08ab5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Обзор и сравнение кодировщиков мультимедиа Azure по запросу
## <a name="encoding-overview"></a>Общие сведения о Службе кодирования
Службы мультимедиа Azure предоставляют несколько вариантов для кодирования мультимедиа в облаке.

При начале работы со службами мультимедиа важно понимать разницу между кодеками и форматами файлов.
Кодеки — это программное обеспечение, которое реализует алгоритмы сжатия и распаковки, тогда как форматы файлов являются контейнерами, в которых хранится сжатое видео.

Службы мультимедиа обеспечивают динамическую упаковку, которая позволяет доставлять закодированное содержимое MP4-файлов с переменной скоростью или Smooth Streaming в форматах потоковой передачи с поддержкой служб мультимедиа (MPEG-DASH, HLS, Smooth Streaming) без необходимости повторной упаковки в эти форматы потоковой передачи.

>[!NOTE]
>При создании учетной записи AMS в нее добавляется конечная точка потоковой передачи **по умолчанию** в состоянии **Остановлена**. Чтобы начать потоковую передачу содержимого и воспользоваться динамической упаковкой и динамическим шифрованием, конечная точка потоковой передачи, из которой необходимо выполнять потоковую передачу содержимого, должна находиться в состоянии **Выполняется**. Чтобы воспользоваться преимуществом [динамической упаковки](media-services-dynamic-packaging-overview.md), необходимо выполнить следующие действия:
>
>Кроме того, закодируйте исходный файл в набор MP4-файлов с переменной скоростью или в набор файлов Smooth Streaming (шаги кодирования показаны далее в этом руководстве).

Службы мультимедиа поддерживают следующие кодировщики по запросу, описанные в этом разделе.

* [Стандартный кодировщик служб мультимедиа](media-services-encode-asset.md#media-encoder-standard)
* [Расширенный рабочий процесс кодировщика мультимедиа](media-services-encode-asset.md#media-encoder-premium-workflow)

В этом разделе предоставлен краткий обзор кодировщиков мультимедиа по запросу, а также приведены ссылки на разделы с подробными сведениями. В этой статье также приводится сравнение кодировщиков.

>[!NOTE]
>По умолчанию каждая учетная запись служб мультимедиа может выполнять одну активную задачу кодирования в текущий момент. Можно зарезервировать единицы кодирования, которые позволят выполнять несколько задач кодирования одновременно, по одной для каждой приобретенной единицы. Дополнительные сведения см. в статье [Обзор масштабирования обработка мультимедиа](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Стандартный кодировщик служб мультимедиа
### <a name="how-to-use"></a>Использование
[Кодирование с помощью стандартного кодировщика мультимедиа](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Форматы
[Форматы и кодеки](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Предустановки
Стандартный кодировщик мультимедиа настраивается с помощью одной из предустановок кодировщика, описанных [здесь](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Входные и выходные метаданные
Входные метаданные кодировщиков описаны [здесь](media-services-input-metadata-schema.md).

Выходные метаданные кодировщиков описаны [здесь](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Создание эскизов
Дополнительные сведения см. в разделе о [создании эскизов с помощью стандартного кодировщика служб мультимедиа](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Усечение видео (обрезка)
Дополнительные сведения см. в разделе об [обрезке видео с помощью стандартного кодировщика мультимедиа](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Создание наложений
Дополнительные сведения см. в разделе о [создании наложения с помощью стандартного кодировщика мультимедиа](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>См. также
[Блог, посвященный службам мультимедиа](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Расширенный рабочий процесс кодировщика мультимедиа
### <a name="overview"></a>Обзор
[Знакомство со Службой кодирования категории "Премиум" в службах мультимедиа Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Использование
Рабочий процесс Premium кодировщика мультимедиа настраивается с помощью сложных рабочих процессов. Файлы рабочих процессов можно создавать и обновлять с помощью [конструктора рабочих процессов](media-services-workflow-designer.md) .

[Использование Службы кодирования категории "Премиум" в службах мультимедиа Azure](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Известные проблемы
Если входящее видео не содержит скрытых субтитров, выходящий ресурс по-прежнему будет содержать пустой файл TTML.


## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Связанные статьи
* [Выполняйте расширенные задачи кодирования путем настройки предустановок стандартного кодировщика служб мультимедиа](media-services-custom-mes-presets-with-dotnet.md)
* [Квоты и ограничения](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
