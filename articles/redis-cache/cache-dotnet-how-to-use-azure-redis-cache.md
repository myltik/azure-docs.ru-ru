<properties 
	pageTitle="Как использовать кэш Azure Redis" 
	description="Узнайте, как повысить производительность приложений Azure с помощью кэша Redis для Azure." 
	services="redis-cache,app-service" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="11/10/2015" 
	ms.author="sdanie"/>

# Как использовать кэш Azure Redis

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

В этом руководстве объясняется, как приступить к использованию **кэша Redis для Azure**. Кэш Redis для Microsoft Azure основывается на популярном продукте с открытым кодом — кэше Redis. Он дает доступ к защищенному выделенному кэшу Redis, управляемому Майкрософт. Кэш, созданный с использованием кэша Azure Redis, доступен из любого приложения в Microsoft Azure.

Доступны указанные ниже уровни кэша Redis для Microsoft Azure.

-	**Basic** — один узел. Множество размеров вплоть до 53 ГБ.
-	**Standard** — два узла: основной и реплика. Множество размеров вплоть до 53 ГБ. СОГЛАШЕНИЕ ОБ УРОВНЕ ОБСЛУЖИВАНИЯ 99,9 %.
-	**Премиум** — сейчас доступна предварительная версия. Два узла (основной и реплика), включающие до 10 сегментов. Несколько размеров от 6 до 530 ГБ (если вам необходим больший размер, свяжитесь с нами). Все функции категории «Стандартный», а также дополнительные функции, включая поддержку [кластера Redis](cache-how-to-premium-clustering.md), [сохраняемости Redis](cache-how-to-premium-persistence.md) и [виртуальной сети Azure](cache-how-to-premium-vnet.md). В течение периода предварительной версии соглашение об уровне обслуживания не используется.

Каждый уровень отличается доступными возможностями и ценой. Дополнительные сведения о ценах см. на странице [Кэш Redis. Цены][].

В этом руководстве показано, как использовать клиент [StackExchange.Redis][] с помощью кода C#. Рассматриваемые сценарии включают **создание и настройку кэша**, **настройку клиентов кэша**, а также **добавление объектов в кэш и их удаление**. Дополнительные сведения об использовании кэша Redis для Azure см. в разделе [Дальнейшие действия][].

<a name="getting-started-cache-service"></a>
## Начало работы с кэшем Redis для Azure

Начать работу с кэшем Azure Redis просто. Чтобы приступить к работе, подготовьте и настройте кэш. Затем следует настроить клиенты кэша, чтобы они могли обращаться к кэшу. Когда клиенты кэша настроены, с ними можно работать.

-	[Создание кэша][]
-	[Настройка клиентов кэша][]

<a name="create-cache"></a>
## Создание кэша

Чтобы создать кэш, войдите на [портал предварительной версии Azure][] и последовательно щелкните **Создать**, **Данные+хранилище** и **Кэш Redis**.

![Новый кэш][NewCacheMenu]

>[AZURE.NOTE]Если у вас нет учетной записи Azure, вы можете создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure][].

В колонке **Новый кэш Redis** выберите конфигурацию кэша.

![Создание кэша][CacheCreate]

-	В поле **Имя DNS** введите имя поддомена для использования конечной точкой кэша. Конечная точка должна быть строкой длиной от шести до двадцати символов, содержать только строчные буквы и цифры, а также должна начинаться с буквы.
-	В поле **Подписка** выберите подписку Azure, которую вы хотите использовать для кэша. Если у вашей учетной записи есть только одна подписка, она будет выбрана автоматически и раскрывающееся меню **Подписка** не появится.
-	В поле **Группа ресурсов** выберите или создайте группу ресурсов для кэша. За дополнительной информацией обратитесь к разделу [Использование групп ресурсов для управления ресурсами Azure][]. 
-	В поле поля **Расположение** укажите, где географически будет располагаться ваш кэш. Для оптимальной производительности Майкрософт настоятельно рекомендует размещать кэш в том же регионе, что и клиентское приложение кэша.
-	Используйте **Уровень цен** для выбора желаемого размера и компонентов кэша.
-	**Кластер Redis** позволяет создавать кэши размером более 53 ГБ и сегментировать данные по нескольким узлам Redis. Дополнительные сведения см. в статье [Настройка кластеризации для кэша Redis для Azure уровня Премиум](cache-how-to-premium-clustering.md).
-	**Функция сохраняемости Redis** дает возможность сохранять кэш в учетную запись хранения Azure. Инструкции по настройке сохраняемости см. в разделе [Настройка постоянного хранения для кэша Redis для Azure уровня Премиум](cache-how-to-premium-persistence.md).
-	**Виртуальная сеть** обеспечивает улучшенную защиту и изоляцию, предоставляя доступ к кэшу только тем клиентам, которые входят в указанную виртуальную сеть Azure. Вы можете использовать все возможности виртуальной сети, например подсети, политики контроля доступа и другие функции, для дальнейшего ограничения доступа к Redis. Дополнительные сведения см. в статье [Настройка поддержки виртуальной сети для кэша Redis для Azure уровня Премиум](cache-how-to-premium-vnet.md).
-	Чтобы указать учетную запись хранения для метрик кэша, щелкните **Диагностика**. Дополнительные сведения о настройке и просмотре метрик кэша см. в статье [Как отслеживать кэш Redis для Azure](cache-how-to-monitor.md).

После задания настроек нового кэша щелкните **Создать**. Создание кэша может занять несколько минут. Для проверки состояния можно отслеживать прогресс на начальной панели. После создания новый кэш имеет состояние **Работает** и готов для использования со значениями по умолчанию.

![Кэш создан][CacheCreated]

После создания кэша он доступен в лезвии **Обзор**.

![Лезвие "Обзор"][BrowseCaches]

Щелкните **Кэши Redis**, чтобы просмотреть свои экземпляры кэша.

![Кэши][Caches]

<a name="NuGet"></a>
## Настройка клиентов кэша

Экземпляр кэша на базе кэша Redis для Azure доступен из любого приложения Azure. Приложения .NET, разработанные в Visual Studio, могут использовать клиент **StackExchange.Redis**, который конфигурируется с помощью пакета NuGet, упрощающего настройку клиентских приложений кэша.

>[AZURE.NOTE]Дополнительные сведения см. на странице [StackExchange.Redis][] на github и в [документации по клиенту кэша StackExchange.Redis][].

Чтобы настроить клиентское приложение в Visual Studio, используя пакет StackExchange.Redis из NuGet, щелкните правой кнопкой мыши **обозреватель решений** и выберите **Управление пакетами NuGet**.

![Управление пакетами NuGet][NuGetMenu]

Введите **StackExchange.Redis** или **StackExchange.Redis.StrongName** в текстовом поле **Поиск в сети**, выберите желаемую версию из результатов и щелкните **Установить**.

>[AZURE.NOTE]При желании использовать версию клиентской библиотеки **StackExchange.Redis** со строгими именами выберите **StackExchange.Redis.StrongName**, в противном случае выберите **StackExchange.Redis**.

![Пакет NuGet StackExchange.Redis][StackExchangeNuget]

Пакет NuGet загружает и добавляет необходимые ссылки на сборки в клиентском приложении для доступа к кэшу Azure Redis из клиента кэша StackExchange.Redis.

После настройки проекта клиента для кэширования можно использовать методы, описанные в следующих разделах, для работы с кэшем.

<a name="working-with-caches"></a>
## Работа с кэшами

Действия, приведенные в этом разделе, описывают способы выполнения типовых задач при работе с кэшем.

-	[Подключение к кэшу][]
-   [Добавление в кэш объектов и их извлечение][]
-   [Работа с объектами .NET в кэше](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>
## Подключение к кэшу

Чтобы программно работать с кэшем, требуется ссылка на кэш. Добавьте следующий код вверху любого файла, из которого будет использоваться клиент StackExchange.Redis для доступа к кэшу Redis для Azure.

    using StackExchange.Redis;

>[AZURE.NOTE]Клиенту StackExchange.Redis необходима среда .NET Framework 4 или выше.

Подключение к кэшу Redis для Azure выполняется с помощью класса `ConnectionMultiplexer`. Этот класс предназначен для общего использования и повторного использования клиентским приложением и не нуждается в создании нового экземпляра для каждой отдельной операции.

Для подключения к кэшу Redis для Azure и получения экземпляра подключенного класса `ConnectionMultiplexer` вызовите статический метод `Connect` и передайте ему конечную точку кэша и ключ, как показано в примере ниже. Используйте в качестве параметра пароля ключ Azure, созданный на портале предварительной версии.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

>[AZURE.IMPORTANT]Внимание! Никогда не храните учетные данные в исходном коде. Для сохранения простоты примера они показаны в исходном коде. Сведения о том, как хранить учетные данные, см. в статье [Принципы работы строк приложений и подключения][].

Если вам не требуется SSL, задайте значение `ssl=false` или пропустите параметр `ssl`.

>[AZURE.NOTE]Для новых кэшей не SSL порт по умолчанию запрещен. Инструкции по включению портов, не использующих протокол SSL, см. в разделе [Порты доступа](cache-configure.md#access-ports).

Один из способов совместного использования экземпляра `ConnectionMultiplexer` в приложении таков: нужно иметь статическое свойство, которое возвращает подключенный экземпляр (как в приведенном ниже примере). Это потокобезопасный способ инициализации только одного подключенного экземпляра `ConnectionMultiplexer`. В этих примерах для параметра `abortConnect` задано значение False. Это означает, что вызов завершается успешно, даже если подключение к кэшу Redis для Azure не установлено. Одна из ключевых особенностей `ConnectionMultiplexer` заключается в том, что этот параметр автоматически восстанавливает подключение к кэшу, когда устраняется проблема с сетью или другие проблемы.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

За дополнительной информацией по вариантам расширенных настроек соединения обратитесь к раз делу [Модель конфигурации StackExchange.Redis][].

Конечная точка и ключи указаны в колонке **Кэш Redis** вашего экземпляра кэша.

![Свойства кэша][CacheProperties]

![Управление ключами][ManageKeys]

Создав подключение, верните ссылку на базу данных кэша Redis с помощью метода `ConnectionMultiplexer.GetDatabase`. Объект, возвращенный из метода `GetDatabase`, – это упрощенный передаваемый объект, не требующий сохранения.

	// Connection refers to a property that returns a ConnectionMultiplexer
	// as shown in the previous example.
	IDatabase cache = Connection.GetDatabase();

	// Perform cache operations using the cache object...
	// Simple put of integral data types into the cache
	cache.StringSet("key1", "value");
	cache.StringSet("key2", 25);

	// Simple get of data types from the cache
	string key1 = cache.StringGet("key1");
	int key2 = (int)cache.StringGet("key2");

Теперь нам известно, как соединяться с экземпляром кэша Azure Redis и возвращать ссылку на базу данных кэша, поэтому давайте посмотрим, как работать с кэшем.

<a name="add-object"></a>
## Добавление и извлечение объектов из кэша

Элементы можно хранить в кэше и извлекать из него с помощью методов `StringSet` и `StringGet`.

	// If key1 exists, it is overwritten.
	cache.StringSet("key1", "value1");

	string value = cache.StringGet("key1");

Redis хранит большинство данных в строках Redis, но эти строки могут содержать данные многих типов, включая сериализованные двоичные данные, которые можно использовать при сохранении объектов .NET в кэше.

Если объект существует, вызов метода `StringGet` возвращает его. В противном случае возвращается значение `null`. В этом случае можно извлечь значение из желаемого источника данных и сохранить его в кэше для последующего использования. Это называется шаблоном "кэш на стороне".

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

Чтобы указать срок действия объекта в кэше, используйте параметр `TimeSpan` метода `StringSet`.

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## Работа с объектами .NET в кэше

Кэш Azure Redis может кэшировать объекты .NET, а также несложные типы данных, но прежде чем объект .NET можно будет сохранить, он должен быть сериализован. Это ответственность разработчика приложения, что дает разработчику гибкость в выборе сериализатора.

Простой способ сериализации объектов — использовать методы сериализации `JsonConvert` в [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) и выполнять сериализацию в нотацию JSON и из нее. В следующем примере показано получение и задание экземпляра объекта `Employee`.


	class Employee
	{
	    public int Id { get; set; }
	    public string Name { get; set; }
	
	    public Employee(int EmployeeId, string Name)
	    {
	        this.Id = EmployeeId;
	        this.Name = Name;
	    }
	}

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>
## Дальнейшие действия

Теперь, когда вы ознакомились с основными сведениями, используйте следующие ссылки для получения дополнительных сведений о кэше Redis для Azure.

-	Ознакомьтесь с информацией о поставщиках ASP.NET для кэша Redis для Azure.
	-	[Поставщик состояний сеансов Redis для Azure](cache-asp.net-session-state-provider.md)
	-	[Поставщик кэша вывода ASP.NET для кэша Redis для Azure](cache-asp.net-output-cache-provider.md)
-	[Включите диагностику кэша](cache-how-to-monitor.md#enable-cache-diagnostics), чтобы можно было [наблюдать](cache-how-to-monitor.md) за работоспособностью кэша. Вы можете просматривать метрики на портале предварительной версии, а также [скачивать и изучать](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) их с помощью удобных для вас средств.
-	Ознакомьтесь с [документацией по клиенту кэша StackExchange.Redis][].
	-	Доступ к кэшу Redis для Azure можно получить из многих клиентов Redis и языков разработки. Дополнительные сведения см. в статьях [http://redis.io/clients][] и [Разработка на других языках для кэша Redis для Azure][].
	-	Кэш Redis для Azure может также использоваться со службами, такими как Redsmin. Дополнительные сведения см. в статье [Получение строки подключения Redis для Azure и ее использование с Redsmin][].
-	См. документацию по [redis][]\: прочитайте [статью о типах данных redis][] и [пятнадцатиминутное введение в типы данных Redis][].



<!-- INTRA-TOPIC LINKS -->
[Дальнейшие действия]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Создание кэша]: #create-cache
[Configure the cache]: #enable-caching
[Настройка клиентов кэша]: #NuGet
[Working with Caches]: #working-with-caches
[Подключение к кэшу]: #connect-to-cache
[Добавление в кэш объектов и их извлечение]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session

  
<!-- IMAGES -->
[NewCacheMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-create.png

[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png

[CacheCreated]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-created.png




   
<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[Разработка на других языках для кэша Redis для Azure]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[Получение строки подключения Redis для Azure и ее использование с Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configure a cache in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[Модель конфигурации StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Work with .NET objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Кэш Redis. Цены]: http://www.windowsazure.com/pricing/details/cache/
[портал предварительной версии Azure]: https://portal.azure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Использование групп ресурсов для управления ресурсами Azure]: http://azure.microsoft.com/documentation/articles/resource-group-overview/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[документацией по клиенту кэша StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation
[документации по клиенту кэша StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[статью о типах данных redis]: http://redis.io/topics/data-types
[пятнадцатиминутное введение в типы данных Redis]: http://redis.io/topics/data-types-intro

[Принципы работы строк приложений и подключения]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/

[Бесплатная пробная версия Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero

<!---HONumber=Nov15_HO3-->