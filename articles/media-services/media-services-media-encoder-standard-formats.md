<properties 
	pageTitle="Форматы и кодеки стандартного кодировщика служб мультимедиа"
	description="В этом разделе приведен обзор кодеков и форматов стандартного кодировщика служб мультимедиа."
	services="media-services"
	documentationCenter=""
	authors="juliako"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="juliako"/>

#Форматы и кодеки стандартного кодировщика служб мультимедиа


Этот документ содержит список наиболее распространенных форматов для импорта и экспорта файлов, которые можно использовать со стандартным кодировщиком служб мультимедиа.


##Контейнер ввода/ форматы файлов

Форматы файлов (расширения файлов)|Поддерживаются
---|---|---|---
FLV (с кодеками H.264 и AAC) (.flv) |Да 
MXF (.mxf) |Да 
GXF (.gxf) |Да 
MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp) |Да 
Windows Media Video (WMV)/ASF (.wmv, .asf) |Да 
AVI (без сжатия 8 бит/10 бит) (.avi)|Да 
MP4/ISMV (.ismv)|Да 
[Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Да 
Matroska/WebM (.mkv) |Да 
WAVE/WAV (.wav) |Да 
 

##Входные видеокодеки

Входные видеокодеки|Поддерживаются
---|---|---|---
AVC 8-разрядный/10-разрядный, до 4:2:2, включая AVCIntra |8 бит 4:2:0 и 4:2:2 
Avid DNxHD (в MXF) |Да 
DVCPro/DVCProHD (в MXF) |Да 
JPEG 2000 |Да 
MPEG-2 (до 422 Profile и High Level, включая такие варианты, как XDCAM, XDCAM HD, XDCAM IMX, CableLabs® и D10)|До 422 Profile 
MPEG-1 |Да 
VC-1/WMV9 |Да 
Canopus HQ/HQX |Нет 
MPEG-4, часть 2 |Да 
[Theora](https://en.wikipedia.org/wiki/Theora) |Да 
YUV420 без сжатия или мезонинный файл |Да


##Входные аудиокодеки

Входные аудиокодеки|Поддерживаются
---|---|---|---
AAC (AAC-LC, AAC-HE и AAC-HEv2; до 5.1)|Да 
MPEG Layer 2|Да 
MP3 (MPEG-1 Audio Layer 3)|Да 
Windows Media Audio|Да 
WAV/PCM|Да 
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|Да 
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |Да 
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|Да 
AMR (adaptive multi-rate)|Да
AES (SMPTE 331M и 302M, AES3-2003) |Нет 
Dolby® E |Нет 
Dolby® Digital (AC3) |Нет 
Dolby® Digital Plus (E-AC3) |Нет 


##Выходные форматы и кодеки

В следующей таблице перечислены кодеки и форматы файлов, поддерживаемые для экспорта.


Формат файла|Видеокодек|Аудиокодек
---|---|---
MP4 (* .mp4)<br/><br/>(включая контейнеры MP4 с несколькими скоростями) |H.264 (профили High, Main и Baselin)|AAC-LC, HE-AAC v1, HE-AAC v2 
MPEG2-TS |H.264 (профили High, Main и Baselin)|AAC-LC, HE-AAC v1, HE-AAC v2 

##См. также

[Кодирование содержимого по запросу с помощью служб мультимедиа Azure](media-services-encode-asset.md)

[Кодирование с помощью стандартного кодировщика мультимедиа](media-services-dotnet-encode-with-media-encoder-standard.md)

<!---HONumber=September15_HO1-->