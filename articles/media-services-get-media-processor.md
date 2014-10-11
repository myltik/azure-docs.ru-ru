<properties linkid="develop-media-services-how-to-guides-create-media-processor" urlDisplayName="Create a Media Processor" pageTitle="How to Create a Media Processor - Azure" metaKeywords="" description="Learn how to create a media processor component to encode, convert format, encrypt, or decrypt media content for Azure Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Get a Media Processor Instance" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"/>

# Практическое руководство: Получение экземпляра процессора мультимедиа.

Эта статья является частью серии вводных статей о программировании служб мультимедиа в Azure. Предыдущий раздел [Практическое руководство: Создание зашифрованного актива и его отправка в хранилище][]

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
       <td><a href="http://msdn.microsoft.com/en-us/library/jj129582.aspx"> Строки предустановок задачи для Azure Media Encoder</a></td>
    </tr>
    <tr>
        <td>Azure Media Packager</td>
        <td>Позволяет преобразовать мультимедийные активы из формата MP4 в формат Smooth Streaming. Позволяет также преобразовать мультимедийные активы из формата Smooth Streaming в формат Apple HTTP Live Streaming (HLS).</td>
        <td><a href="http://msdn.microsoft.com/en-us/library/hh973635.aspx">Строки предустановок задачи для Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Azure Media Encryptor</td>
        <td>Позволяет шифровать мультимедийные активы с использованием PlayReady Protection.</td>
        <td><a href="http://msdn.microsoft.com/en-us/library/hh973610.aspx">Строки предустановок задачи для Azure Media Packager</a></td>
    </tr>
    <tr>
        <td>Расшифровка хранилища</td>
        <td>Позволяет расшифровать мультимедийные активы, которые были зашифрованы с помощью шифрования хранилища.</td>
        <td>Недоступно</td>
    </tr>
  </tbody>
</table>                                                

<br />

Приведенные ниже методы показывают, как получить экземпляр обработчика мультимедиа. В примере кода предполагается использование переменной уровня модуля с именем \*\*\_context\*\* для ссылки на контекст сервера, как описано в разделе [Практическое руководство: Подключение к службам мультимедиа программными средствами][].

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
         var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }

## Дальнейшие действия

Теперь, когда вы знаете, как получить экземпляр обработчика мультимедиа, перейдите в раздел [Кодировка актива][], в котором будет показано, как использовать кодировщик Azure Media для кодирования актива.

  [Практическое руководство: Создание зашифрованного актива и его отправка в хранилище]: http://go.microsoft.com/fwlink/?LinkID=301733&clcid=0x409
  [Строки предустановок задачи для Azure Media Encoder]: http://msdn.microsoft.com/en-us/library/jj129582.aspx
  [Строки предустановок задачи для Azure Media Packager]: http://msdn.microsoft.com/en-us/library/hh973635.aspx
  [1]: http://msdn.microsoft.com/en-us/library/hh973610.aspx
  [Практическое руководство: Подключение к службам мультимедиа программными средствами]: http://www.windowsazure.com/en-us/develop/media-services/how-to-guides/set-up-computer-for-media-services
  [Кодировка актива]: http://go.microsoft.com/fwlink/?LinkId=301753
