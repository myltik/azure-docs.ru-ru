<properties urlDisplayName="Create a Media Processor" pageTitle="Как создать обработчик мультимедиа - Azure" metaKeywords="" description="Узнайте, как создать компонент процессора мультимедиа для кодирования, преобразования формата, шифрования или расшифровки мультимедийного контента служб мультимедиа Azure. Примеры кода написаны на языке C# и используют пакет SDK служб мультимедиа для .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Get a Media Processor Instance" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





<h1>Практическое руководство: Получение экземпляра обработчика мультимедиа.</h1>
Эта статья является частью серии вводных статей о программировании служб мультимедиа в Azure. Предыдущий раздел: [Практическое руководство. Создание зашифрованного ресурса и его отправка в хранилище](../media-services-create-encrypted-asset-upload-storage/).

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
       <td>Azure Media Encoder</td>
       <td>Позволяет выполнять задачи кодирования с использованием кодировщика мультимедиа.</td>
       <td><a href="http://msdn.microsoft.com/ru-ru/library/jj129582.aspx"> Строки предустановок задачи для кодировщика мультимедиа Azure</a></td>
    </tr>
    <tr>
        <td>Microsoft Azure Media Packager</td>
        <td>Позволяет преобразовать мультимедийные активы из формата MP4 в формат Smooth Streaming. Позволяет также преобразовать мультимедийные активы из формата Smooth Streaming в формат Apple HTTP Live Streaming (HLS).</td>
		<td><a href="http://msdn.microsoft.com/ru-ru/library/hh973635.aspx">Строки предустановок задачи для Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Microsoft Azure Media Encryptor</td>
        <td>Позволяет шифровать мультимедийные активы с использованием PlayReady Protection.</td>
        <td><a href="http://msdn.microsoft.com/ru-ru/library/hh973610.aspx">Строки предустановок задачи для Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Azure Media Indexer</td>
        <td>Позволяет сделать мультимедийные файлы и контент доступными для поиска, а также создавать дорожки и ключевые слова для субтитров.</td>
		<td>Недоступно</td>
    </tr>
    <tr>
        <td>Расшифровка хранилища</td>
        <td>Позволяет расшифровать мультимедийные активы, которые были зашифрованы с помощью шифрования хранилища.</td>
		<td>Недоступно</td>
    </tr>  </tbody>
</table>

<br />

Приведенные ниже методы показывают, как получить экземпляр обработчика мультимедиа. В примере кода предполагается использование переменной уровня модуля с именем **_context** для ссылки на контекст сервера, как описано в статье [Практическое руководство. Подключение к службам мультимедиа программными средствами].

<pre><code>
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}
</code></pre>

<h2>Дальнейшие действия</h2>
Теперь, когда вы знаете, как получить экземпляр обработчика мультимедиа, перейдите к статье [Практическое руководство. Кодировка ресурсов][], в которой показано, как использовать кодировщик мультимедиа Azure для кодирования ресурса.

[Кодировка ресурса]: ../media-services-encode-asset/
[Строки предустановок задачи для кодировщика мультимедиа Azure]: http://msdn.microsoft.com/ru-ru/library/jj129582.aspx
[Практическое руководство. Подключение к службам мультимедиа программными средствами]: ../media-services-set-up-computer/

<!--HONumber=35.1-->
