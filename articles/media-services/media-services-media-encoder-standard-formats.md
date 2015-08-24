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


[Форматы импорта в кодировщике служб мультимедиа ](#import_formats)

[Форматы экспорта в кодировщике служб мультимедиа](#export_formats)


##<a id="import_formats"></a>Форматы импорта в кодировщике служб мультимедиа 

В следующем разделе перечислены кодеки и форматы файлов, поддерживаемые для импорта.


##Видеокодеки:

- MPEG-2
- H.264
- YUV420 без сжатия или мезонинный файл
- DNxHD
- VC-1/WMV9
- MPEG-4, часть 2
- JPEG 2000
- Theora

###Аудиокодеки

- PCM
- AAC (AAC-LC, AAC-HE и AAC-HEv2)
- WMA9/Pro
- MP3 (MPEG-1 Audio Layer 3)
- FLAC
- Opus
- Vorbis
 
###Форматы

Формат файла|Расширения файлов
---|---
FLV (с кодеками H.264 и AAC) |.flv
MP4/ISMV|* .ismv
MPEG2-PS, MPEG2-TS, 3GP|.ts, .ps, .3gp
MXF|.mxf
WMV/ASF|.mwv, .asf
DVR-MS|.dvr-ms 
AVI|.avi
Matroska|.mkv
GXF|.gxf
WAVE/WAV |.wav


##<a id="export_formats"></a>Форматы экспорта в кодировщике мультимедиа

В следующей таблице перечислены кодеки и форматы файлов, поддерживаемые для экспорта.


Формат файла|Видеокодек|Аудиокодек
---|---|---
MP4 (* .mp4)<br/><br/>(включая контейнеры MP4 с несколькими скоростями) |H.264 (профили High, Main и Baselin)|AAC-LC, HE-AAC v1, HE-AAC v2 
MPEG2-TS |H.264 (профили High, Main и Baselin)|AAC-LC, HE-AAC v1, HE-AAC v2 

##См. также

[Кодирование содержимого по запросу с помощью служб мультимедиа Azure](media-services-encode-asset.md)

<!---HONumber=August15_HO7-->