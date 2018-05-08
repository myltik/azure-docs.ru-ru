---
title: Сравнение кодировщиков мультимедиа Azure по запросу | Документация Майкрософт
description: В этом разделе сравниваются возможности кодирования **Media Encoder Standard** и **рабочего процесса Media Encoder Premium**.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 13e8b3bcb7de4093116b3e2198b210950ac16d78
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Сравнение кодировщиков мультимедиа Azure по запросу

В этом разделе сравниваются возможности кодирования **Media Encoder Standard** и **рабочего процесса Media Encoder Premium**.

## <a name="video-and-audio-processing-capabilities"></a>Возможности обработки видео и звука

В следующей таблице сравниваются функциональные возможности Media Encoder Standard (MES) и Media Encoder Premium Workflow (MEPW). 

|Функция|Стандартный кодировщик служб мультимедиа|Расширенный рабочий процесс кодировщика мультимедиа|
|---|---|---|
|Применение условной логики при кодировании<br/>(например, если входные данные в формате HD, то следует кодировать звук в формат 5.1)|Нет |Yes|
|Субтитры стандарта|Нет |[Да](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Dolby® Professional Loudness Correction](http://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> с Dialogue Intelligence™|Нет |Yes|
|Устранение чересстрочности, обратное преобразование видео|базовая;|Качество трансляции|
|Обнаружение и удаление черных границ <br/>(вертикальные и горизонтальные рамки)|Нет |Yes|
|Создание эскизов|[Да](media-services-dotnet-generate-thumbnail-with-mes.md)|[Да](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Обрезание и совмещение видео|[Да](media-services-advanced-encoding-with-mes.md#trim_video)|Yes|
|Наложение звука или видео|[Да](media-services-advanced-encoding-with-mes.md#overlay)|[Да](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Наложение изображений|Из источников изображений|Из источников изображений и текста|
|Несколько аудиодорожек на разных языках|Ограничено|[Да](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Тарифные единицы, используемые кодировщиками
| Имя обработчика мультимедиа | Применимые цены | Заметки |
| --- | --- | --- |
| **Стандартный кодировщик служб мультимедиа** |КОДИРОВЩИК |Плата за выполнение задач кодирования взимается на основе общей продолжительности (в минутах) всех файлов мультимедиа, созданных в качестве выходных файлов, по тарифу, указанному [здесь][1] в столбце "КОДИРОВЩИК". |
| **Расширенный рабочий процесс кодировщика мультимедиа** |РАСШИРЕННЫЙ КОДИРОВЩИК |Плата за выполнение задач кодирования взимается на основе общей продолжительности (в минутах) всех файлов мультимедиа, созданных в качестве выходных файлов, по тарифу, указанному [здесь][1] в столбце PREMIUM ENCODER (Кодировщик ценовой категории "Премиум"). |

## <a name="input-containerfile-formats"></a>Контейнер ввода и форматы файлов
| Контейнер ввода/ форматы файлов | Стандартный кодировщик служб мультимедиа | Расширенный рабочий процесс кодировщика мультимедиа |
| --- | --- | --- |
| Adobe® Flash® F4V |Yes |Yes |
| MXF/SMPTE 377M |Yes |Yes |
| GXF |Yes |Yes |
| Транспортные потоки MPEG-2 |Yes |Yes |
| Программные потоки MPEG-2 |Yes |Yes |
| MPEG-4/MP4 |Yes |Yes |
| Windows Media/ASF |Yes |Yes |
| AVI (без сжатия 8 бит/10 бит) |Yes |Yes |
| 3GPP/3GPP2 |Yes |Нет  |
| Формат файлов Smooth Streaming (PIFF 1.3) |Yes |Нет  |
| [Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Yes |Нет  |
| Matroska/WebM |Yes |Нет  |
| QuickTime (.mov) |Yes |Нет  |

## <a name="input-video-codecs"></a>Входные видеокодеки
| Входные видеокодеки | Стандартный кодировщик служб мультимедиа | Расширенный рабочий процесс кодировщика мультимедиа |
| --- | --- | --- |
| AVC 8-разрядный/10-разрядный, до 4:2:2, включая AVCIntra |8 бит 4:2:0 и 4:2:2 |Yes |
| Avid DNxHD (в MXF) |Yes |Yes |
| DVCPro/DVCProHD (в MXF) |Yes |Yes |
| JPEG2000 |Yes |Yes |
| MPEG-2 (до 422 Profile и High Level, включая такие варианты, как XDCAM, XDCAM HD, XDCAM IMX, CableLabs® и D10) |До 422 Profile |Yes |
| MPEG-1 |Yes |Yes |
| Windows Media Video/VC-1 |Yes |Yes |
| Canopus HQ/HQX |Нет  |Нет  |
| MPEG-4, часть 2 |Yes |Нет  |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Yes |Нет  |
| Apple ProRes 422 |Yes |Нет  |
| Apple ProRes 422 LT |Yes |Нет  |
| Apple ProRes 422 HQ |Yes |Нет  |
| Apple ProRes Proxy |Yes |Нет  |
| Apple ProRes 4444 |Yes |Нет  |
| Apple ProRes 4444 XQ |Yes |Нет  |

## <a name="input-audio-codecs"></a>Входные аудиокодеки
| Входные аудиокодеки | Стандартный кодировщик служб мультимедиа | Расширенный рабочий процесс кодировщика мультимедиа |
| --- | --- | --- |
| AES (SMPTE 331M и 302M, AES3-2003) |Нет  |Yes |
| Dolby® E |Нет  |Yes |
| Dolby® Digital (AC3) |Нет  |Yes |
| Dolby® Digital Plus (E-AC3) |Нет  |Yes |
| AAC (AAC-LC, AAC-HE и AAC-HEv2; до 5.1) |Yes |Yes |
| MPEG Layer 2 |Yes |Yes |
| MP3 (MPEG-1 Audio Layer 3) |Yes |Yes |
| Windows Media Audio |Yes |Yes |
| WAV/PCM |Yes |Yes |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Yes |Нет  |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Yes |Нет  |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Yes |Нет  |

## <a name="output-containerfile-formats"></a>Контейнер вывода и форматы файлов
| Контейнер вывода/ форматы файлов | Стандартный кодировщик служб мультимедиа | Расширенный рабочий процесс кодировщика мультимедиа |
| --- | --- | --- |
| Adobe® Flash® F4V |Нет  |Yes |
| MXF (OP1a, XDCAM и AS02) |Нет  |Yes |
| DPP (включая AS11) |Нет  |Yes |
| GXF |Нет  |Yes |
| MPEG-4/MP4 |Yes |Yes |
| MPEG-TS |Yes |Yes |
| Windows Media/ASF |Нет  |Yes |
| AVI (без сжатия 8 бит/10 бит) |Нет  |Yes |
| Формат файлов Smooth Streaming (PIFF 1.3) |Нет  |Yes |

## <a name="output-video-codecs"></a>Выходные видеокодеки
| Выходные видеокодеки | Стандартный кодировщик служб мультимедиа | Расширенный рабочий процесс кодировщика мультимедиа |
| --- | --- | --- |
| AVC (H.264; 8-разрядный; до High Profile, Level 5.2; 4K Ultra HD; AVC Intra) |Только 8 бит 4:2:0 |Yes |
| HEVC (H.265; 8-разрядный и 10-разрядный)  |Нет  |Yes |
| Avid DNxHD (в MXF) |Нет  |Yes |
| MPEG-2 (до 422 Profile и High Level, включая такие варианты, как XDCAM, XDCAM HD, XDCAM IMX, CableLabs® и D10) |Нет  |Yes |
| MPEG-1 |Нет  |Yes |
| Windows Media Video/VC-1 |Нет  |Yes |
| Создание эскизов JPEG |Yes |Yes |
| Создание эскизов PNG |Yes |Yes |
| Создание эскизов BMP |Yes |Нет  |

## <a name="output-audio-codecs"></a>Выходные аудиокодеки
| Выходные аудиокодеки | Стандартный кодировщик служб мультимедиа | Расширенный рабочий процесс кодировщика мультимедиа |
| --- | --- | --- |
| AES (SMPTE 331M и 302M, AES3-2003) |Нет  |Yes |
| Dolby® Digital (AC3) |Нет  |Yes |
| Dolby® Digital Plus (E-AC3) до 7.1 |Нет  |Yes |
| AAC (AAC-LC, AAC-HE и AAC-HEv2; до 5.1) |Yes |Yes |
| MPEG Layer 2 |Нет  |Yes |
| MP3 (MPEG-1 Audio Layer 3) |Нет  |Yes |
| Windows Media Audio |Нет  |Yes |

>[!NOTE]
>При кодировании в формат Dolby® Digital (AC3) выходные данные могут записываться только в MP4-файл ISO.

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Связанные статьи
* [Выполняйте расширенные задачи кодирования путем настройки предустановок стандартного кодировщика служб мультимедиа](media-services-custom-mes-presets-with-dotnet.md)
* [Квоты и ограничения](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
