<properties 
	pageTitle="Как создать обработчик мультимедиа - Azure" 
	description="Узнайте, как создать компонент обработчика мультимедиа для кодирования, преобразования формата, шифрования или расшифровки мультимедийного контента служб мультимедиа Azure. Примеры кода написаны на языке C# и используют пакет SDK служб мультимедиа для .NET." 
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
	ms.date="02/10/2015" 
	ms.author="juliako"/>


#Практическое руководство. Получение экземпляра обработчика мультимедиа

Это одна из статей серии [Рабочий процесс для видео по запросу в службах мультимедиа](media-services-video-on-demand-workflow.md) . 

##Обзор

В службах мультимедиа обработчик мультимедиа является компонентом, который работает со специфическими задачами обработки, такими как кодирование, преобразование формата, шифрование или расшифровка мультимедийного контента. Обработчик мультимедиа обычно создается при создании задачи для кодирования, шифрования или преобразования формата мультимедийного контента.

В приведенной ниже таблице указаны имена и описания для всех доступных обработчиков мультимедиа.

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>Имя обработчика мультимедиа</th>
       <th>Описание</th>
	<th>Дополнительные сведения</th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Кодировщик службы мультимедиа Azure</td>
       <td>Позволяет выполнять задачи кодирования с использованием кодировщика службы мультимедиа Azure.</td>
       <td><a href="http://msdn.microsoft.com/library/jj129582.aspx"> Строки предустановок задачи для кодировщика службы мультимедиа Azure</a></td>
    </tr>
    <tr>
       <td>Рабочий процесс Premium кодировщика службы мультимедиа</td>
       <td>Позволяет выполнять задачи кодирования с использованием рабочего процесса Premium кодировщика службы мультимедиа.</td>
       <td><a href="http://azure.microsoft.com/documentation/articles/media-services-encode-with-premium-workflow/">Кодирование с помощью рабочего процесса Premium кодировщика службы мультимедиа.</a></td>
    </tr>    
	<tr>
        <td>Azure Media Indexer</td>
        <td>Позволяет сделать мультимедийные файлы и контент доступными для поиска, а также создавать дорожки и ключевые слова для субтитров.</td>
		<td><a href="http://azure.microsoft.com/documentation/articles/media-services-index-content/">Индексирование файлов мультимедиа с помощью индексатора мультимедиа Azure</a>.</td>
    </tr>
    <tr>
        <td>Microsoft Azure Media Packager</td>
        <td>Позволяет преобразовать мультимедийные активы из формата MP4 в формат Smooth Streaming. Позволяет также преобразовать мультимедийные активы из формата Smooth Streaming в формат Apple HTTP Live Streaming (HLS).</td>
		<td><a href="http://msdn.microsoft.com/library/hh973635.aspx">Строки предустановок задачи для Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Microsoft Azure Media Encryptor</td>
        <td>Позволяет шифровать мультимедийные активы с использованием PlayReady Protection.</td>
        <td><a href="http://msdn.microsoft.com/library/hh973610.aspx">Строки предустановок задачи для Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Расшифровка хранилища</td>
        <td>Позволяет расшифровать мультимедийные активы, которые были зашифрованы с помощью шифрования хранилища.</td>
		<td>Недоступно</td>
    </tr>  </tbody>
</table>

<br />

##Получение MediaProcessor

Приведенные ниже методы показывают, как получить экземпляр обработчика мультимедиа. В примере кода предполагается использование переменной уровня модуля с именем **_context** для ссылки на контекст сервера, как описано в разделе [Практическое руководство: Подключение к службам мультимедиа программными средствами].

	private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	    return processor;
	}

##Дальнейшие действия
Теперь, когда вы знаете, как получить экземпляр обработчика мультимедиа, перейдите в раздел [Кодировка актива][], в котором будет показано, как использовать кодировщик службы мультимедиа Azure для кодирования актива.

[Кодировка актива]: media-services-encode-asset.md
[Строки предустановок задачи для кодировщика службы мультимедиа Azure]: http://msdn.microsoft.com/library/jj129582.aspx
[Практическое руководство. Подключение к службам мультимедиа программными средствами]: ../media-services-set-up-computer/


<!--HONumber=52-->