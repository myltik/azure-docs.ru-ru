---
title: "Схема Media Encoder Standard | Документация Майкрософт"
description: "В этой статье приведен обзор схемы Media Encoder Standard."
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: 4c060062-8ef2-41d9-834e-e81e8eafcf2e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 03112e4bde1e5f986102083fc0e16cd51fc47504
ms.openlocfilehash: 83a80fe794000387173575e84879789ff1a057b7


---
# <a name="media-encoder-standard-schema"></a>Схема Media Encoder Standard
В этой статье описаны некоторые элементы и типы XML-схемы, лежащие в основе [предустановок Media Encoder Standard](https://msdn.microsoft.com/library/azure/mt269960.aspx). Также рассмотрены элементы и их допустимые значения. Полная схема будет опубликована в будущем.  

## <a name="a-namepreseta-preset-root-element"></a><a name="Preset"></a> Предустановка (корневой элемент)
Определяет предустановку кодирования.  

### <a name="elements"></a>Элементы
| Имя | Тип | Описание |
| --- | --- | --- |
| **Encoding** |[Encoding](media-services-mes-schema.md#Encoding) |Корневой элемент, который указывает, что источники входных данных должны быть закодированы. |
| **Outputs** |[Outputs](media-services-mes-schema.md#Output) |Коллекция требуемых выходных файлов. |

### <a name="attributes"></a>Атрибуты
| Имя | Тип | Описание |
| --- | --- | --- |
| **Версия**<br /><br /> Обязательно |**xs:decimal** |Версия предустановки. Применяются следующие ограничения: xs:fractionDigits value="1" и xs:minInclusive value="1". Например, **version="1.0"**. |

## <a name="a-nameencodinga-encoding"></a><a name="Encoding"></a> Кодирование
Содержит последовательность следующих элементов.  

### <a name="elements"></a>Элементы
| Имя | Тип | Описание |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Параметры кодирования видео в формате H.264. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Параметры кодирования аудио в формате AAC. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Параметры изображения в формате BMP. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Параметры изображения в формате PNG. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Параметры изображения в формате JPG. |

## <a name="a-nameh264videoa-h264video"></a><a name="H264Video"></a> H264Video
### <a name="elements"></a>Элементы
| Имя | Тип | Описание |
| --- | --- | --- |
| **TwoPass**<br /><br /> minOccurs="0" |**xs:boolean** |Сейчас поддерживается только однопроходное кодирование. |
| **KeyFrameInterval**<br /><br /> minOccurs="0"<br /><br /> **default="00:00:02"** |**xs:time** |Определяет расстояние между кадрами IDR (по умолчанию). |
| **SceneChangeDetection**<br /><br /> minOccurs="0"<br /><br /> default=”false” |**xs:boolean** |Если задано значение true, кодировщик пытается обнаружить изменение сцены в видео и вставить кадр IDR. |
| **Complexity**<br /><br /> minOccurs="0"<br /><br /> default="Balanced" |**xs:string** |Регулирует баланс между скоростью кодирования и качеством видео. Может принимать одно из следующих значений: **Speed**, **Balanced** и **Quality**.<br /><br /> Значение по умолчанию: **Balanced**. |
| **SyncMode**<br /><br /> minOccurs="0" | |Функция будет доступна в будущих выпусках. |
| **H264Layers**<br /><br /> minOccurs="0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Коллекция уровней выходных видео. |

## <a name="a-nameh264layersa-h264layers"></a><a name="H264Layers"></a> H264Layers
### <a name="elements"></a>Элементы
| Имя | Тип | Описание |
| --- | --- | --- |
| **H264Layer**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[H264Layer](media-services-mes-schema.md#H264Layer) |Коллекция уровней H264. |

## <a name="a-nameh264layera-h264layer"></a><a name="H264Layer"></a> H264Layer
> [!NOTE]
> Ограничения видео основаны на значениях, описанных в таблице [уровней H264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels).  
> 
> 

### <a name="elements"></a>Элементы
| Имя | Тип | Описание |
| --- | --- | --- |
| **Профиль**<br /><br /> minOccurs="0"<br /><br /> default=”Auto” |**xs:string** |Может иметь одно из следующих значений **xs:string**: **Auto**, **Baseline**, **Main**, **High**. |
| **Level**<br /><br /> minOccurs="0"<br /><br /> default=”Auto” |**xs:string** | |
| **Bitrate**<br /><br /> minOccurs="0" |**xs:int** |Скорость, используемая для этого уровня видео, указанная в Кбит/с. |
| **MaxBitrate**<br /><br /> minOccurs="0" |**xs:int** |Максимальная скорость, используемая для этого уровня видео, указанная в Кбит/с. |
| **BufferWindow**<br /><br /> minOccurs="0"<br /><br /> default="00:00:05" |**xs:time** |Длина буфера видео. |
| **Width**<br /><br /> minOccurs="0" |**xs:int** |Ширина кадра выходного видео в пикселях.<br /><br /> Обратите внимание, что сейчас необходимо указывать и ширину, и высоту. Значения ширины и высоты должны быть четными числами. |
| **Height**<br /><br /> minOccurs="0" |**xs:int** |Высота кадра выходного видео в пикселях.<br /><br /> Обратите внимание, что сейчас необходимо указывать и ширину, и высоту. Значения ширины и высоты должны быть четными числами.|
| **BFrames**<br /><br /> minOccurs="0" |**xs:int** |Число кадров B между опорными кадрами. |
| **ReferenceFrames**<br /><br /> minOccurs="0"<br /><br /> default=”3” |**xs:int** |Число опорных кадров в GOP. |
| **EntropyMode**<br /><br /> minOccurs="0"<br /><br /> default=”Cabac” |**xs:string** |Может принимать одно из следующих значений: **Cabac** и **Cavlc**. |
| **FrameRate**<br /><br /> minOccurs="0" |Рациональное число |Определяет частоту кадров выходного видео. Используйте стандартное значение 0/1, чтобы кодировщик использовал ту же частоту кадров, что и выходное видео. Допустимые значения должны быть стандартными для частоты кадров, как показано ниже. Но допустимо любое рациональное число. Например, значение 1/1 — это 1 кадр/с. Оно является допустимым.<br /><br /> - 12/1 (12 кадров/с)<br /><br /> - 15/1 (15 кадров/с)<br /><br /> - 24/1 (24 кадра/с)<br /><br /> - 24 000/1001 (23,976 кадра/с)<br /><br /> - 25/1 (25 кадров/с)<br /><br /> - 30/1 (30 кадров/с)<br /><br /> - 30 000/1001 (29,97 кадра/с) |
| **AdaptiveBFrame**<br /><br /> minOccurs="0" |**xs:boolean** |Копия из кодировщика мультимедиа Azure |
| **Slices**<br /><br /> minOccurs="0"<br /><br /> default="0" |**xs:int** |Определяет число фрагментов для разделенного кадра. Рекомендуется использовать значение по умолчанию. |

## <a name="a-nameaacaudioa-aacaudio"></a><a name="AACAudio"></a> AACAudio
 Содержит последовательность следующих элементов и групп.  

 См. дополнительные сведения о стандарте [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Элементы
| Имя | Тип | Описание |
| --- | --- | --- |
| **Профиль**<br /><br /> minOccurs="0 "<br /><br /> default="AACLC" |**xs:string** |Может принимать одно из следующих значений: **AACLC**, **HEAACV1** или **HEAACV2**. |

### <a name="attributes"></a>Атрибуты
| Имя | Тип | Описание |
| --- | --- | --- |
| **Condition** |**xs:string** |Чтобы заставить кодировщик создать ресурс, содержащий звуковую дорожку с тишиной, когда входные данные не содержат звука, укажите значение "InsertSilenceIfNoAudio".<br /><br /> По умолчанию при отправке кодировщику входных данных, которые содержат только видео и не содержат звука, выходной ресурс будет содержать файлы только с видеоданными. Некоторые проигрыватели не смогут обработать такие выходные потоки. Этот параметр можно использовать для принудительного добавления кодировщиком звуковой дорожки с тишиной к выходным данным в этом сценарии. |

### <a name="groups"></a>Группы
| Справочные материалы | Описание |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br /><br /> minOccurs="0" |См. описание [AudioGroup](media-services-mes-schema.md#AudioGroup), чтобы узнать необходимое число каналов, частоту выборки и скорость потока, которые можно установить для каждого профиля. |

## <a name="a-nameaudiogroupa-audiogroup"></a><a name="AudioGroup"></a> AudioGroup
Дополнительные сведения о допустимых значениях для каждого профиля см. в таблице "Сведения об аудиокодеке" ниже.  

### <a name="elements"></a>Элементы
| Имя | Тип | Описание |
| --- | --- | --- |
| **Channels**<br /><br /> minOccurs="0" |**xs:int** |Число закодированных аудиоканалов. Допустимые значения: 1, 2, 5, 6, 8.<br /><br /> Значение по умолчанию: 2. |
| **SamplingRate**<br /><br /> minOccurs="0" |**xs:int** |Частота дискретизации, указанная в Гц. |
| **Bitrate**<br /><br /> minOccurs="0" |**xs:int** |Скорость кодировки аудио, указанная в Кбит/с. |

### <a name="audio-codec-details"></a>Сведения об аудиокодеке
| Аудиокодек |
| --- | --- |
| **AACLC** |
| **HEAACV1** |
| **HEAACV2** |

## <a name="a-nameclipa-clip"></a><a name="Clip"></a> Клип
### <a name="attributes"></a>Атрибуты
| Имя | Тип | Описание |
| --- | --- | --- |
| **StartTime** |**xs:duration** |Указывает время начала презентации. Значение StartTime должно соответствовать абсолютной метке времени входящего видео. Например, если первый кадр входящего видео имеет метку времени 12:00:10.000, значение StartTime должно было равно или больше 12:00:10.000. |
| **Duration** |**xs:duration** |Указывает продолжительность презентации (например, внешний вид наложения в видео). |

## <a name="a-nameoutputa-output"></a><a name="Output"></a> Выходные данные
### <a name="attributes"></a>Атрибуты
| Имя | Тип | Описание |
| --- | --- | --- |
| **FileName** |**xs:string** |Имя выходного файла.<br /><br /> Для создания имен выходных файлов можно использовать макросы, описанные в следующей таблице. Например:<br /><br /> **"Outputs": [      {       "FileName": "{Basename}*{Resolution}*{Bitrate}.mp4",       "Format": {         "Type": "MP4Format"       }     }   ]** |

### <a name="macros"></a>Макросы
| Макрос | Описание |
| --- | --- |
| **{Basename}** |Если вы кодируете видео по запросу, макрос {Basename} представляет первые 32 символа свойства AssetFile.Name основного файла во входном ресурсе-контейнере.<br /><br /> Если входной ресурс-контейнер — это динамический архив, макрос {Basename} является производным от атрибутов trackName в манифесте сервера. Если вы отправляете задание субклипа с помощью TopBitrate, как в <VideoStream\>TopBitrate</VideoStream\>, и выходной файл содержит видео, макрос {Basename} представляет первые 32 символа trackName уровня видео с наивысшей скоростью.<br /><br /> Если вы отправляете задание субклипа с помощью входящей скорости, как в <VideoStream\>*</VideoStream\>, и выходной файл содержит видео, макрос {Basename} представляет первые 32 символа соответствующего уровня видео. |
| **{Codec}** |Соответствует формату H.264 для видео и AAC для аудио. |
| **{Bitrate}** |Целевая скорость видео, если выходной файл содержит видео и аудио, или целевая скорость аудио, если выходной файл содержит только аудио. Используется значение скорости в Кбит/с. |
| **{Channel}** |Счетчик аудиоканала, если файл содержит аудио. |
| **{Width}** |Ширина видео в пикселях в выходном файле, если файл содержит видео. |
| **{Height}** |Высота видео в пикселях в выходном файле, если файл содержит видео. |
| **{Extension}** |Наследует свойство Type для выходного файла. Имя выходного файла будет иметь одно из следующих расширений: MP4, TS, JPG, PNG или BMP. |
| **{Index}** |Обязателен для эскиза. Должен быть представлен только один раз. |

## <a name="a-namevideoa-video-complex-type-inherits-from-codec"></a><a name="Video"></a> Видео (сложный тип, который наследуется из кодека)
### <a name="attributes"></a>Атрибуты
| Имя | Тип | Описание |
| --- | --- | --- |
| **Начало** |**xs:string** | |
| **Step** |**xs:string** | |
| **Range** |**xs:string** | |
| **PreserveResolutionAfterRotation** |**xs:boolean** |Подробное описание см. в следующем разделе: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation). |

### <a name="a-namepreserveresolutionafterrotationa-preserveresolutionafterrotation"></a><a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
Рекомендуется использовать флаг PreserveResolutionAfterRotation в сочетании со значениями разрешения, выраженными в процентах (Width=”100%”, Height=“100%”).  

По умолчанию параметры разрешения кодирования (ширина и высота) в предварительных настройках Media Encoder Standard (MES) предназначены для видео с поворотом 0 градусов. Например, если входящее видео имеет разрешение 1280 x 720 с поворотом 0 градусов, предустановки по умолчанию определят это же разрешение и для выходного видео. См. рисунок ниже.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Но это также означает следующее. Если входящее видео записано с поворотом с ненулевым значением (например, на вертикально расположенный смартфон или планшет), обработчик MES по умолчанию применяет параметры разрешения кодирования (ширину и высоту) к входящему видео MES, а затем компенсирует поворот. Пример см. на рисунке ниже. Предварительная настройка использует значения ширины и высоты (Width=“100%”, Height=“100%”), согласно которым MES создает выходное видео шириной 1280 пикселей и высотой 720 пикселей. После поворота видео обработчик сжимает изображение, чтобы оно поместилось в окно. В результате слева и справа появляются черные полосы.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Если описанный выше сценарий не является желательным, вы можете использовать флаг PreserveResolutionAfterRotation и установить для него значение true (по умолчанию — false). Итак, если в предустановке указано Width=“100%”, Height=“100%”, а для флага PreserveResolutionAfterRotation установлено значение true, входящее видео шириной 1280 пикселей и высотой 720 пикселей с поворотом 90 градусов будет преобразовано в выходное видео с нулевым градусом поворота и размером 720 х 1280 пикселей. См. рисунок ниже.  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="a-nameformatgroupa-formatgroup-group"></a><a name="FormatGroup"></a> FormatGroup (группа)
### <a name="elements"></a>Элементы
| Имя | Тип | Описание |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="a-namebmplayera-bmplayer"></a><a name="BmpLayer"></a> BmpLayer
### <a name="element"></a>Элемент
| Имя | Тип | Описание |
| --- | --- | --- |
| **Width**<br /><br /> minOccurs="0" |**xs:int** | |
| **Height**<br /><br /> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Атрибуты
| Имя | Тип | Описание |
| --- | --- | --- |
| **Condition** |**xs:string** | |

## <a name="a-namepnglayera-pnglayer"></a><a name="PngLayer"></a> PngLayer
### <a name="element"></a>Элемент
| Имя | Тип | Описание |
| --- | --- | --- |
| **Width**<br /><br /> minOccurs="0" |**xs:int** | |
| **Height**<br /><br /> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Атрибуты
| Имя | Тип | Описание |
| --- | --- | --- |
| **Condition** |**xs:string** | |

## <a name="a-namejpglayera-jpglayer"></a><a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>Элемент
| Имя | Тип | Описание |
| --- | --- | --- |
| **Width**<br /><br /> minOccurs="0" |**xs:int** | |
| **Height**<br /><br /> minOccurs="0" |**xs:int** | |
| **Quality**<br /><br /> minOccurs="0" |**xs:int** |Допустимые значения: 1–100 (от худшего качества до наилучшего) |

### <a name="attributes"></a>Атрибуты
| Имя | Тип | Описание |
| --- | --- | --- |
| **Condition** |**xs:string** | |

## <a name="a-namepnglayersa-pnglayers"></a><a name="PngLayers"></a> PngLayers
### <a name="elements"></a>Элементы
| Имя | Тип | Описание |
| --- | --- | --- |
| **PngLayer**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="a-namebmplayersa-bmplayers"></a><a name="BmpLayers"></a> BmpLayers
### <a name="elements"></a>Элементы
| Имя | Тип | Описание |
| --- | --- | --- |
| **BmpLayer**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="a-namejpglayersa-jpglayers"></a><a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>Элементы
| Имя | Тип | Описание |
| --- | --- | --- |
| **JpgLayer**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="a-namebmpimagea-bmpimage-complex-type-inherits-from-video"></a><a name="BmpImage"></a> BmpImage (сложный тип, который наследуется из видео)
### <a name="elements"></a>Элементы
| Имя | Тип | Описание |
| --- | --- | --- |
| **PngLayers**<br /><br /> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Уровни PNG |

## <a name="a-namejpgimagea-jpgimage-complex-type-inherits-from-video"></a><a name="JpgImage"></a> JpgImage (сложный тип, который наследуется из видео)
### <a name="elements"></a>Элементы
| Имя | Тип | Описание |
| --- | --- | --- |
| **PngLayers**<br /><br /> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Уровни PNG |

## <a name="a-namepngimagea-pngimage-complex-type-inherits-from-video"></a><a name="PngImage"></a> PngImage (сложный тип, который наследуется из видео)
### <a name="elements"></a>Элементы
| Имя | Тип | Описание |
| --- | --- | --- |
| **PngLayers**<br /><br /> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Уровни PNG |

## <a name="examples"></a>Примеры
Примеры предустановок XML, созданных на основе этой схемы, см. в документации по [предустановкам задач для MES (Media Encoder Standard)](https://msdn.microsoft.com/library/azure/mt269960.aspx).

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Отзывы
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO3-->


