---
title: Схема Media Encoder Standard | Документация Майкрософт
description: Эта статья содержит обзор схемы Media Encoder Standard.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 4c060062-8ef2-41d9-834e-e81e8eafcf2e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako
ms.openlocfilehash: 346d7aecb6a4295f8ceb64bc1b5c6494b7b41bfd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783893"
---
# <a name="media-encoder-standard-schema"></a>Схема Media Encoder Standard
В этой статье описаны некоторые элементы и типы схемы XML, на которых основаны [предустановки Media Encoder Standard](media-services-mes-presets-overview.md). Также рассмотрены элементы и их допустимые значения.  

## <a name="Preset"></a> Предустановка (корневой элемент)
Определяет предустановку кодирования.  

### <a name="elements"></a>Элементы
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **Кодирование** |[Кодирование](media-services-mes-schema.md#Encoding) |Корневой элемент, который указывает, что источники входных данных должны быть закодированы. |
| **Выходные данные** |[Выходные данные](media-services-mes-schema.md#Output) |Коллекция требуемых выходных файлов. |

### <a name="attributes"></a>Атрибуты
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **Версия**<br/><br/> Обязательно |**xs: decimal** |Версия предустановки. Применяются следующие ограничения: xs:fractionDigits value="1" и xs:minInclusive value="1". Например, **version="1.0"**. |

## <a name="Encoding"></a> Кодирование
Содержит последовательность следующих элементов.  

### <a name="elements"></a>Элементы
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Параметры кодирования видео в формате H.264. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Параметры кодирования аудио в формате AAC. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Параметры изображения в формате BMP. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Параметры изображения в формате PNG. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Параметры изображения в формате JPG. |

## <a name="H264Video"></a> H264Video
### <a name="elements"></a>Элементы
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs="0" |**xs:boolean** |Сейчас поддерживается только однопроходное кодирование. |
| **KeyFrameInterval**<br/><br/> minOccurs="0"<br/><br/> **default="00:00:02"** |**xs:time** |Определяет фиксированный интервал между кадрами IDR в секундах. Этот параметр также называют длительностью GOP. В разделе **SceneChangeDetection** описывается, как указать, может ли кодировщик отклоняться от этого значения. |
| **SceneChangeDetection**<br/><br/> minOccurs="0"<br/><br/> default=”false” |**xs: boolean** |Если задано значение true, кодировщик пытается обнаружить изменение сцены в видео и вставить кадр IDR. |
| **Complexity**<br/><br/> minOccurs="0"<br/><br/> default="Balanced" |**xs:string** |Регулирует баланс между скоростью кодирования и качеством видео. Может принимать одно из следующих значений: **Speed**, **Balanced** и **Quality**.<br/><br/> Значение по умолчанию: **Balanced**. |
| **SyncMode**<br/><br/> minOccurs="0" | |Функция будет доступна в будущем выпуске. |
| **H264Layers**<br/><br/> minOccurs="0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Коллекция уровней выходных видео. |

### <a name="attributes"></a>Атрибуты
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **Condition** |**xs:string** | Если входные данные не содержат видео, может потребоваться указать кодировщику принудительно вставлять монохромную видеодорожку. Чтобы сделать это, добавьте Condition="InsertBlackIfNoVideoBottomLayerOnly" (для вставки видео только с наименьшей скоростью) или Condition="InsertBlackIfNoVideo" (для вставки видео с каждой из выходных скоростей). Дополнительные сведения см. в [этой статье](media-services-advanced-encoding-with-mes.md#no_video).|

## <a name="H264Layers"></a> H264Layers

По умолчанию в случае отправки кодировщику входных данных, которые содержат только звук и не содержат видео, выходной ресурс-контейнер содержит файлы только с аудиоданными. Некоторые проигрыватели не смогут обработать такие выходные потоки. Можно указать атрибут **InsertBlackIfNoVideo** H264Video, чтобы кодировщик принудительного добавлял видеодорожку в выходные данные в этом сценарии. Дополнительные сведения см. в [этой статье](media-services-advanced-encoding-with-mes.md#no_video).
              
### <a name="elements"></a>Элементы
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[H264Layer](media-services-mes-schema.md#H264Layer) |Коллекция уровней H264. |

## <a name="H264Layer"></a> H264Layer
> [!NOTE]
> Ограничения видео основаны на значениях, описанных в таблице [уровней H264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels).  
> 
> 

### <a name="elements"></a>Элементы
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **Профиль**<br/><br/> minOccurs="0"<br/><br/> default=”Auto” |**xs: string** |Может иметь одно из следующих значений **xs:string**: **Auto**, **Baseline**, **Main**, **High**. |
| **Level**<br/><br/> minOccurs="0"<br/><br/> default=”Auto” |**xs: string** | |
| **Bitrate**<br/><br/> minOccurs="0" |**xs:int** |Скорость, используемая для этого уровня видео, указанная в Кбит/с. |
| **MaxBitrate**<br/><br/> minOccurs="0" |**xs: int** |Максимальная скорость, используемая для этого уровня видео, указанная в Кбит/с. |
| **BufferWindow**<br/><br/> minOccurs="0"<br/><br/> default="00:00:05" |**xs: time** |Длина буфера видео. |
| **Width**<br/><br/> minOccurs="0" |**xs: int** |Ширина кадра выходного видео в пикселях.<br/><br/> Сейчас необходимо указать и ширину, и высоту. Значения ширины и высоты должны быть четными числами. |
| **Height**<br/><br/> minOccurs="0" |**xs:int** |Высота кадра выходного видео в пикселях.<br/><br/> Сейчас необходимо указать и ширину, и высоту. Значения ширины и высоты должны быть четными числами.|
| **BFrames**<br/><br/> minOccurs="0" |**xs: int** |Число кадров B между опорными кадрами. |
| **ReferenceFrames**<br/><br/> minOccurs="0"<br/><br/> default=”3” |**xs:int** |Число опорных кадров в GOP. |
| **EntropyMode**<br/><br/> minOccurs="0"<br/><br/> default=”Cabac” |**xs: string** |Может принимать одно из следующих значений: **Cabac** и **Cavlc**. |
| **FrameRate**<br/><br/> minOccurs="0" |Рациональное число |Определяет частоту кадров выходного видео. Используйте стандартное значение "0/1", чтобы кодировщик использовал ту же частоту кадров, что и выходное видео. Допустимые значения должны быть стандартными для частоты видеокадров. Но допустимо любое рациональное число. Например, значение 1/1 — это 1 кадр/с. Оно является допустимым.<br/><br/> - 12/1 (12 кадров/с)<br/><br/> - 15/1 (15 кадров/с)<br/><br/> - 24/1 (24 кадра/с)<br/><br/> - 24 000/1001 (23,976 кадра/с)<br/><br/> - 25/1 (25 кадров/с)<br/><br/>  - 30/1 (30 кадров/с)<br/><br/> - 30 000/1001 (29,97 кадра/с) <br/> <br/>**Примечание.** При создании пользовательской предустановки для кодирования с несколькими скоростями все уровни предустановки **должны** использовать одно и то же значение частоты кадров.|
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**xs: boolean** |Копия из кодировщика мультимедиа Azure |
| **Slices**<br/><br/> minOccurs="0"<br/><br/> default="0" |**xs:int** |Определяет число фрагментов для разделенного кадра. Рекомендуется использовать значение по умолчанию. |

## <a name="AACAudio"></a> AACAudio
 Содержит последовательность следующих элементов и групп.  

 См. дополнительные сведения о стандарте [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Элементы
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **Профиль**<br/><br/> minOccurs="0 "<br/><br/> default="AACLC" |**xs: string** |Может принимать одно из следующих значений: **AACLC**, **HEAACV1** или **HEAACV2**. |

### <a name="attributes"></a>Атрибуты
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **Condition** |**xs: string** |Чтобы заставить кодировщик создать ресурс, содержащий звуковую дорожку с тишиной, когда входные данные не содержат звука, укажите значение "InsertSilenceIfNoAudio".<br/><br/> По умолчанию при отправке кодировщику входных данных, которые содержат только видео и не содержат звука, выходной ресурс-контейнер будет содержать файлы только с видеоданными. Некоторые проигрыватели не смогут обработать такие выходные потоки. Этот параметр можно использовать для принудительного добавления кодировщиком звуковой дорожки с тишиной к выходным данным в этом сценарии. |

### <a name="groups"></a>Группы
| Справочные материалы | ОПИСАНИЕ |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |См. описание [AudioGroup](media-services-mes-schema.md#AudioGroup), чтобы узнать необходимое число каналов, частоту выборки и скорость потока, которые можно установить для каждого профиля. |

## <a name="AudioGroup"></a> AudioGroup
Дополнительные сведения о допустимых значениях для каждого профиля см. в таблице "Сведения об аудиокодеке" ниже.  

### <a name="elements"></a>Элементы
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **Channels**<br/><br/> minOccurs="0" |**xs: int** |Число закодированных аудиоканалов. Допустимые значения: 1, 2, 5, 6, 8.<br/><br/> Значение по умолчанию: 2. |
| **SamplingRate**<br/><br/> minOccurs="0" |**xs: int** |Частота дискретизации, указанная в Гц. |
| **Bitrate**<br/><br/> minOccurs="0" |**xs: int** |Скорость кодировки аудио, указанная в Кбит/с. |

### <a name="audio-codec-details"></a>Сведения об аудиокодеке
Аудиокодек|Сведения  
-----------------|---  
**AACLC**|1:<br/><br/> - 11 025 : 8 &lt;= скорость &lt; 16<br/><br/> - 12 000 : 8 &lt;= скорость &lt; 16<br/><br/> - 16 000 : 8 &lt;= скорость &lt;32<br/><br/>- 22 050 : 24 &lt;= скорость &lt; 32<br/><br/> - 24 000 : 24 &lt;= скорость &lt; 32<br/><br/> - 32 000 : 32 &lt;= скорость &lt; 192<br/><br/> - 44 100 : 56 &lt;= скорость &lt; 288<br/><br/> - 48 000 : 56 &lt;= скорость &lt;288<br/><br/> - 88 200 : 128 &lt;= скорость &lt; 288<br/><br/> - 96 000 : 128 &lt;= скорость &lt; 288<br/><br/> 2.<br/><br/> - 11 025 : 16 &lt;= скорость &lt; 24<br/><br/> - 12 000 : 16 &lt;= скорость &lt; 24<br/><br/> - 16 000 : 16 &lt;= скорость &lt; 40<br/><br/> - 22 050 : 32 &lt;= скорость &lt; 40<br/><br/> - 24 000 : 32 &lt;= скорость &lt; 40<br/><br/> - 32 000 : 40 &lt;= скорость &lt; 384<br/><br/> - 44 100 : 96 &lt;= скорость &lt; 576<br/><br/> - 48 000 : 96 &lt;= скорость &lt; 576<br/><br/> - 88 200 : 256 &lt;= скорость &lt; 576<br/><br/> - 96 000 : 256 &lt;= скорость &lt; 576<br/><br/> 5/6:<br/><br/> - 32 000 : 160 &lt;= скорость &lt; 896<br/><br/> - 44 100 : 240 &lt;= скорость &lt; 1024<br/><br/> - 48 000 : 240 &lt;= скорость &lt; 1024<br/><br/> - 88 200 : 640 &lt;= скорость &lt; 1024<br/><br/> - 96 000 : 640 &lt;= скорость &lt; 1024<br/><br/> 8:<br/><br/> - 32 000 : 224 &lt;= скорость &lt; 1024<br/><br/> - 44 100 : 384 &lt;= скорость &lt; 1024<br/><br/> - 48 000 : 384 &lt;= скорость &lt; 1024<br/><br/> - 88 200 : 896 &lt;= скорость &lt; 1024<br/><br/> - 96 000 : 896 &lt;= скорость &lt; 1024  
**HEAACV1**|1:<br/><br/> - 22 050 : скорость = 8<br/><br/> - 24 000 : 8 &lt;= скорость &lt; 10<br/><br/> - 32 000 : 12 &lt;= скорость &lt; 64<br/><br/> - 44 100 : 20 &lt;= скорость &lt; 64<br/><br/> - 48 000 : 20 &lt;= скорость &lt; 64<br/><br/> - 88 200 : скорость = 64<br/><br/> 2.<br/><br/> - 32 000 : 16 &lt;= скорость &lt;= 128<br/><br/> - 44 100 : 16 &lt;= скорость &lt;= 128<br/><br/> - 48 000 : 16 &lt;= скорость &lt;= 128<br/><br/> - 88 200 : 96 &lt;= скорость &lt;= 128<br/><br/> - 96 000 : 96 &lt;= скорость &lt;= 128<br/><br/> 5/6:<br/><br/> - 32 000 : 64 &lt;= скорость &lt;= 320<br/><br/> - 44 100 : 64 &lt;= скорость &lt;= 320<br/><br/> - 48 000 : 64 &lt;= скорость &lt;= 320<br/><br/> - 88 200 : 256 &lt;= скорость &lt;= 320<br/><br/> - 96 000 : 256 &lt;= скорость &lt;= 320<br/><br/> 8:<br/><br/> - 32 000 : 96 &lt;= скорость &lt;= 448<br/><br/> - 44 100 : 96 &lt;= скорость &lt;= 448<br/><br/> - 48 000 : 96 &lt;= скорость &lt;= 448<br/><br/> - 88 200 : 384 &lt;= скорость &lt;= 448<br/><br/> - 96 000 : 384 &lt;= скорость &lt;= 448  
**HEAACV2**|2.<br/><br/> - 22 050 : 8 &lt;= скорость &lt;= 10<br/><br/> - 24 000 : 8 &lt;= скорость &lt; 10<br/><br/> - 32 000 : 12 &lt;= скорость &lt; 64<br/><br/> - 44 100 : 20 &lt;= скорость &lt; 64<br/><br/> - 48 000 : 20 &lt;= скорость &lt; 64<br/><br/> - 88 200 : 64 &lt;= скорость &lt;= 64  
  

## <a name="Clip"></a> Клип
### <a name="attributes"></a>Атрибуты
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **StartTime** |**xs:duration** |Указывает время начала презентации. Значение StartTime должно соответствовать абсолютной метке времени входящего видео. Например, если первый кадр входящего видео имеет метку времени 12:00:10.000, значение StartTime должно было равно или больше 12:00:10.000. |
| **Duration** |**xs:duration** |Указывает продолжительность презентации (например, внешний вид наложения в видео). |

## <a name="Output"></a> Выходные данные
### <a name="attributes"></a>Атрибуты
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **FileName** |**xs:string** |Имя выходного файла.<br/><br/> Для создания имен выходных файлов можно использовать макросы, описанные в следующей таблице. Например: <br/><br/> **"Outputs": [      {       "FileName": "{Basename}*{Resolution}*{Bitrate}.mp4",       "Format": {         "Type": "MP4Format"       }     }   ]** |

### <a name="macros"></a>Макросы
| Макрос | ОПИСАНИЕ |
| --- | --- |
| **{Basename}** |Если вы кодируете видео по запросу, макрос {Basename} представляет первые 32 символа свойства AssetFile.Name основного файла во входном ресурсе-контейнере.<br/><br/> Если входной ресурс-контейнер — это динамический архив, макрос {Basename} является производным от атрибутов trackName в манифесте сервера. Если вы отправляете задание субклипа с помощью TopBitrate, как в <VideoStream\>TopBitrate</VideoStream\>, и выходной файл содержит видео, макрос {Basename} представляет первые 32 символа trackName уровня видео с наивысшей скоростью.<br/><br/> Если вы отправляете задание субклипа с помощью входящей скорости, как в <VideoStream\>*</VideoStream\>, и выходной файл содержит видео, макрос {Basename} представляет первые 32 символа соответствующего уровня видео. |
| **{Codec}** |Соответствует формату H.264 для видео и AAC для аудио. |
| **{Bitrate}** |Целевая скорость видео, если выходной файл содержит видео и аудио, или целевая скорость аудио, если выходной файл содержит только аудио. Используется значение скорости в Кбит/с. |
| **{Channel}** |Счетчик аудиоканала, если файл содержит аудио. |
| **{Width}** |Ширина видео в пикселях в выходном файле, если файл содержит видео. |
| **{Height}** |Высота видео в пикселях в выходном файле, если файл содержит видео. |
| **{Extension}** |Наследует свойство Type для выходного файла. Имя выходного файла имеет одно из следующих расширений: MP4, TS, JPG, PNG или BMP. |
| **{Index}** |Обязателен для эскиза. Должен быть представлен только один раз. |

## <a name="Video"></a> Видео (сложный тип, который наследуется из кодека)
### <a name="attributes"></a>Атрибуты
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **Начало** |**xs:string** | |
| **Step** |**xs:string** | |
| **Range** |**xs:string** | |
| **PreserveResolutionAfterRotation** |**xs:boolean** |Подробное описание см. в следующем разделе: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation). |

### <a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
Рекомендуется использовать флаг **PreserveResolutionAfterRotation** в сочетании со значениями разрешения, выраженными в процентах (Width="100%", Height="100%").  

По умолчанию параметры разрешения кодирования (ширина и высота) в предварительных настройках Media Encoder Standard (MES) предназначены для видео с поворотом 0 градусов. Например, если входящее видео имеет разрешение 1280 x 720 с поворотом 0 градусов, предустановки по умолчанию определят это же разрешение и для выходного видео.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Если входящее видео записано с поворотом с ненулевым значением (например, на вертикально расположенный смартфон или планшет), обработчик MES по умолчанию применяет параметры разрешения кодирования (ширину и высоту) к входящему видео, а затем компенсирует поворот. Пример см. на рисунке ниже. Предварительная настройка использует значения ширины и высоты (Width=“100%”, Height=“100%”), согласно которым MES создает выходное видео шириной 1280 пикселей и высотой 720 пикселей. После поворота видео обработчик сжимает изображение, чтобы оно поместилось в окно. В результате слева и справа появляются черные полосы.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Или можно использовать флаг **PreserveResolutionAfterRotation** и установить для него значение true (по умолчанию — false). Итак, если в предустановке указано Width="100%", Height="100%", а для флага PreserveResolutionAfterRotation установлено значение true, входящее видео шириной 1280 пикселей и высотой 720 пикселей с поворотом 90 градусов преобразовывается в выходное видео с нулевым градусом поворота и размером 720 х 1280 пикселей. См. следующий рисунок.  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="FormatGroup"></a> FormatGroup (группа)
### <a name="elements"></a>Элементы
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="BmpLayer"></a> BmpLayer
### <a name="element"></a>Элемент
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Атрибуты
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **Condition** |**xs:string** | |

## <a name="PngLayer"></a> PngLayer
### <a name="element"></a>Элемент
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Атрибуты
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **Condition** |**xs:string** | |

## <a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>Элемент
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |
| **Quality**<br/><br/> minOccurs="0" |**xs:int** |Допустимые значения: 1–100 (от худшего качества до наилучшего) |

### <a name="attributes"></a>Атрибуты
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **Condition** |**xs:string** | |

## <a name="PngLayers"></a> PngLayers
### <a name="elements"></a>Элементы
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="BmpLayers"></a> BmpLayers
### <a name="elements"></a>Элементы
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>Элементы
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="BmpImage"></a> BmpImage (сложный тип, который наследуется из видео)
### <a name="elements"></a>Элементы
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Уровни PNG |

## <a name="JpgImage"></a> JpgImage (сложный тип, который наследуется из видео)
### <a name="elements"></a>Элементы
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Уровни PNG |

## <a name="PngImage"></a> PngImage (сложный тип, который наследуется из видео)
### <a name="elements"></a>Элементы
| ИМЯ | type | ОПИСАНИЕ |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Уровни PNG |

## <a name="examples"></a>Примеры
Примеры предустановок XML, созданных на основе этой схемы, см. в документации по [предустановкам задач для MES (Media Encoder Standard)](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Дополнительная информация
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

