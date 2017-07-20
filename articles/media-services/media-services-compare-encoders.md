---
title: "Сравнение кодировщиков мультимедиа Azure по запросу | Документация Майкрософт"
description: "В этом разделе сравниваются возможности кодирования **Media Encoder Standard** и **рабочего процесса Media Encoder Premium**."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 32cbfbc78fbb808ed59142b80602a751f607706d
ms.contentlocale: ru-ru
ms.lasthandoff: 06/29/2017

---

# <a name="comparison-of-azure-on-demand-media-encoders"></a>Сравнение кодировщиков мультимедиа Azure по запросу

В этом разделе сравниваются возможности кодирования **Media Encoder Standard** и **рабочего процесса Media Encoder Premium**.

## <a name="video-and-audio-processing-capabilities"></a>Возможности обработки видео и звука

В следующей таблице сравниваются функциональные возможности Media Encoder Standard (MES) и Media Encoder Premium Workflow (MEPW). 

|Функция|Стандартный кодировщик служб мультимедиа|Расширенный рабочий процесс кодировщика мультимедиа|
|---|---|---|
|Применение условной логики при кодировании<br/>(например, если входные данные в формате HD, то следует кодировать звук в формат 5.1)|Нет|Да|
|Субтитры стандарта|Нет|[Да](media-services-premium-workflow-encoder-formats.md#a-idclosedcaptioningasupport-for-closed-captioning)|
|[Dolby® Professional Loudness Correction](http://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> с Dialogue Intelligence™|Нет|Да|
|Устранение чересстрочности, обратное преобразование видео|базовая;|Качество трансляции|
|Обнаружение и удаление черных границ <br/>(вертикальные и горизонтальные рамки)|Нет|Да|
|Создание эскизов|[Да](media-services-dotnet-generate-thumbnail-with-mes.md)|[Да](media-services-media-encoder-premium-workflow-tutorials.md#a-idthumbnailstomultibitratemp4aadding-thumbnails-to-multibitrate-mp4-output)|
|Обрезание и совмещение видео|[Да](media-services-advanced-encoding-with-mes.md#a-idtrimvideoatrim-a-video-clipping)|Да|
|Наложение звука или видео|[Да](media-services-advanced-encoding-with-mes.md#a-idoverlayacreate-an-overlay)|[Да](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Наложение изображений|Из источников изображений|Из источников изображений и текста|
|Несколько аудиодорожек на разных языках|Ограничено|[Да](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Тарифные единицы, используемые кодировщиками
| Имя обработчика мультимедиа | Применимые цены | Примечания |
| --- | --- | --- |
| **Стандартный кодировщик служб мультимедиа** |КОДИРОВЩИК |Плата за выполнение задач кодирования взимается на основе общей продолжительности (в минутах) всех файлов мультимедиа, созданных в качестве выходных файлов, по тарифу, указанному [здесь][1] в столбце "КОДИРОВЩИК". |
| **Рабочий процесс Media Encoder Premium** |РАСШИРЕННЫЙ КОДИРОВЩИК |Плата за выполнение задач кодирования взимается на основе общей продолжительности (в минутах) всех файлов мультимедиа, созданных в качестве выходных файлов, по тарифу, указанному [здесь][1] в столбце PREMIUM ENCODER (Кодировщик ценовой категории "Премиум"). |

## <a name="input-containerfile-formats"></a>Контейнер ввода и форматы файлов
| Контейнер ввода/ форматы файлов | Стандартный кодировщик служб мультимедиа | Расширенный рабочий процесс кодировщика мультимедиа |
| --- | --- | --- |
| Adobe® Flash® F4V |Да |Да |
| MXF/SMPTE 377M |Да |Да |
| GXF |Да |Да |
| Транспортные потоки MPEG-2 |Да |Да |
| Программные потоки MPEG-2 |Да |Да |
| MPEG-4/MP4 |Да |Да |
| Windows Media/ASF |Да |Да |
| AVI (без сжатия 8 бит/10 бит) |Да |Да |
| 3GPP/3GPP2 |Да |Нет |
| Формат файлов Smooth Streaming (PIFF 1.3) |Да |Нет |
| [Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Да |Нет |
| Matroska/WebM |Да |Нет |
| QuickTime (.mov) |Да |Нет |

## <a name="input-video-codecs"></a>Входные видеокодеки
| Входные видеокодеки | Стандартный кодировщик служб мультимедиа | Расширенный рабочий процесс кодировщика мультимедиа |
| --- | --- | --- |
| AVC 8-разрядный/10-разрядный, до 4:2:2, включая AVCIntra |8 бит 4:2:0 и 4:2:2 |Да |
| Avid DNxHD (в MXF) |Да |Да |
| DVCPro/DVCProHD (в MXF) |Да |Да |
| JPEG2000 |Да |Да |
| MPEG-2 (до 422 Profile и High Level, включая такие варианты, как XDCAM, XDCAM HD, XDCAM IMX, CableLabs® и D10) |До 422 Profile |Да |
| MPEG-1 |Да |Да |
| Windows Media Video/VC-1 |Да |Да |
| Canopus HQ/HQX |Нет |Нет |
| MPEG-4, часть 2 |Да |Нет |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Да |Нет |
| Apple ProRes 422 |Да |Нет |
| Apple ProRes 422 LT |Да |Нет |
| Apple ProRes 422 HQ |Да |Нет |
| Apple ProRes Proxy |Да |Нет |
| Apple ProRes 4444 |Да |Нет |
| Apple ProRes 4444 XQ |Да |Нет |

## <a name="input-audio-codecs"></a>Входные аудиокодеки
| Входные аудиокодеки | Стандартный кодировщик служб мультимедиа | Расширенный рабочий процесс кодировщика мультимедиа |
| --- | --- | --- |
| AES (SMPTE 331M и 302M, AES3-2003) |Нет |Да |
| Dolby® E |Нет |Да |
| Dolby® Digital (AC3) |Нет |Да |
| Dolby® Digital Plus (E-AC3) |Нет |Да |
| AAC (AAC-LC, AAC-HE и AAC-HEv2; до 5.1) |Да |Да |
| MPEG Layer 2 |Да |Да |
| MP3 (MPEG-1 Audio Layer 3) |Да |Да |
| Windows Media Audio |Да |Да |
| WAV/PCM |Да |Да |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Да |Нет |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Да |Нет |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Да |Нет |

## <a name="output-containerfile-formats"></a>Контейнер вывода и форматы файлов
| Контейнер вывода/ форматы файлов | Стандартный кодировщик служб мультимедиа | Расширенный рабочий процесс кодировщика мультимедиа |
| --- | --- | --- |
| Adobe® Flash® F4V |Нет |Да |
| MXF (OP1a, XDCAM и AS02) |Нет |Да |
| DPP (включая AS11) |Нет |Да |
| GXF |Нет |Да |
| MPEG-4/MP4 |Да |Да |
| MPEG-TS |Да |Да |
| Windows Media/ASF |Нет |Да |
| AVI (без сжатия 8 бит/10 бит) |Нет |Да |
| Формат файлов Smooth Streaming (PIFF 1.3) |Нет |Да |

## <a name="output-video-codecs"></a>Выходные видеокодеки
| Выходные видеокодеки | Стандартный кодировщик служб мультимедиа | Расширенный рабочий процесс кодировщика мультимедиа |
| --- | --- | --- |
| AVC (H.264; 8-разрядный; до High Profile, Level 5.2; 4K Ultra HD; AVC Intra) |Только 8 бит 4:2:0 |Да |
| Avid DNxHD (в MXF) |Нет |Да |
| MPEG-2 (до 422 Profile и High Level, включая такие варианты, как XDCAM, XDCAM HD, XDCAM IMX, CableLabs® и D10) |Нет |Да |
| MPEG-1 |Нет |Да |
| Windows Media Video/VC-1 |Нет |Да |
| Создание эскизов JPEG |Да |Да |
| Создание эскизов PNG |Да |Да |
| Создание эскизов BMP |Да |Нет |

## <a name="output-audio-codecs"></a>Выходные аудиокодеки
| Выходные аудиокодеки | Стандартный кодировщик служб мультимедиа | Расширенный рабочий процесс кодировщика мультимедиа |
| --- | --- | --- |
| AES (SMPTE 331M и 302M, AES3-2003) |Нет |Да |
| Dolby® Digital (AC3) |Нет |Да |
| Dolby® Digital Plus (E-AC3) до 7.1 |Нет |Да |
| AAC (AAC-LC, AAC-HE и AAC-HEv2; до 5.1) |Да |Да |
| MPEG Layer 2 |Нет |Да |
| MP3 (MPEG-1 Audio Layer 3) |Нет |Да |
| Windows Media Audio |Нет |Да |

>[!NOTE]
>При кодировании в формат Dolby® Digital (AC3) выходные данные могут записываться только в MP4-файл ISO.

## <a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Связанные статьи
* [Выполняйте расширенные задачи кодирования путем настройки предустановок стандартного кодировщика служб мультимедиа](media-services-custom-mes-presets-with-dotnet.md)
* [Квоты и ограничения](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

