<properties 
	pageTitle="Создание фильтров с помощью пакета SDK для .NET служб мультимедиа Azure" 
	description="В этом разделе описывается создание фильтров, с помощью которых клиент может передавать определенные секции потока. Для достижения такой выборочной потоковой передачи службы мультимедиа создают динамические манифесты." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="04/18/2016"
	ms.author="juliako;cenkdin"/>


#Создание фильтров с помощью пакета SDK для .NET служб мультимедиа Azure

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-dynamic-manifest.md)
- [REST](media-services-rest-dynamic-manifest.md)

Начиная с версии 2.11, службы мультимедиа позволяют определять фильтры для активов. Эти фильтры представляют собой правила на стороне сервера, позволяющие пользователям выполнять следующие действия: воспроизведение только части видео (вместо целого) или указание подмножества представлений аудио и видео, которые может обрабатывать устройство клиента (вместо всех представлений, связанных с активом). Такая фильтрация активов достигается с помощью **динамических манифестов**, которые создаются по запросу клиента для потоковой передачи видео на основе указанных фильтров.

Более подробные сведения, относящиеся к фильтрам и динамическому манифесту, см. в разделе [Обзор динамических манифестов](media-services-dynamic-manifest-overview.md).

В этом разделе показано, как использовать пакет SDK .NET служб мультимедиа для создания, обновления и удаления фильтров.


Обратите внимание, что при обновлении фильтра может понадобиться до 2 минут на обновление правил конечной точкой потоковой передачи. Если содержимое было обработано с помощью данного фильтра (и кэшировано на прокси-серверах и в кэшах CDN), обновление этого фильтра может привести к сбоям проигрывателя. Рекомендуется очистить кэш после обновления фильтра. Если такой вариант невозможен, рассмотрите возможность использования другого фильтра.

##Типы, используемые для создания фильтров

При создании фильтров используются следующие типы:

- **IStreamingFilter**. В основе этого типа лежит следующий интерфейс API REST: [Filter](http://msdn.microsoft.com/library/azure/mt149056.aspx).
- **IStreamingAssetFilter**. В основе этого типа лежит следующий интерфейс API REST: [AssetFilter](http://msdn.microsoft.com/library/azure/mt149053.aspx).
- **PresentationTimeRange**. В основе этого типа лежит следующий интерфейс API REST: [PresentationTimeRange](http://msdn.microsoft.com/library/azure/mt149052.aspx).
- **FilterTrackSelectStatement** и **IFilterTrackPropertyCondition**. В основе этих типов лежат следующие интерфейсы API REST: [FilterTrackSelect и FilterTrackPropertyCondition](http://msdn.microsoft.com/library/azure/mt149055.aspx).


##Создание, обновление, чтение и удаление глобальных фильтров

Следующий код демонстрирует, как использовать .NET для создания, обновления, чтения и удаления фильтров ресурсов-контейнеров.
	
	string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();
	            
	List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();
	
	FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
	filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
	filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
	filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
	filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
	filterTrackSelectStatements.Add(filterTrackSelectStatement);
	
	// Create
	IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);
	
	// Update
	filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
	filter.Update();
	
	// Read
	var filterUpdated = _context.Filters.FirstOrDefault();
	Console.WriteLine(filterUpdated.Name);

	// Delete
	filter.Delete();


##Создание, обновление, чтение и удаление фильтров ресурсов-контейнеров

Следующий код демонстрирует, как использовать .NET для создания, обновления, чтения и удаления фильтров ресурсов-контейнеров.

	
	string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
	var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);
	
	string filterName = "AssetFilter_" + Guid.NewGuid().ToString();
	
	    
	// Create
	IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
	                                    new PresentationTimeRange(), 
	                                    new List<FilterTrackSelectStatement>());
	
	// Update
	filter.PresentationTimeRange = 
	        new PresentationTimeRange(start: 6000000000, end: 72000000000);
	
	filter.Update();
	
	// Read
	asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
	var filterUpdated = asset.AssetFilters.FirstOrDefault();
	Console.WriteLine(filterUpdated.Name);
	
	// Delete
	filterUpdated.Delete();
	



##Построение URL-адресов потоковой передачи с использованием фильтров

Сведения о публикации и доставке активов см. в разделе [Обзор доставки содержимого клиентам](media-services-deliver-content-overview.md).


Следующие примеры показывают, как добавлять фильтры к URL-адресам потоковой передачи.

**MPEG DASH**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


**HDS**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f, filter=MyFilter)


##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##См. также 

[Обзор динамических манифестов](media-services-dynamic-manifest-overview.md)
 

<!---HONumber=AcomDC_0420_2016-->