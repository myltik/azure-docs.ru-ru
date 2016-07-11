<properties 
	pageTitle="Создание обработчика мультимедиа | Microsoft Azure" 
	description="Узнайте, как создать компонент обработчика мультимедиа для кодирования, преобразования формата, шифрования или расшифровки мультимедийного контента служб мультимедиа Azure. Примеры кода написаны на языке C# и используют пакет SDK служб мультимедиа для .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2016" 
	ms.author="juliako"/>


#Получение экземпляра процессора мультимедиа

> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [REST](media-services-rest-get-media-processor.md)


##Обзор

В службах мультимедиа обработчик мультимедиа является компонентом, который работает со специфическими задачами обработки, такими как кодирование, преобразование формата, шифрование или расшифровка мультимедийного контента. Обработчик мультимедиа обычно создается при создании задачи для кодирования, шифрования или преобразования формата мультимедийного контента.

В приведенной ниже таблице указаны имена и описания для всех доступных обработчиков мультимедиа.

Имя обработчика мультимедиа|Описание|Дополнительные сведения
---|---|---
Стандартный кодировщик служб мультимедиа|Предоставляет стандартные возможности для кодирования по запросу. |[Обзор и сравнение кодировщиков мультимедиа Azure по запросу](media-services-encode-asset.md)
Расширенный рабочий процесс кодировщика мультимедиа|Позволяет выполнять задачи кодирования с использованием расширенного рабочего процесса кодировщика мультимедиа.|[Обзор и сравнение кодировщиков мультимедиа Azure по запросу](media-services-encode-asset.md)
Azure Media Indexer| Позволяет сделать мультимедийные файлы и контент доступными для поиска, а также создавать дорожки и ключевые слова для субтитров.|[Azure Media Indexer](media-services-index-content.md)
Azure Media Hyperlapse (предварительная версия)|Позволяет сгладить "неровности" видео с помощью стабилизации видео. Также позволяет ускорить содержимое в виде пригодного к использованию клипа.|[Azure Media Hyperlapse](media-services-hyperlapse-content.md)
Кодировщик мультимедиа Azure|Цена снижена
Расшифровка хранилища| Цена снижена|
Azure Media Packager|Цена снижена|
Azure Media Encryptor|Цена снижена|

##Получение обработчика мультимедиа

Приведенные ниже методы показывают, как получить экземпляр обработчика мультимедиа. В примере кода предполагается использование переменной уровня модуля с именем **\_context** для ссылки на контекст сервера (см. статью [Практическое руководство. Подключение к службам мультимедиа программными средствами](media-services-dotnet-connect-programmatically.md)).

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
		var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
		ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
		
		if (processor == null)
		throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
		
		return processor;
	}


##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Дальнейшие действия

Теперь, когда вы знаете, как получить экземпляр обработчика мультимедиа, перейдите в раздел [Кодировка актива](media-services-dotnet-encode-with-media-encoder-standard.md), в котором будет показано, как использовать стандартный кодировщик мультимедиа для кодирования ресурса-контейнера.

<!---HONumber=AcomDC_0629_2016-->