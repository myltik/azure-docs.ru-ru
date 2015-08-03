<properties 
	pageTitle="Форматы и кодеки рабочего процесса Premium Media Encoder" 
	description="В этом разделе содержится обзор форматов и кодеков рабочего процесса Premium Media Encoder" 
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

#Форматы и кодеки рабочего процесса Premium Media Encoder


**Примечание**. Обработчик мультимедиа рабочего процесса Premium Media Encoder, рассмотренный в данном разделе, недоступен в Китае.

Этот документ содержит список форматов входных и выходных файлов, а также список кодеков, которые поддерживает общедоступная предварительная версия **расширенного рабочего процесса кодировщика мультимедиа**.

[Входные форматы и кодеки расширенного рабочего процесса кодировщика мультимедиа](#input_formats)

[Выходные форматы и кодеки расширенного рабочего процесса кодировщика мультимедиа](#output_formats)

**Расширенный рабочий процесс кодировщика мультимедиа** поддерживает скрытые субтитры, описанные в [этом](#closed_captioning) разделе.


##<a id="input_formats"></a>Входные форматы и кодеки расширенного рабочего процесса кодировщика мультимедиа

В следующем разделе приводится список кодеков и форматов файлов, которые этот кодировщик мультимедиа поддерживает на входе.

###Контейнер ввода/ форматы файлов

- Adobe® Flash® F4V
- MXF/SMPTE 377M
- GXF
- Транспортные потоки MPEG-2
- Программные потоки MPEG-2
- MPEG-4/MP4
- Windows Media/ASF
- AVI (без сжатия 8 бит/10 бит)

###Входные видеокодеки

- AVC 8-разрядный/10-разрядный, до 4:2:2, включая AVCIntra
- Avid DNxHD (в MXF)
- DVCPro/DVCProHD (в MXF)
- JPEG2000
- MPEG-2 (до 422 Profile и High Level, включая такие варианты, как XDCAM, XDCAM HD, XDCAM IMX, CableLabs® и D10)
- MPEG-1
- Windows Media Video/VC-1

###Входные аудиокодеки

- AES (SMPTE 331M и 302M, AES3-2003)
- Dolby® E
- Dolby® Digital (AC3)
- AAC (AAC-LC, AAC-HE и AAC-HEv2; до 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Windows Media Audio
- WAV/PCM
 
##<a id="output_format"></a>Выходные форматы и кодеки расширенного рабочего процесса кодировщика мультимедиа

В следующем разделе приводится список кодеков и форматов файлов, которые этот кодировщик мультимедиа поддерживает на выходе.

###Контейнер вывода/ форматы файлов

- Adobe® Flash® F4V
- MXF (OP1a, XDCAM и AS02)
- DPP (включая AS11)
- GXF
- MPEG-4/MP4
- Windows Media/ASF
- AVI (без сжатия 8 бит/10 бит)
- Формат файлов Smooth Streaming (PIFF 1.3)
- MPEG-TS 


###Выходные видеокодеки

- AVC (H.264; 8-разрядный; до High Profile, Level 5.2; 4K Ultra HD; AVC Intra)
- Avid DNxHD (в MXF)
- DVCPro/DVCProHD (в MXF)
- MPEG-2 (до 422 Profile и High Level, включая такие варианты, как XDCAM, XDCAM HD, XDCAM IMX, CableLabs® и D10)
- MPEG-1
- Windows Media Video/VC-1
- Создание эскизов JPEG

###Выходные аудиокодеки

- AES (SMPTE 331M и 302M, AES3-2003)
- Dolby® Digital (AC3)
- Dolby® Digital Plus (E-AC3) до 7.1
- AAC (AAC-LC, AAC-HE и AAC-HEv2; до 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 Audio Layer 3)
- Windows Media Audio

##<a id="closed_captioning"></a>Поддержка скрытых титров

При приеме **расширенный рабочий процесс кодировщика мультимедиа** поддерживает:

1. файлы SCC
1. файлы SMPTE-TT
1. CEA-608/CEA-708 – переносятся как данные пользователя (сообщения SEI простейших потоков H.264, ATSC/53, SCTE20) или переносятся как вспомогательные данные в файлах MXF/GXF
1. Файлы субтитров STL

На выходе доступны следующие параметры:

1. Преобразование CEA-608 в CEA-708
1. Пропуск CEA-608/CEA-708 (встраиваются в сообщения SEI простейших потоков H.264 или переносятся как дополнительные данный в файлах MXF)
1. SCC
1. SMPTE Timed Text (из источника CEA-608 через SMPTE RP2052, включая создание файла DFXP)
1. Файл подзаголовка SRT
1. Потоки подзаголовка DVB

Примечание: доставки через потоковую передачу в службах мультимедиа Azure поддерживаются не все указанные выше форматы.

##Известные проблемы

Если входящее видео не содержит скрытых субтитров, выходящий ресурс по-прежнему будет содержать пустой файл TTML.

<!---HONumber=July15_HO4-->