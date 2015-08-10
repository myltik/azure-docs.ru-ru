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
	ms.date="07/23/2015" 
	ms.author="juliako"/>

#Кодирование содержимого по запросу с помощью служб мультимедиа Azure

Это один из разделов серии [Рабочий процесс для видео по запросу в службах мультимедиа](media-services-video-on-demand-workflow.md).

##Обзор

Службы мультимедиа поддерживают следующие кодировщики:

- [Стандартный кодировщик служб мультимедиа](#media_encoder_standard)
- [Azure Media Encoder](#azure_media_encoder)
- [Рабочий процесс Premium обработчика мультимедиа](#media_encoder_premium_workflow)

В [следующем разделе](#compare_encoders) сравниваются возможности поддерживаемых кодировщиков.

По умолчанию каждая учетная запись служб мультимедиа может выполнять одну активную задачу кодирования в текущий момент. Можно зарезервировать единицы кодирования, которые позволят выполнять несколько задач кодирования одновременно, по одной для каждой приобретенной единицы. Информацию о масштабировании единиц кодирования см. в следующих статьях о **портале** и **.NET**.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

##<a id="media_encoder_standard"></a>Стандартный кодировщик мультимедиа

[Форматы, поддерживаемые стандартным кодировщиком служб мультимедиа](media-services-media-encoder-standard-formats.md): описание форматов файлов и потоковой передачи, поддерживаемых **стандартным кодировщиком мультимедиа**.

**Стандартный кодировщик мультимедиа** настраивается с помощью либо одной из описанных [здесь](http://go.microsoft.com/fwlink/?LinkId=618336) предустановок кодировщика, либо пользовательских предустановок, основанных на [этих](http://go.microsoft.com/fwlink/?LinkId=618336) предустановках.

Дополнительную информацию см. в [этом блоге](http://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).

##<a id="azure_media_encoder"></a> Azure Media Encoder

[Форматы, поддерживаемые кодировщиком служб мультимедиа](media-services-azure-media-encoder-formats.md): описание форматов файлов и потоковой передачи, поддерживаемых **кодировщиком служб мультимедиа Azure** (Azure Media Encoder).

**Кодировщик служб мультимедиа Azure** настраивается с помощью одной из предустановленных строк кодировщика, описанных [здесь](https://msdn.microsoft.com/library/azure/dn619392.aspx). Также [здесь](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder) можно получить фактические файлы предустановок для кодировщика служб мультимедиа Azure.

Кодируйте файлы с помощью **кодировщика службы мультимедиа Azure** через **портал управления Azure**, **.NET** или **API REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

####Другие связанные разделы

[Динамическая упаковка](https://msdn.microsoft.com/library/azure/jj889436.aspx): описание кодирования в MP4-файлы с адаптивной скоростью и динамической обработки потоков Smooth Streaming, Apple HLS или MPEG-DASH.

[Управление именами выходных файлов кодировщика служб мультимедиа](https://msdn.microsoft.com/library/azure/dn303341.aspx): описание соглашения об именовании файлов, используемого кодировщиком Azure Media Encoder, и способа изменения имен выходных файлов.

[Кодирование мультимедиа с помощью Dolby Digital Plus](media-services-encode-with-dolby-digital-plus.md): описание кодирования звуковых дорожек с использованием Dolby Digital Plus.


##<a id="media_encoder_premium_wokrflow"></a> Рабочий процесс Premium обработчика мультимедиа 

**Примечание**. Обработчик мультимедиа рабочего процесса Premium Media Encoder, рассмотренный в данном разделе, недоступен в Китае.

[Форматы, которые поддерживаются расширенным рабочим процессом кодировщика служб мультимедиа](media-services-premium-workflow-encoder-formats.md): описание файловых форматов и кодеков, поддерживаемых **расширенным рабочим процессом кодировщика служб мультимедиа**.

**Расширенный рабочий процесс кодировщика служб мультимедиа** настраивается с помощью сложных рабочих процессов. Файлы рабочих процессов можно создавать с помощью [конструктора рабочих процессов](media-services-workflow-designer.md).

Файлы используемого по умолчанию рабочего процесса можно получить [здесь](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). Папка также содержит описание этих файлов.

Кодирование с **расширенным рабочим процессом кодировщика мультимедиа** с использованием **.NET**. Дополнительные сведения см. в статье [Дополнительное кодирование с помощью расширенного рабочего процесса кодировщика мультимедиа](media-services-encode-with-premium-workflow.md).
 

##<a id="compare_encoders"></a>Сравнение кодировщиков

###<a id="billing"></a>Индикатор выставления счетов, используемый каждым кодировщиком

<table border="1">
<tr><th>Имя обработчика мультимедиа</th><th>Применимые цены</th><th>Примечания</th></tr>
<tr><td><b>Microsoft Azure Media Encryptor</b></td><td>КОДИРОВЩИК ПРЕЖНИХ ВЕРСИЙ</td><td>Плата за выполнение задач кодирования будет взиматься, исходя из суммарного размера входных и выходных ресурсов в гигабайтах, по тарифу, указанному <a href="http://azure.microsoft.com/pricing/details/media-services/">здесь</a> в столбце «КОДИРОВЩИК ПРЕЖНИХ ВЕРСИЙ».</td></tr>
<tr><td><b>Azure Media Encoder</b></td><td>КОДИРОВЩИК</td><td>Плата за выполнение задач кодирования будет взиматься, исходя из размера выходного ресурса в гигабайтах, по тарифу, указанному <a href="http://azure.microsoft.com/pricing/details/media-services/">здесь</a> в столбце «КОДИРОВЩИК».</td></tr>
<tr><td><b>Стандартный кодировщик служб мультимедиа</b></td><td>КОДИРОВЩИК</td><td>Плата за выполнение задач кодирования будет взиматься, исходя из размера выходного ресурса в гигабайтах, по тарифу, указанному <a href="http://azure.microsoft.com/pricing/details/media-services/">здесь</a> в столбце «КОДИРОВЩИК».</td></tr>
<tr><td><b>Рабочий процесс Premium обработчика мультимедиа</b></td><td>РАСШИРЕННЫЙ КОДИРОВЩИК</td><td>Плата за выполнение задач кодирования будет взиматься, исходя из размера выходного ресурса в гигабайтах, по тарифу, указанному <a href="http://azure.microsoft.com/pricing/details/media-services/">здесь</a> в столбце «РАСШИРЕННЫЙ КОДИРОВЩИК».</td></tr>
</table>


В этом разделе сравниваются возможности кодировщика **Azure Media Encoder**, **расширенного рабочего процесса кодировщика служб мультимедиа** и **стандартного кодировщика служб мультимедиа**.


###Форматы входных данных

Контейнер ввода/ форматы файлов

<table border="1">
<tr><th>Контейнер ввода/ форматы файлов</th><th>Рабочий процесс Premium обработчика мультимедиа</th><th>Azure Media Encoder
</th><th>Стандартный кодировщик служб мультимедиа</th></tr>
<tr><td>Adobe® Flash® F4V</td><td>Да</td><td>Нет</td><td>Да</td></tr>
<tr><td>MXF/SMPTE 377M</td><td>Да</td><td>Ограничено</td><td>Да</td></tr>
<tr><td>GXF</td><td>Да</td><td>Нет</td><td>Да</td></tr>
<tr><td>Транспортные потоки MPEG-2</td><td>Да</td><td>Да</td><td>Да</td></tr>
<tr><td>Программные потоки MPEG-2</td><td>Да</td><td>Да</td><td>Да</td></tr>
<tr><td>MPEG-4/MP4</td><td>Да</td><td>Да</td><td>Да</td></tr>
<tr><td>Windows Media/ASF</td><td>Да</td><td>Да</td><td>Да</td></tr>
<tr><td>AVI (без сжатия 8 бит/10 бит)</td><td>Да</td><td>Да</td><td>Да</td></tr>
<tr><td>3GPP/3GPP2</td><td>Нет</td><td>Да</td><td>Да</td></tr>
<tr><td>Формат файлов Smooth Streaming (PIFF 1.3)</td><td>Нет</td><td>Да</td><td>Да</td></tr>
<tr><td><a href="https://msdn.microsoft.com/ru-ru/library/windows/desktop/dd692984(v=vs.85).aspx">Microsoft Digital Video Recording(DVR-MS)</a></td><td>Нет</td><td>Нет</td><td>Да</td></tr>
<tr><td>Matroska/WebM</td><td>Нет</td><td>Нет</td><td>Да</td></tr></table>

Входные видеокодеки

<table border="1">
<tr><th>Входные видеокодеки</th><th>Рабочий процесс Premium обработчика мультимедиа</th><th>Azure Media Encoder</th><th>Стандартный кодировщик служб мультимедиа</th></tr>
<tr><td>AVC 8-разрядный/10-разрядный, до 4:2:2, включая AVCIntra</td><td>Да</td><td>Только 8-разрядный 4:2:0</td><td>8 бит 4:2:0 и 4:2:2</td></tr>
<tr><td>Avid DNxHD (в MXF)</td><td>Да</td><td>Нет</td><td>Да</td></tr>
<tr><td>DVCPro/DVCProHD (в MXF)</td><td>Да</td><td>Нет</td><td>Да</td></tr>
<tr><td>JPEG2000</td><td>Да</td><td>Нет</td><td>Да</td></tr>
<tr><td>MPEG-2 (до 422 Profile и High Level, включая такие варианты, как XDCAM, XDCAM HD, XDCAM IMX, CableLabs® и D10)</td><td>Да</td><td>До 422 Profile</td><td>До 422 Profile</td></tr>
<tr><td>MPEG-1</td><td>Да</td><td>Да</td><td>Да</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Да</td><td>Да</td><td>Да</td></tr>
<tr><td>Canopus HQ/HQX</td><td>Нет</td><td>Да</td><td>Нет</td></tr>
<tr><td>MPEG-4, часть 2</td><td>Нет</td><td>Нет</td><td>Да</td></tr>
<tr><td><a href="https://en.wikipedia.org/wiki/Theora">Theora</a></td><td>Нет</td><td>Нет</td><td>Да</td></tr>
</table>

Входные аудиокодеки

<table border="1">
<tr><th>Входные аудиокодеки</th><th>Рабочий процесс Premium обработчика мультимедиа</th><th>Azure Media Encoder</th><th>Стандартный кодировщик служб мультимедиа</th></tr>
<tr><td>AES (SMPTE 331M и 302M, AES3-2003)</td><td>Да</td><td>Нет</td><td>Нет</td></tr>
<tr><td>Dolby® E</td><td>Да</td><td>Нет</td><td>Нет</td></tr>
<tr><td>Dolby® Digital (AC3)</td><td>Да</td><td>Да</td><td>Нет</td></tr>
<tr><td>Dolby® Digital Plus (E-AC3)</td><td>Да</td><td>Нет</td><td>Нет</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE и AAC-HEv2; до 5.1)</td><td>Да</td><td>Да</td><td>Да</td></tr>
<tr><td>MPEG Layer 2</td><td>Да</td><td>Да</td><td>Да</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Да</td><td>Да</td><td>Да</td></tr>
<tr><td>Windows Media Audio</td><td>Да</td><td>Да</td><td>Да</td></tr>
<tr><td>WAV/PCM</td><td>Да</td><td>Да</td><td>Да</td></tr>
<tr><td><a href="https://en.wikipedia.org/wiki/FLAC">FLAC</a></td><td>Нет</td><td>Нет</td><td>Да</td></tr>
<tr><td><a href="https://en.wikipedia.org/wiki/Opus_(audio_format)">Opus</a></td><td>Нет</td><td>Нет</td><td>Да</td></tr>
<tr><td><a href="https://en.wikipedia.org/wiki/Vorbis">Vorbis</a></td><td>Нет</td><td>Нет</td><td>Да</td></tr>
</table>

###Форматы выходных данных

Контейнер вывода/ форматы файлов

<table border="1">
<tr><th>Контейнер вывода/ форматы файлов</th><th>Рабочий процесс Premium обработчика мультимедиа</th><th>Azure Media Encoder</th><th>Стандартный кодировщик служб мультимедиа</th></tr>
<tr><td>Adobe® Flash® F4V</td><td>Да</td><td>Нет</td><td>Нет</td></tr>
<tr><td>MXF (OP1a, XDCAM и AS02)</td><td>Да</td><td>Нет</td><td>Нет</td></tr>
<tr><td>DPP (включая AS11)</td><td>Да</td><td>Нет</td><td>Нет</td></tr>
<tr><td>GXF</td><td>Да</td><td>Нет</td><td>Нет</td></tr>
<tr><td>MPEG-4/MP4</td><td>Да</td><td>Да</td><td>Да</td></tr>
<tr><td>MPEG-TS</td><td>Да</td><td>Нет</td><td>Да</td></tr>
<tr><td>Windows Media/ASF</td><td>Да</td><td>Да</td><td>Нет</td></tr>
<tr><td>AVI (без сжатия 8 бит/10 бит)</td><td>Да</td><td>Нет</td><td>Нет</td></tr>
<tr><td>Формат файлов Smooth Streaming (PIFF 1.3)</td><td>Да</td><td>Да</td><td>Нет</td></tr>
</table>

Выходные видеокодеки

<table border="1">
<tr><th>Выходные видеокодеки</th><th>Рабочий процесс Premium обработчика мультимедиа</th><th>Azure Media Encoder</th><th>Стандартный кодировщик служб мультимедиа</th></tr>
<tr><td>AVC (H.264; 8-разрядный; до High Profile, Level 5.2; 4K Ultra HD; AVC Intra)</td><td>Да</td><td>Только 8 бит 4:2:0 до 1080p</td><td>Только 8 бит 4:2:0</td></tr>
<tr><td>Avid DNxHD (в MXF)</td><td>Да</td><td>Нет</td><td>Нет</td></tr>
<tr><td>DVCPro/DVCProHD (в MXF)</td><td>Да</td><td>Нет</td><td>Нет</td></tr>
<tr><td>MPEG-2 (до 422 Profile и High Level, включая такие варианты, как XDCAM, XDCAM HD, XDCAM IMX, CableLabs® и D10)</td><td>Да</td><td>Нет</td><td>Нет</td></tr>
<tr><td>MPEG-1</td><td>Да</td><td>Нет</td><td>Нет</td></tr>
<tr><td>Windows Media Video/VC-1</td><td>Да</td><td>Да</td><td>Нет</td></tr>
<tr><td>Создание эскизов JPEG</td><td>Да</td><td>Да</td><td>Нет</td></tr>
</table>

Выходные аудиокодеки

<table border="1">
<tr><th>Выходные аудиокодеки</th><th>Рабочий процесс Premium обработчика мультимедиа</th><th>Azure Media Encoder</th><th>Стандартный кодировщик служб мультимедиа</th></tr>
<tr><td>AES (SMPTE 331M и 302M, AES3-2003)</td><td>Да</td><td>Нет</td><td>Нет</td></tr>
<tr><td>Dolby® Digital (AC3)</td><td>Да</td><td>Да</td><td>Нет</td></tr>
<tr><td>Dolby® Digital Plus (E-AC3) до 7.1</td><td>Да</td><td>До 5.1</td><td>Нет</td></tr>
<tr><td>AAC (AAC-LC, AAC-HE и AAC-HEv2; до 5.1)</td><td>Да</td><td>Да</td><td>Да</td></tr>
<tr><td>MPEG Layer 2</td><td>Да</td><td>Нет</td><td>Нет</td></tr>
<tr><td>MP3 (MPEG-1 Audio Layer 3)</td><td>Да</td><td>Нет</td><td>Нет</td></tr>
<tr><td>Windows Media Audio</td><td>Да</td><td>Да&lt;/td<td>Нет</td></tr>
</table>

##Связанные статьи

- [Знакомство с кодированием Premium в службах мультимедиа Azure](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [Использование кодирования Premium в службах мультимедиа Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
- [Квоты и ограничения](media-services-quotas-and-limitations.md)

 

<!---HONumber=July15_HO5-->