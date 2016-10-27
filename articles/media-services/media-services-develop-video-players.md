<properties 
    pageTitle="Разработка приложений видеопроигрывателя" 
    description="В этом разделе приведены ссылки на платформы проигрывателей и подключаемые модули, которые можно использовать для разработки собственных клиентских приложений, способных использовать потоковое мультимедийное содержимое из служб мультимедиа." 
    authors="Juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>



#<a name="develop-video-player-applications"></a>Разработка приложений видеопроигрывателя

##<a name="overview"></a>Обзор

Службы мультимедиа Azure предоставляют средства, необходимые для создания динамических многофункциональных клиентских приложений проигрывателя для большинства платформ, включая: устройства iOS, устройства Android, Windows, Windows Phone, Xbox и абонентские приставки. В этом разделе приведены также ссылки на пакеты SDK и платформы проигрывателей, которые можно использовать для разработки собственных клиентских приложений, способных использовать потоковое мультимедийное содержимое из служб мультимедиа Azure.

##<a name="azure-media-player"></a>Azure Media Player

[Мультимедиапроигрыватель Azure](http://aka.ms/ampinfo) — это веб-проигрыватель, созданный для воспроизведения мультимедийного содержимого из служб мультимедиа Microsoft Azure в разных браузерах и на разных устройствах. Azure Media Player использует такие отраслевые стандарты, как HTML5, Media Source Extensions (MSE) и Encrypted Media Extensions (EME), для оптимальной адаптивной потоковой передачи. Если эти стандарты недоступны на устройстве или в браузере, Azure Media Player использует Flash и Silverlight. Независимо от используемой технологии воспроизведения, разработчики получают единый интерфейс JavaScript для доступа к интерфейсам API. Это позволяет службам мультимедиа Azure обслуживать содержимое для воспроизведения на широком спектре устройств и в различных браузерах без дополнительных усилий.

Службы мультимедиа Microsoft Azure поддерживают форматы потоковой передачи DASH, Smooth Streaming и HLS для воспроизведения содержимого. Azure Media Player учитывает эти различные форматы и автоматически воспроизводит наиболее подходящую ссылку в зависимости от возможностей платформы или браузера. Службы мультимедиа Microsoft Azure также позволяют динамически шифровать ресурсы с использованием PlayReady или 128-битного шифрования AES. Azure Media Player поддерживает расшифровку содержимого, зашифрованного с помощью PlayReady и 128-битного алгоритма шифрования AES, если заданы соответствующие параметры. 

Дополнительные сведения

- [Azure Media Player](http://aka.ms/ampinfo)
- [Документация Azure Media Player](http://aka.ms/ampdocs) 
- [Блог о начале работы с Azure Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
- [Подпишитесь, чтобы оставаться в курсе последних новостей о мультимедиапроигрывателе Azure](http://aka.ms/ampsignup)
- [Запрос новых функций, ваши идеи и отзывы](http://aka.ms/ampuservoice ) 


##<a name="other-tools-for-creating-player-applications"></a>Другие средства для создания приложений проигрывателя

Также можно использовать любой из следующих пакетов SDK:

- [клиентский пакет SDK Smooth Streaming;](http://www.iis.net/downloads/microsoft/smooth-streaming) 
- [приложение магазина Windows с потоковой передачей Smooth Streaming;](media-services-build-smooth-streaming-apps.md)
- [платформа проигрывателя Microsoft Media Platform;](http://playerframework.codeplex.com/) 
- [документация по платформе проигрывателя HTML5;](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
- [подключаемый модуль Microsoft Smooth Streaming для OSMF;](https://www.microsoft.com/download/details.aspx?id=36057) 
- [Лицензирование пакета для портирования клиента бесперебойной потоковой передачи Microsoft® Smooth Streaming](http://aka.ms/sspk) 
- [Разработка приложений для воспроизведения видео на XBOX](http://xbox.create.msdn.com/) 
 

##<a name="advertising"></a>Реклама

Службы мультимедиа Azure поддерживают вставку рекламы с помощью платформ проигрывателя Windows Media. Платформы проигрывателя с поддержкой рекламы доступны для устройств Windows 8, Silverlight, Windows Phone 8 и iOS. Каждая платформа проигрывателя содержит образец кода, в котором демонстрируется реализация приложения проигрывателя. В мультимедиасодержимое можно вставить три вида рекламы:

линейная — реклама на весь кадр, которая приостанавливает основное видео;

нелинейная — наложенная реклама, отображаемая во время воспроизведения основного видео, обычно это логотип или другое статическое изображение, размещенное в проигрывателе;

сопутствующая — рекламные объявления, отображаемые за пределами проигрывателя.

Рекламу можно размещать в любой временной точке основного видео. Необходимо сообщить проигрывателю, когда будет показана реклама и какие рекламные объявления следует показать. Для этого используется набор стандартных XML-файлов: Video Ad Service Template (VAST), Digital Video Multiple Ad Playlist (VMAP), Media Abstract Sequencing Template (MAST) и Digital Video Player Ad Interface Definition (VPAID). VAST-файлы указывают, какая реклама будет отображаться. VMAP-файлы указывают, когда следует воспроизводить различные рекламные ролики, и содержат XML-код VAST. MAST-файлы представляют собой еще один способ упорядочивания рекламных объявлений, которые также могут содержать XML VAST. VPAID-файлы определяют интерфейс между видеопроигрывателем и рекламой или сервером рекламы. Дополнительные сведения см. в статье [Вставка рекламы на стороне клиента](https://msdn.microsoft.com/library/dn387398.aspx).

Сведения о поддержке субтитров и рекламы в динамических потоковых видео см. в статье [Общие сведения о динамической потоковой передаче с использованием служб мультимедиа Azure](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad).


##<a name="media-services-learning-paths"></a>Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>См. также

[Встраивание адаптивного потокового видео MPEG-DASH в приложение HTML5 с помощью DASH.js](media-services-embed-mpeg-dash-in-html5.md)

[Репозиторий dash.js на GitHub](https://github.com/Dash-Industry-Forum/dash.js)
 



<!--HONumber=Oct16_HO2-->


