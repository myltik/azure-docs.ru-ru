<properties 
	pageTitle="Кодирование содержимого по требованию с помощью служб мультимедиа Azure" 
	description="В этом разделе представлен обзор кодирования содержимого по требованию с помощью служб мультимедиа." 
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
	ms.date="03/05/2015" 
	ms.author="juliako"/>

#Кодирование содержимого по требованию с помощью служб мультимедиа Azure

Это одна из статей серии [Рабочий процесс для видео по запросу в службах мультимедиа](media-services-video-on-demand-workflow.md).

##Обзор

Службы мультимедиа поддерживают следующие кодировщики:

- [кодировщик служб мультимедиа Azure;](#azure_media_encoder)
- [рабочий процесс Premium кодировщика служб мультимедиа](#media_encoder_premium_workflow) (общедоступная предварительная версия).

В [следующем разделе](#compare_encoders) сравниваются возможности обоих кодировщиков.

По умолчанию каждая учетная запись служб мультимедиа может выполнять одну активную задачу кодирования в текущий момент. Можно зарезервировать единицы кодирования, которые позволят выполнять несколько задач кодирования одновременно, по одной для каждой приобретенной единицы. Сведения о масштабировании единиц кодирования см. в следующих статьях о **портале** и **.NET**.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../includes/media-services-selector-scale-encoding-units.md)]

##<a id="azure_media_encoder"></a>Кодировщик служб мультимедиа Azure

[Форматы, поддерживаемые кодировщиком служб мультимедиа](media-services-azure-media-encoder-formats.md), - описание форматов файлов и потоковой передачи, поддерживаемых **кодировщиком службы мультимедиа Azure**.

**Кодировщик службы мультимедиа Azure** настраивается с помощью одной из предустановленных строк кодировщика, описанных [здесь](https://msdn.microsoft.com/library/azure/dn619392.aspx). Также можно получить фактические файлы кодировщика службы мультимедиа Azure [здесь](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder).

Кодирование **кодировщиком мультимедиа Azure** с помощью **портала управления Azure**, **.NET** или **API-интерфейса REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

####Другие связанные разделы

[Динамическая упаковка](https://msdn.microsoft.com/library/azure/jj889436.aspx) - описание кодирования в MP4-файлы с адаптивной скоростью и динамической обработки потоков Smooth Streaming, Apple HLS или MPEG-DASH.

[Управление именами выходных файлов кодировщика служб мультимедиа](https://msdn.microsoft.com/library/azure/dn303341.aspx) - описание соглашения об именовании файлов, используемого кодировщиком службы мультимедиа Azure, и способа изменения выходных файлов.

[Кодирование мультимедиа с помощью Dolby Digital Plus](media-services-encode-with-dolby-digital-plus.md) - описание кодирования звуковых дорожек с использованием Dolby Digital Plus.


##<a id="media_encoder_premium_wokrflow"></a>Рабочий процесс Premium кодировщика мультимедиа (общедоступная предварительная версия)

**Примечание** Обработчик мультимедиа рабочего процесса Premium Media Encoder, рассмотренный в данном разделе, недоступен в Китае.

[Форматы, поддерживаемые рабочим процессом Premium кодировщика мультимедиа](media-services-premium-workflow-encoder-formats.md) - представлены форматы файлов и кодеков, поддерживаемых **рабочим процессом Premium кодировщика мультимедиа**.

**Рабочий процесс Premium кодировщика мультимедиа** настраивается с помощью сложных рабочих процессов. Файлы рабочих процессов можно создать с помощью [конструктора рабочих процессов](media-services-workflow-designer.md) . 

Файлы рабочего процесса по умолчанию можно получить [здесь](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Папка также содержит описание этих файлов.

Кодирование с **рабочим процессом Premium кодировщика мультимедиа** с использованием **.NET**. Дополнительные сведения см. в разделе [Расширенное кодирование с рабочим процессом Premium кодировщика мультимедиа](media-services-encode-with-premium-workflow.md).
 

##<a id="compare_encoders"></a>Сравнение кодировщиков

В этом разделе сравниваются возможности кодирования **кодировщика служб мультимедиа Azure** и **рабочего процесса Premium кодировщика мультимедиа**.

###Форматы входных данных

Контейнер ввода/ форматы файлов

<table border="1">
<tr><th>Input Container/File Formats</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>Adobe(r) Flash(r) F4V</td><td>Yes</td><td>No</td></tr>
<tr><td>MXF/SMPTE 377M</td><td>Yes</td><td>Limited</td></tr>
<tr><td>GXF</td><td>Yes</td><td>No</td></tr>
<tr><td>MPEG-2 Transport Streams</td><td>Yes</td><td>Yes</td></tr>
<tr><td>MPEG-2 Program Streams</td><td>Yes</td><td>Yes</td></tr>
<tr><td>MPEG-4/MP4</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Windows Media/ASF</td><td>Yes</td><td>Yes</td></tr>
<tr><td>AVI (Uncompressed 8bit/10bit)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>3GPP/3GPP2</td><td>No</td><td>Yes</td></tr>
<tr><td>Smooth Streaming File Format (PIFF 1.3)</td><td>No</td><td>Yes</td></tr>
</table>

Входные видеокодеки

<table border="1">
<tr><th>Input Video Codecs</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AVC 8-bit/10-bit, up to 4:2:2, including AVCIntra</td><td>Yes</td><td>Only 8bit 4:2:0</td></tr>
<tr><td>Avid DNxHD (in MXF)</td><td>Yes</td><td>No</td></tr>
<tr><td>DVCPro/DVCProHD (in MXF)</td><td>Yes</td><td>No</td></tr>
<tr><td>JPEG2000</td><td>Yes</td><td>No</td></tr>
<tr><td>MPEG-2 (up to 422 Profile and High Level; including variants such as XDCAM, XDCAM HD, XDCAM IMX, CableLabs(r) and D10)</td><td>Yes</td><td>Up to 422 Profile</td></tr>
<tr><td>MPEG-1</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Canopus HQ/HQX</td><td>No</td><td>Yes</td></tr>
</table>

Входные аудиокодеки

<table border="1">
<tr><th>Input Audio Codecs</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AES (SMPTE 331M and 302M, AES3-2003)</td><td>Yes</td><td>No</td></tr>
<tr><td>Dolby(r) E</td><td>Yes</td><td>No</td></tr>
<tr><td>Dolby(r) Digital (AC3)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Dolby(r) Digital Plus (E-AC3)</td><td>Yes</td><td>No</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE, and AAC-HEv2; up to 5.1)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>MPEG Layer 2</td><td>Yes</td><td>Yes</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Windows Media Audio</td><td>Yes</td><td>Yes</td></tr>
<tr><td>WAV/PCM</td><td>Yes</td><td>Yes</td></tr>
</table>

###Форматы выходных данных

Контейнер вывода/ форматы файлов

<table border="1">
<tr><th>Output Container/File Formats</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>Adobe(r) Flash(r) F4V</td><td>Yes</td><td>No</td></tr>
<tr><td>MXF (OP1a, XDCAM and AS02)</td><td>Yes</td><td>No</td></tr>
<tr><td>DPP (including AS11)</td><td>Yes</td><td>No</td></tr>
<tr><td>GXF</td><td>Yes</td><td>No</td></tr>
<tr><td>MPEG-4/MP4</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Windows Media/ASF</td><td>Yes</td><td>Yes</td></tr>
<tr><td>AVI (Uncompressed 8bit/10bit)</td><td>Yes</td><td>No</td></tr>
<tr><td>Smooth Streaming File Format (PIFF 1.3)</td><td>Yes</td><td>Yes</td></tr>
</table>

Выходные видеокодеки

<table border="1">
<tr><th>Output Video Codecs</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AVC (H.264; 8-bit; up to High Profile, Level 5.2; 4K Ultra HD; AVC Intra)</td><td>Yes</td><td>Only 8bit 4:2:0 up to 1080p</td></tr>
<tr><td>Avid DNxHD (in MXF)</td><td>Yes</td><td>No</td></tr>
<tr><td>DVCPro/DVCProHD (in MXF)</td><td>Yes</td><td>No</td></tr>
<tr><td>MPEG-2 (up to 422 Profile and High Level; including variants such as XDCAM, XDCAM HD, XDCAM IMX, CableLabs(r) and D10)</td><td>Yes</td><td>No</td></tr>
<tr><td>MPEG-1</td><td>Yes</td><td>No</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Yes</td><td>Yes</td></tr>
<tr><td>JPEG thumbnail creation</td><td>Yes</td><td>Yes</td></tr>
</table>

Выходные аудиокодеки

<table border="1">
<tr><th>Output Audio Codecs</th><th>Media Encoder Premium Workflow</th><th>Azure Media Encoder
</th></tr>
<tr><td>AES (SMPTE 331M and 302M, AES3-2003)</td><td>Yes</td><td>No</td></tr>
<tr><td>Dolby(r) Digital (AC3)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>Dolby(r) Digital Plus (E-AC3) up to 7.1</td><td>Yes</td><td>Up to 5.1</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE, and AAC-HEv2; up to 5.1)</td><td>Yes</td><td>Yes</td></tr>
<tr><td>MPEG Layer 2</td><td>Yes</td><td>No</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Yes</td><td>No</td></tr>
<tr><td>Windows Media Audio</td><td>Yes</td><td>Yes</td></tr>
</table>
##Связанные статьи

- [Знакомство с кодированием Premium в службах мультимедиа Azure](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [Использование кодирования Premium в службах мультимедиа Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
- [Квоты и ограничения](media-services-quotas-and-limitations.md)



<!--HONumber=52-->