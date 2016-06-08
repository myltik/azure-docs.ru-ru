<properties 
	pageTitle="Настройка кластеризации для кэша Redis для Azure уровня Премиум | Microsoft Azure" 
	description="Узнайте, как настроить кластеризацию и управлять ей для экземпляров кэша Redis для Azure уровня Премиум" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/23/2016" 
	ms.author="sdanie"/>

# Настройка кластеризации для кэша Redis для Azure уровня Премиум
Кэш Redis для Azure предлагает разные варианты кэша, которые обеспечивают гибкость в выборе размера и функций кэша, включая новый уровень Премиум.

Кэш Redis для Azure уровня Премиум включает кластеризацию, постоянное хранение данных и поддержку виртуальной сети. В этой статье описывается настройка кластеризации в экземпляре кэша Redis для Azure уровня Премиум.

Сведения об использовании других функций кэша уровня Премиум см. в статьях [Настройка постоянного хранения для кэша Redis для Azure уровня Премиум](cache-how-to-premium-persistence.md) и [Настройка поддержки виртуальной сети для кэша Redis для Azure уровня Премиум](cache-how-to-premium-vnet.md).

## Что такое кластер Redis?
Кэш Redis для Azure предлагает кластер Redis в том виде, как это [реализовано в Redis](http://redis.io/topics/cluster-tutorial). При использовании кластера Redis вы получаете следующие преимущества.

-	Возможность автоматически разделить набор данных между несколькими узлами. 
-	Возможность продолжить работу, когда на подмножестве узлов возникают ошибки или они не могут обмениваться данными с остальной частью кластера. 
-	Более высокая пропускная способность: пропускная способность линейно увеличивается по мере увеличения числа сегментов. 
-	Больший объем памяти: объем памяти линейно увеличивается по мере увеличения числа сегментов.  

Дополнительные сведения о размере и пропускной способности для кэшей уровня Премиум см. в разделе [Часто задаваемые вопросы о кэше Redis для Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

В Azure кластер Redis предоставляется в виде модели основного экземпляра и реплики. У каждого сегмента есть пара основного экземпляра и реплики. Репликацией управляет служба кэша Redis для Azure.

## Кластеризация
Кластер включается во время создания кэша в колонке **Новый кэш Redis**. Для создания кэша войдите на [портал Azure](https://portal.azure.com), щелкните **Создать** > **Данные+хранилище** > **Кэш Redis**.

![Создание кэша Redis][redis-cache-new-cache-menu]

Чтобы настроить кластер, сначала выберите один из кэшей категории **Премиум** в колонке **Выбор ценовой категории**.

![Выберите ценовую категорию][redis-cache-premium-pricing-tier]

Кластер настраивается в колонке **Кластер Redis**.

![Кластеризация][redis-cache-clustering]

Кластер может включать до 10 сегментов. Щелкните **Включено**, передвиньте ползунок или введите число от 1 до 10 для поля **Число сегментов** и нажмите кнопку **ОК**.

Каждый сегмент представляет собой пару основного экземпляра кэша и реплики кэша, которыми управляет Azure. Общий размер кэша вычисляется путем умножения количества сегментов на размер кэша, выбранный в ценовой категории.

![Кластеризация][redis-cache-clustering-selected]

После создания кэша вы подключаетесь к нему и пользуетесь им как некластеризованным кэшем, и Redis будет распространять данные по сегментам кэша. Если диагностика [включена](cache-how-to-monitor.md#enable-cache-diagnostics), метрики собираются отдельно для каждого сегмента и [отображаются](cache-how-to-monitor.md) в колонке кэша Redis.

Пример кода по работе с кластеризацией клиента StackExchange.Redis см. в разделе [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) примера [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

<a name="move-exceptions"></a>

>[AZURE.IMPORTANT] Подключаясь к кэшу Redis для Azure с включенной кластеризацией и использованием функции StackExchange.Redis, вы можете столкнуться с проблемой и получить исключения `MOVE`. Это связано с тем, что клиент кэша StackExchange.Redis собирает информацию об узлах в кластере кэша в течение короткого интервала. Исключения могут возникать, если вы подключаетесь к кэшу впервые и осуществляете вызовы в кэш, прежде чем клиент закончит собирать информацию. Самый простой способ решения этой проблемы в приложении — после подключения к кэшу подождать одну секунду, прежде чем осуществлять вызовы в кэш. Для этого добавьте параметр `Thread.Sleep(1000)`, как показано в приведенном ниже примере кода. Обратите внимание, что параметр `Thread.Sleep(1000)` применяется только при первом подключении к кэшу. Дополнительные сведения см. в разделе [StackExchange.Redis.RedisServerException — MOVED #248](https://github.com/StackExchange/StackExchange.Redis/issues/248). Решение этой проблемы разрабатывается. Все обновления будут публиковаться здесь. **Обновление**: эта проблема устранена в последней сборкеStackExchange.Redis, [prerelease 1.1.572-alpha](https://www.nuget.org/packages/StackExchange.Redis/1.1.572-alpha). См. последнюю сборку на [странице StackExchange.Redis портала NuGet](https://www.nuget.org/packages/StackExchange.Redis/).


	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
        // Connect to the Redis cache for the first time
	    var connection =  ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

		// Wait for 1 second
		Thread.Sleep(1000);

		// Return the connected ConnectionMultiplexer
		return connection;
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

<a name="cluster-size"></a>
## Изменение размера кластера на работающем кэше категории "Премиум"

Чтобы изменить размер кластера для работающего кэша категории "Премиум" с включенной кластеризацией, щелкните **Размер кластера Redis (предварительная версия)** в колонке **Параметры**.

>[AZURE.NOTE] Обратите внимание, что хотя кэш Redis для Azure категории «Премиум» выпущен для общего доступа, сейчас функция изменения размера кластера Redis находится на этапе предварительной версии.

![Размер кластера Redis][redis-cache-redis-cluster-size]

Чтобы изменить размер кластера, воспользуйтесь ползунком или введите число от 1 до 10 в текстовом поле **Количество сегментов**, затем нажмите кнопку **ОК** для сохранения изменений.

## Часто задаваемые вопросы по кластеризации

В следующем списке приведены ответы на часто задаваемые вопросы о кластеризации кэша Redis для Azure.

-	[Нужно ли вносить изменения в клиентское приложение, чтобы использовать кластеризацию?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
-	[Распределение ключей в кластере](#how-are-keys-distributed-in-a-cluster)
-	[Каков максимальный размер кэша, который можно создать?](#what-is-the-largest-cache-size-i-can-create)
-	[Все ли клиенты Redis поддерживают кластеризацию?](#do-all-redis-clients-support-clustering)
-	[Как подключиться к кэшу, если кластеризация включена?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
-	[Можно ли напрямую подключаться к отдельным сегментам кэша?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
-	[Можно ли настроить кластеризацию для ранее созданного кэша?](#can-i-configure-clustering-for-a-previously-created-cache)
-	[Можно ли настроить кластеризацию для кэша уровней Базовый и Стандартный?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
-	[Можно ли использовать кластеризацию с поставщиками состояний сеансов и кэширования выходных данных ASP.NET Redis?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### Нужно ли вносить изменения в клиентское приложение, чтобы использовать кластеризацию?

-	Если кластеризация включена, доступна только база данных 0. Если клиентское приложение использует несколько баз данных и пытается выполнить чтение или запись в базе данных, отличной от 0, порождается следующее исключение: `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`

    Дополнительные сведения см. в [разделе спецификаций кластера Redis, посвященном реализованному подмножеству](http://redis.io/topics/cluster-spec#implemented-subset).

-	При использовании клиента [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) необходимо установить версию 1.0.481 или более позднюю версию. Вы можете подключаться к кэшу с помощью тех же [конечных точек, портов и ключей](cache-configure.md#properties), которые используются для подключения к кэшу с отключенной кластеризацией. Единственное отличие заключается в том, что все операции чтения и записи должны выполняться в базе данных 0.
	-	Требования других клиентов могут отличаться. См. раздел [Все ли клиенты Redis поддерживают кластеризацию?](#do-all-redis-clients-support-clustering)
-	Если приложение использует несколько операций с ключом, объединенных в одну команду, все ключи должны быть расположены в одном сегменте. Выполнение этих действий описано в разделе [Распределение ключей в кластере](#how-are-keys-distributed-in-a-cluster).
-	Если вы используете поставщик состояний сеансов ASP.NET Redis, вам необходимо установить версию 2.0.1 или более позднюю версию. См. раздел [Можно ли использовать кластеризацию с поставщиками состояний сеансов и кэширования выходных данных ASP.NET Redis?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers).

### Распределение ключей в кластере

В соответствии с документацией по [модели распределения ключей](http://redis.io/topics/cluster-spec#keys-distribution-model) в Redis пространство ключей разбивается на 16 384 слота. Каждый ключ хэшируется и присваивается одному из этих слотов, распределенных между узлами кластера. С помощью хэш-тегов вы можете указать хэшируемую часть ключа, чтобы убедиться в том, что несколько ключей находятся в одном сегменте.

-	Ключи с хэш-тегом. Если любая часть ключа заключена в фигурные скобки — `{` и `}`, — для определения хэш-слота ключа хэшируется только эта часть. Например, три ключа — `{key}1`, `{key}2` и `{key}3` — будут расположены в одном сегменте, так как хэшируется только часть имени `key`. Полный список спецификаций для хэш-тегов ключей см. в разделе [Хэш-теги ключей](http://redis.io/topics/cluster-spec#keys-hash-tags).
-	Ключи без хэш-тега. Для хэширования используется полное имя ключа. Это позволяет достичь статистически равномерного распределения по сегментам кэша.

Для оптимальной производительности и пропускной способности рекомендуется равномерно распределять ключи. Если вы используете ключи с хэш-тегом, приложение должно обеспечивать равномерное распределение ключей.

Дополнительные сведения см. в разделах [Модель распределения ключей](http://redis.io/topics/cluster-spec#keys-distribution-model), [Сегментирование данных в кластере Redis](http://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding) и [Хэш-теги ключей](http://redis.io/topics/cluster-spec#keys-hash-tags).

Пример кода по работе с кластеризацией и поиска ключей в одном сегменте для клиента StackExchange.Redis см. в разделе [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) примера [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

### Каков максимальный размер кэша, который можно создать?

Максимальный размер кэша для уровня Премиум — 53 ГБ. Можно создать до 10 сегментов, таким образом общий максимальный размер составит 530 ГБ. Если требуется больший размер, вы можете [отправить запрос на получение дополнительного места](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Подробнее см. в разделе [Цены на кэш Redis для Azure](https://azure.microsoft.com/pricing/details/cache/).

### Все ли клиенты Redis поддерживают кластеризацию?

В настоящее время не все клиенты Redis поддерживают кластеризацию. Например, ее не поддерживает StackExchange.Redis. Дополнительные сведения о других клиентах см. в разделе [Эксперименты с кластером](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) [руководства по кластерам Redis](http://redis.io/topics/cluster-tutorial).

>[AZURE.NOTE] Если вы используете клиент [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/), убедитесь, что у вас установлена версия 1.0.481 или более поздняя. Это необходимо для правильного выполнения кластеризации. При проблемах с исключениями переноса см. [здесь](#move-exceptions).

### Как подключиться к кэшу, если кластеризация включена?

Вы можете подключаться к кэшу с помощью тех же [конечных точек, портов и ключей](cache-configure.md#properties), которые используются для подключения к кэшу с отключенной кластеризацией. Redis управляет кластеризацией на сервере, поэтому управлять ей из клиента не нужно.

### Можно ли напрямую подключаться к отдельным сегментам кэша?

Официально это не поддерживается. Тем не менее, каждый сегмент состоит основного кэша и реплики кэша, которые в совокупности называются экземпляром кэша. Вы можете подключиться к этим экземплярам кэша с помощью служебной программы redis-cli из ветви [нестабильных версий](http://redis.io/download) репозитория Redis на портале GitHub. Эта версия реализует базовую поддержку при запуске с параметром `-c`. Дополнительные сведения см. в разделе [Эксперименты с кластером](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) [учебника по кластерам Redis](http://redis.io/topics/cluster-tutorial) на сайте [http://redis.io](http://redis.io).

Для non-ssl используйте следующие команды.

	Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
	Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
	Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
	...
	Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Для SSL замените `1300N` на `1500N`.

### Можно ли настроить кластеризацию для ранее созданного кэша?

Сейчас вы можете включить кластеризацию только при создании кэша. Размер кластера вы можете изменить после создания кэша, но нельзя добавить кластеризацию в кэш категории "Премиум" или удалить кластеризацию из него после создания кэша. Кэш категории «Премиум» с включенной кластеризацией и только одним сегментом отличается от кэша категории «Премиум» такого же размера, но без кластеризации.

### Можно ли настроить кластеризацию для кэша уровней Базовый и Стандартный?

Кластеризация доступна только для кэша уровня Премиум.

### Можно ли использовать кластеризацию с поставщиками состояний сеансов и кэширования выходных данных ASP.NET Redis?

-	**Поставщик кэша вывода Redis** — изменения не требуются.
-	**Поставщик состояний сеансов Redis** — для кластеризации необходимо использовать [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 или более поздней версии. В противном случае будет выдано исключение. Это критическое изменение. Дополнительные сведения см. в статье [Подробные сведения о критических изменениях в версии 2.0.0](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

## Дальнейшие действия
Узнайте, как использовать расширенные функции кэша.

-	[Настройка сохраняемости для кэша Redis для Azure уровня Премиум](cache-how-to-premium-persistence.md)
-	[Настройка поддержки виртуальной сети для кэша Redis для Azure уровня Премиум](cache-how-to-premium-vnet.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-tier.png

[NewCacheMenu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-how-to-premium-clustering/redis-cache-cache-create.png

[redis-cache-premium-pricing-group]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-group.png

[redis-cache-premium-features]: ./media/cache-how-to-premium-clustering/redis-cache-premium-features.png

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png

<!---HONumber=AcomDC_0525_2016-->