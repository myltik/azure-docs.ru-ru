<properties 
	pageTitle="Как создать обработчик мультимедиа — Azure" 
	description="Узнайте, как создать компонент процессора мультимедиа для кодирования, преобразования формата, шифрования или расшифровки мультимедийного контента служб мультимедиа Azure. Примеры кода написаны на языке C# и используют пакет SDK служб мультимедиа для .NET." 
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
	ms.date="05/13/2015" 
	ms.author="juliako"/>


#Получение экземпляра процессора мультимедиа

Это одна из статей цикла [Рабочий процесс для видео по запросу в службах мультимедиа](media-services-video-on-demand-workflow.md).

##Обзор

В службах мультимедиа обработчик мультимедиа является компонентом, который работает со специфическими задачами обработки, такими как кодирование, преобразование формата, шифрование или расшифровка мультимедийного контента. Обработчик мультимедиа обычно создается при создании задачи для кодирования, шифрования или преобразования формата мультимедийного контента.

В приведенной ниже таблице указаны имена и описания для всех доступных обработчиков мультимедиа.

Имя обработчика мультимедиа|Описание|Дополнительные сведения
---|---|---
Azure Media Encoder|Позволяет выполнять задачи кодирования с использованием обработчика мультимедиа Azure.| [Строки предустановок задачи для Azure Media Encoder](http://msdn.microsoft.com/library/jj129582.aspx)
Рабочий процесс Premium обработчика мультимедиа|Позволяет выполнять задачи кодирования с использованием рабочего процесса Premium обработчика мультимедиа.|[Кодирование с помощью расширенного рабочего процесса кодировщика мультимедиа](media-services-encode-with-premium-workflow.md)
Azure Media Indexer|Позволяет сделать мультимедийные файлы и контент доступными для поиска, а также создавать дорожки и ключевые слова для субтитров.|[Индексирование файлов мультимедиа с помощью индексатора мультимедийных данных Azure](media-services-index-content.md)
Microsoft Azure Media Packager|Позволяет преобразовать мультимедийные активы из формата MP4 в формат Smooth Streaming. Позволяет также преобразовать мультимедийные активы из формата Smooth Streaming в формат Apple HTTP Live Streaming (HLS).|[Строки предустановок задачи для Azure Media Packager](http://msdn.microsoft.com/library/hh973635.aspx)
Microsoft Azure Media Encryptor|Позволяет шифровать мультимедийные активы с использованием PlayReady Protection.|[Строки предустановок задачи для Azure Media Packager](http://msdn.microsoft.com/library/hh973610.aspx)
Azure Media Hyperlapse (предварительная версия)|Позволяет сгладить «неровности» видео с помощью стабилизации видео. Также позволяет ускорить содержимое в виде пригодного к использованию клипа.|[Azure Media Hyperlapse](http://go.microsoft.com/fwlink/?LinkId=613274)
Расшифровка хранилища|Позволяет расшифровать мультимедийные активы, которые были зашифрованы с помощью шифрования хранилища.|Недоступно

##Получение MediaProcessor

Приведенные ниже методы показывают, как получить экземпляр обработчика мультимедиа. В примере кода предполагается использование переменной уровня модуля с именем **\_context** для ссылки на контекст сервера, как описано в разделе [Практическое руководство. Подключение к службам мультимедиа программными средствами].

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	    return processor;
	}

##Дальнейшие действия
Теперь, когда вы знаете, как получить экземпляр обработчика мультимедиа, перейдите в раздел [Кодировка актива][], в котором будет показано, как использовать кодировщик Azure Media для кодирования актива.

[Кодировка актива]: media-services-encode-asset.md
[Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[Практическое руководство. Подключение к службам мультимедиа программными средствами]: ../media-services-set-up-computer/

<!---HONumber=August15_HO6-->