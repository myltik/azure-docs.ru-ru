<properties 
	pageTitle="Кодирование содержимого по запросу с помощью служб мультимедиа Azure" 
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
	ms.date="06/29/2015" 
	ms.author="juliako"/>

#Кодирование содержимого по запросу с помощью служб мультимедиа Azure

Это одна из статей серии [Рабочий процесс для видео по запросу в службах мультимедиа](media-services-video-on-demand-workflow.md).

##Обзор

Службы мультимедиа поддерживают следующие кодировщики:

- [Azure Media Encoder](#azure_media_encoder)
- [Расширенный рабочий процесс кодировщика мультимедиа](#media_encoder_premium_workflow) (общедоступная предварительная версия)

В [следующем разделе](#compare_encoders) сравниваются возможности обоих кодировщиков.

По умолчанию каждая учетная запись служб мультимедиа может выполнять одну активную задачу кодирования в текущий момент. Можно зарезервировать единицы кодирования, которые позволят выполнять несколько задач кодирования одновременно, по одной для каждой приобретенной единицы. Информацию о масштабировании единиц кодирования см. в следующих статьях о **портале** и **.NET**.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

##<a id="azure_media_encoder"></a>Azure Media Encoder

[Форматы, поддерживаемые кодировщиком служб мультимедиа](media-services-azure-media-encoder-formats.md) — описание форматов файлов и потоковой передачи, поддерживаемых кодировщиком **Azure Media Encoder**.

Кодировщик **Azure Media Encoder** настраивается с помощью одной из строк предустановок кодировщика, описанных [здесь](https://msdn.microsoft.com/library/azure/dn619392.aspx). Также [здесь](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder) можно получить фактические файлы предустановок Azure Media Encoder.

Кодируйте файлы с помощью **кодировщика службы мультимедиа Azure** через **портал управления Azure**, **.NET** или **API REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

####Другие связанные разделы

[Динамическая упаковка](https://msdn.microsoft.com/library/azure/jj889436.aspx) — описание кодирования в MP4-файлы с адаптивной скоростью и динамической обработки потоков Smooth Streaming, Apple HLS или MPEG-DASH.

[Управление именами выходных файлов кодировщика служб мультимедиа](https://msdn.microsoft.com/library/azure/dn303341.aspx) — описание соглашения об именовании файлов, используемого кодировщиком Azure Media Encoder, и способа изменения имен выходных файлов.

[Кодирование мультимедиа с помощью Dolby Digital Plus](media-services-encode-with-dolby-digital-plus.md) — описание кодирования звуковых дорожек с использованием Dolby Digital Plus.


##<a id="media_encoder_premium_wokrflow"></a>Расширенный рабочий процесс кодировщика мультимедиа (общедоступная предварительная версия)

**Примечание**. Обработчик мультимедиа рабочего процесса Premium Media Encoder, рассмотренный в данном разделе, недоступен в Китае.

[Форматы, которые поддерживаются расширенным рабочим процессом кодировщика мультимедиа](media-services-premium-workflow-encoder-formats.md) — описание файловых форматов и кодеков, поддерживаемых **расширенным рабочим процессом кодировщика мультимедиа**.

**Расширенный рабочий процесс кодировщика мультимедиа** настраивается с помощью сложных рабочих процессов. Файлы рабочих процессов можно создать с помощью [конструктора рабочих процессов](media-services-workflow-designer.md).

Файлы используемого по умолчанию рабочего процесса можно получить [здесь](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Папка также содержит описание этих файлов.

Кодирование с **расширенным рабочим процессом кодировщика мультимедиа** с использованием **.NET**. Дополнительные сведения см. в разделе [Дополнительное кодирование с помощью расширенного рабочего процесса кодировщика мультимедиа](media-services-encode-with-premium-workflow.md).
 

##<a id="compare_encoders"></a>Сравнение кодировщиков

В этом разделе сравниваются возможности кодирования **Azure Media Encoder** и **расширенного рабочего процесса кодировщика мультимедиа**.

###Форматы входных данных

Контейнер ввода/ форматы файлов

<table border="1">
<tr><th>Контейнер ввода/ форматы файлов</th><th>Рабочий процесс Premium обработчика мультимедиа</th><th>Azure Media Encoder
</th></tr>
<tr><td>Adobe® Flash® F4V</td><td>Да</td><td>Нет</td></tr>
<tr><td>MXF/SMPTE 377M</td><td>Да</td><td>Ограничено</td></tr>
<tr><td>GXF</td><td>Да</td><td>Нет</td></tr>
<tr><td>Транспортные потоки MPEG-2</td><td>Да</td><td>Да</td></tr>
<tr><td>Программные потоки MPEG-2</td><td>Да</td><td>Да</td></tr>
<tr><td>MPEG-4/MP4</td><td>Да</td><td>Да</td></tr>
<tr><td>Windows Media/ASF</td><td>Да</td><td>Да</td></tr>
<tr><td>AVI (без сжатия 8 бит/10 бит)</td><td>Да</td><td>Да</td></tr>
<tr><td>3GPP/3GPP2</td><td>Нет</td><td>Да</td></tr>
<tr><td>Формат файлов Smooth Streaming (PIFF 1.3)</td><td>Нет</td><td>Да</td></tr>
</table>

Входные видеокодеки

<table border="1">
<tr><th>Входные видеокодеки</th><th>Рабочий процесс Premium обработчика мультимедиа</th><th>Azure Media Encoder
</th></tr>
<tr><td>AVC 8-разрядный/10-разрядный, до 4:2:2, включая AVCIntra</td><td>Да</td><td>Только 8-разрядный 4:2:0</td></tr>
<tr><td>Avid DNxHD (в MXF)</td><td>Да</td><td>Нет</td></tr>
<tr><td>DVCPro/DVCProHD (в MXF)</td><td>Да</td><td>Нет</td></tr>
<tr><td>JPEG2000</td><td>Да</td><td>Нет</td></tr>
<tr><td>MPEG-2 (до 422 Profile и High Level, включая такие варианты, как XDCAM, XDCAM HD, XDCAM IMX, CableLabs® и D10)</td><td>Да</td><td>До 422 Profile</td></tr>
<tr><td>MPEG-1</td><td>Да</td><td>Да</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Да</td><td>Да</td></tr>
<tr><td>Canopus HQ/HQX</td><td>Нет</td><td>Да</td></tr>
</table>

Входные аудиокодеки

<table border="1">
<tr><th>Входные аудиокодеки</th><th>Рабочий процесс Premium обработчика мультимедиа</th><th>Azure Media Encoder
</th></tr>
<tr><td>AES (SMPTE 331M и 302M, AES3-2003)</td><td>Да</td><td>Нет</td></tr>
<tr><td>Dolby® E</td><td>Да</td><td>Нет</td></tr>
<tr><td>Dolby® Digital (AC3)</td><td>Да</td><td>Да</td></tr>
<tr><td>Dolby® Digital Plus (E-AC3)</td><td>Да</td><td>Нет</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE и AAC-HEv2; до 5.1)</td><td>Да</td><td>Да</td></tr>
<tr><td>MPEG Layer 2</td><td>Да</td><td>Да</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Да</td><td>Да</td></tr>
<tr><td>Windows Media Audio</td><td>Да</td><td>Да</td></tr>
<tr><td>WAV/PCM</td><td>Да</td><td>Да</td></tr>
</table>

###Форматы выходных данных

Контейнер вывода/ форматы файлов

<table border="1">
<tr><th>Контейнер вывода/ форматы файлов</th><th>Рабочий процесс Premium обработчика мультимедиа</th><th>Azure Media Encoder
</th></tr>
<tr><td>Adobe® Flash® F4V</td><td>Да</td><td>Нет</td></tr>
<tr><td>MXF (OP1a, XDCAM и AS02)</td><td>Да</td><td>Нет</td></tr>
<tr><td>DPP (включая AS11)</td><td>Да</td><td>Нет</td></tr>
<tr><td>GXF</td><td>Да</td><td>Нет</td></tr>
<tr><td>MPEG-4/MP4</td><td>Да</td><td>Да</td></tr>
<tr><td>Windows Media/ASF</td><td>Да</td><td>Да</td></tr>
<tr><td>AVI (без сжатия 8 бит/10 бит)</td><td>Да</td><td>Нет</td></tr>
<tr><td>Формат файлов Smooth Streaming (PIFF 1.3)</td><td>Да</td><td>Да</td></tr>
</table>

Выходные видеокодеки

<table border="1">
<tr><th>Выходные видеокодеки</th><th>Рабочий процесс Premium обработчика мультимедиа</th><th>Azure Media Encoder
</th></tr>
<tr><td>AVC (H.264; 8-разрядный; до High Profile, Level 5.2; 4K Ultra HD; AVC Intra)</td><td>Да</td><td>Только 8-разрядный 4:2:0 вплоть до 1080p</td></tr>
<tr><td>Avid DNxHD (в MXF)</td><td>Да</td><td>Нет</td></tr>
<tr><td>DVCPro/DVCProHD (в MXF)</td><td>Да</td><td>Нет</td></tr>
<tr><td>MPEG-2 (до 422 Profile и High Level, включая такие варианты, как XDCAM, XDCAM HD, XDCAM IMX, CableLabs® и D10)</td><td>Да</td><td>Нет</td></tr>
<tr><td>MPEG-1</td><td>Да</td><td>Нет</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Да</td><td>Да</td></tr>
<tr><td>Создание эскизов JPEG</td><td>Да</td><td>Да</td></tr>
</table>

Выходные аудиокодеки

<table border="1">
<tr><th>Выходные аудиокодеки</th><th>Рабочий процесс Premium обработчика мультимедиа</th><th>Azure Media Encoder
</th></tr>
<tr><td>AES (SMPTE 331M и 302M, AES3-2003)</td><td>Да</td><td>Нет</td></tr>
<tr><td>Dolby® Digital (AC3)</td><td>Да</td><td>Да</td></tr>
<tr><td>Dolby® Digital Plus (E-AC3) до 7.1</td><td>Да</td><td>До 5.1</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE и AAC-HEv2; до 5.1)</td><td>Да</td><td>Да</td></tr>
<tr><td>MPEG Layer 2</td><td>Да</td><td>Нет</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Да</td><td>Нет</td></tr>
<tr><td>Windows Media Audio</td><td>Да</td><td>Да</td></tr>
</table>
##Связанные статьи

- [Знакомство с кодированием Premium в службах мультимедиа Azure](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [Использование кодирования Premium в службах мультимедиа Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
- [Квоты и ограничения](media-services-quotas-and-limitations.md)

 

<!---HONumber=July15_HO2-->