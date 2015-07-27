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
	ms.date="07/08/2015" 
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

<table border="1">
<tr><th>Формат файла</th><th>Расширения файлов</th></tr>
<tr><td>FLV (с кодеками H.264 и AAC) </td><td>.flv</td></tr>
<tr><td>MP4/ISMV</td><td>*.ismv</td></tr>
<tr><td>MPEG2-PS, MPEG2-TS, 3GP</td><td>.ts, .ps, .3gp</td></tr>
<tr><td>MXF</td><td>.mxf</td></tr>
<tr><td>WMV/ASF</td><td>.mwv, .asf</td></tr>
<tr><td>DVR-MS</td><td>.dvr-ms </td></tr>
<tr><td>AVI</td><td>.avi</td></tr>
<tr><td>Matroska</td><td>.mkv</td></tr>
<tr><td>GXF</td><td>.gxf</td></tr>
<tr><td>WAVE/WAV </td><td>.wav</td></tr>
</table>

##<a id="export_formats"></a>Форматы экспорта в кодировщике служб мультимедиа

В следующей таблице перечислены кодеки и форматы файлов, поддерживаемые для экспорта.


<table border="1">
<tr><th>Формат файла</th><th>Видеокодек</th><th>Аудиокодек</th></tr>
<tr><td>MP4 (MP4)<br/><br/>(включая контейнеры MP4 с несколькими скоростями) </td><td>H.264 (профили High, Main и Baselin)</td><td>AAC-LC, HE-AAC v1, HE-AAC v2 </td></tr>
<tr><td>MPEG2-TS </td><td>H.264 (профили High, Main и Baselin)</td><td>AAC-LC, HE-AAC v1, HE-AAC v2 </td></tr>
</table>

##См. также

[Кодирование содержимого по запросу с помощью служб мультимедиа Azure](media-services-encode-asset.md)

<!---HONumber=July15_HO3-->