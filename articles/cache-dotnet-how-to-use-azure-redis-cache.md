<properties pageTitle="Как использовать кэш Azure Redis" metaKeywords="" description="Узнайте, как создать и использовать кэш в кэше Redis для Azure" metaCanonical="" services="cache" documentationCenter="API Management" title="How to Use Azure Redis Cache" authors="sdanie" solutions="" manager="dwrede" editor="" />

<tags ms.service="cache" ms.workload="tbd" ms.tgt_pltfrm="cache-redis" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="sdanie" />

# Как использовать кэш Azure Redis

В этом руководстве показано, как начать использовать 
**Кэш Redis для Azure**. Примеры написаны на языке C\# с
использованием API .NET. Рассматриваемые сценарии включают **создание и настройку кэша**, **настройку клиентов кэша**, **добавление и удаление объектов из кэша** и **сохранение состояния сеанса ASP.NET в кэше**. Для получения дополнительной информации
по использованию кэша Redis для Azure обратитесь к разделу [Дальнейшие действия][].

## Оглавление

-   [Что такое кэш Azure Redis][]
-	[Начало работы с кэшем Azure Redis][]
	-	[Создание кэша][]
	-	[Настройка клиентов кэша][]
-	[Работа с кэшами][]
	-	[Подключение к кэшу][]
	-   [Добавление и извлечение объектов из кэша][]
	-   [Указание срока действия объекта в кэше][]
	-   [Сохранение состояния сеанса ASP.NET в кэше][]
-   [Дальнейшие действия][]

<a name="what-is"></a>
## Что такое кэш Azure Redis

Кэш Redis для Microsoft Azure основывается на популярном продукте с открытым кодом - кэше Redis. Он дает доступ к защищенному выделенному кэшу Redis, управляемому Майкрософт. Кэш, созданный с использованием кэша Redis для Azure, доступен из любого приложения в Microsoft Azure.

Кэш Microsoft Azure Redis доступен в двух уровнях:

-	**Basic** - один узел. Множество размеров вплоть до 53 ГБ.
-	**Standard** - двухузловой "ведущий-ведомый". Множество размеров вплоть до 53 ГБ. СОГЛАШЕНИЕ ОБ УРОВНЕ ОБСЛУЖИВАНИЯ 99,9 %.

Каждый уровень отличается доступными возможностями и ценой. Конкретные возможности рассматриваются ниже в этом руководстве, а дополнительную информацию о ценах см. в разделе [Сведения о ценах - кэш][].

Это руководство содержит обзор начала работы с кэшем Redis для Azure. За дополнительной детальной информацией о компонентах, которые не вошли в это руководство по началу работы, обратитесь к разделу [Обзор кэша Redis для Azure][].

<a name="getting-started-cache-service"></a>
## Начало работы с кэшем Azure Redis

Приступить к работе с кэшем Redis для Azure просто. Чтобы приступить к работе, подготовьте и настройте кэш. Затем следует настроить клиенты кэша, чтобы они могли обращаться к кэшу. Когда клиенты кэша настроены, с ними можно работать.

-	[Создание кэша][]
-	[Настройка кэша][]
-	[Настройка клиентов кэша][]

<a name="create-cache"></a>
## Создание кэша

Для создания кэша войдите на портал управления предварительной версией Azure и выберите пункты **Создать**, **Кэш Redis**.

![New cache][NewCacheMenu]

В колонке **Новый кэш Redis** укажите желаемую конфигурацию кэша.

![Create cache][CacheCreate]

В поле **Имя DNS** введите имя поддомена для использования конечной точкой кэша. Конечная точка должна быть строкой длиной от шести до двадцати символов, содержать только строчные буквы и цифры, а также должна начинаться с буквы.

Используйте **Уровень цен**, чтобы выбрать желаемый размер и компоненты кэша. Кэш Redis доступен для следующих двух уровней.

-	**Basic** - один узел, множество размеров вплоть до 53 ГБ.
-	**Standard** - два узла, "ведущий-ведомый", SLA 99,9 %, множество размеров вплоть до 53 ГБ.

В поле **Подписка** выберите подписку Azure, которую вы хотите использовать для кэша.

>[AZURE.NOTE] Если у вашей учетной записи есть только одна подписка, она будет выбрана автоматически и раскрывающееся меню "Подписка" не появится.

В поле **Группа ресурсов** выберите или создайте группу ресурсов для кэша.

>[AZURE.NOTE] Дополнительную информацию см. в статье [Использование групп ресурсов для управления ресурсами Azure][]. 

Используйте **Географическое положение**, чтобы указать географическое расположение, где будет расположен кэш. Для оптимальной производительности корпорация Майкрософт настоятельно рекомендует размещать кэш в том же регионе, что и клиентское приложение кэша.

После задания настроек нового кэша щелкните **Создать**. Создание кэша может занять несколько минут. Для проверки состояния можно отслеживать прогресс на начальной панели. После создания новый кэш имеет состояние **Работает** и готов для использования со значениями по умолчанию.

![Cache created][CacheCreated]

После создания кэша он доступен в колонке **Обзор**.

![Browse blade][BrowseCaches]

Щелкните пункт **Кэши**, чтобы просмотреть кэши.

![Caches][Caches]

<a name="NuGet"></a>
## Настройка клиентов кэша

Кэш, созданный с использованием кэша Redis для Azure, доступен из любого приложения Azure. Приложения .NET, разработанные в Visual Studio, могут использовать клиент кэша **StackExchange.Redis**, который можно настроить, используя пакет NuGet, упрощающий настройку клиентских приложений кэша. 

>[AZURE.NOTE] За дополнительной информацией обратитесь к странице [StackExchange.Redis][] на GitHub и [документации по клиенту кэша StackExchange.Redis][].

Чтобы настроить клиентское приложение в Visual Studio, используя пакет NuGet StackExchange.Redis, щелкните правой кнопкой мыши **обозреватель решений** и выберите **Управление пакетами NuGet**. 

![Manage NuGet packages][NuGetMenu]

Введите **StackExchange.Redis** или **StackExchange.Redis.StrongName** в текстовом поле **Поиск в сети**, выберите желаемую версию из результатов и щелкните **Установить**.

>[AZURE.NOTE] Если вы предпочитаете использовать версию клиентской библиотеки **StackExchange.Redis** со строгими именами, выберите **StackExchange.Redis.StrongName**. В противном случае выберите **StackExchange.Redis**.

![StackExchange.Redis NuGet package][StackExchangeNuget]

Пакет NuGet загружает и добавляет необходимые ссылки на сборки в клиентском приложении для доступа к кэшу Azure Redis из клиента кэша StackExchange.Redis.

После настройки проекта клиента для кэширования можно использовать методы, описанные в следующих разделах, для работы с кэшем.

<a name="working-with-caches"></a>
## Работа с кэшами

Действия, приведенные в этом разделе, описывают способы выполнения типовых задач при работе с кэшем.

-	[Подключение к кэшу][]
-   [Добавление и извлечение объектов из кэша][]
-   [Сохранение состояния сеанса ASP.NET в кэше][]

<a name="connect-to-cache"></a>
## Подключение к кэшу

Чтобы программно работать с кэшем, требуется ссылка на кэш. Добавьте следующий код вверху любого файла, из которого клиент StackExchange.Redis будет использоваться для доступа к кэшу Redis для Azure.

    using StackExchange.Redis;

>[AZURE.NOTE] Клиенту StackExchange.Redis необходима среда .NET Framework 4 или выше.

Подключение к кэшу Redis для Azure управляется классом `ConnectionMultiplexer`. Этот класс предназначен для общего использования и повторного использования клиентским приложением и не нуждается в создании нового экземпляра для каждой отдельной операции. 

Для подключения к кэшу Redis для Azure и возврата экземпляра подключенного `ConnectionMultiplexer` вызовите статический метод `Connect` и передайте конечную точку кэша и ключ так, как показано в следующем примере. Используйте ключ Azure, созданный на портале службы, как параметр пароля.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

>[AZURE.NOTE] Предупреждение. Никогда не сохраняйте учетные данные в исходном коде. Для сохранения простоты примера они показаны в исходном коде. См. раздел [Веб-сайты Windows Azure: как работают строки приложений и строки подключения][], чтобы получить информацию о том, как хранить учетные данные.

Если вы не хотите использовать SSL, установите значение `ssl=false` или просто передайте конечную точку и ключ.

	connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,password=...");

>[AZURE.NOTE] За дополнительной информацией о вариантах расширенных настроек соединения обратитесь к разделу [Модель конфигурации StackExchange.Redis][].

Конечную точку и ключи кэша можно получить на портале управления Azure предварительной версии в лезвии для экземпляра кэша.

![Cache properties][CacheProperties]

![Manage keys][ManageKeys]

После установки соединения верните ссылку на базу данных кэша Redis, вызвав метод `ConnectionMultiplexer.GetDatabase`.

	// connection referes to a previously configured ConnectionMultiplexer
	IDatabase cache = connection.GetDatabase();

>[AZURE.NOTE] Объект, возвращенный из метода `GetDatabase` - это упрощенный передаваемый объект, не требующий сохранения.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

	IDatabase cache = connection.GetDatabase();

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

Элементы могут храниться в кэше и извлекаться из него с помощью методов `StringSet` и `StringGet`.

	// If key1 exists, it is overwritten.
	cache.StringSet("key1", "value1");

	string value = cache.StringGet("key1");

>[AZURE.NOTE] Redis хранит большинство данных в строках Redis, но эти строки могут содержать данные многих типов, включая сериализованные двоичные данные, которые можно использовать при сохранении объектов .NET в кэше.

Если при вызове `StringGet` объект существует, он возвращается, а если нет, возвращается значение NULL. В этом случае можно извлечь значение из желаемого источника данных и сохранить его в кэше для последующего использования. Это называется шаблоном программирования отдельно от кэша.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

>[AZURE.NOTE] Кэш Redis для Azure может кэшировать объекты .NET, а также простые типы данных, но прежде чем объект .NET можно будет сохранить, он должен быть сериализован. Это ответственность разработчика приложения, что дает разработчику гибкость в выборе сериализатора. За дополнительной информацией обратитесь к разделу [Работа с объектами .NET в кэше][].

<a name="specify-expiration"></a>
## Указание срока действия объекта в кэше

Чтобы задать срок действия элемента в кэше, используйте параметр `TimeSpan` в `StringSet`..

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));


<a name="store-session"></a>
## Сохранение состояния сеанса ASP.NET в кэше

Кэш Redis для Azure предоставляет поставщик состояний сеансов, который можно использовать для сохранения состояния сеансов в кэше, а не в памяти или базе данных SQL Server. Чтобы использовать поставщик состояний сеансов кэширования,
вначале настройте кэш, а затем настройте приложение ASP.NET для использования кэша, используя пакет NuGet для состояний сеансов кэша Redis.

Чтобы настроить клиентское приложение в Visual Studio с использованием пакета NuGet StackExchange.Redis, щелкните правой кнопкой мыши **обозреватель решений** и выберите **Управление пакетами NuGet**. 

![Manage NuGet packages][NuGetMenu]

Введите **RedisSessionStateProvider** в текстовом поле **Поиск в сети**, выберите этот поставщик из результатов и щелкните **Установить**.

![Redis Cache Session State NuGet Package][SessionStateNuGet]

Пакет NuGet загружает и добавляет требуемые ссылки на сборки и добавляет в файл web.config следующий раздел, который содержит необходимые в приложении ASP.NET настройки для использования кэширующего поставщика состояний сеансов Redis.

  <sessionState mode="Custom" customProvider="MySessionStateStore">
      <providers>
        <!--
          <add name="MySessionStateStore" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />
      </providers>
    </sessionState>



Раздел с комментариями содержит пример атрибутов и возможные их настройки.

Задайте атрибутам значения из лезвия кэша на портале предварительной версии и укажите другие желаемые величины.

	<sessionState mode="Custom" customProvider="MySessionStateStore">
      <providers>
        <!--
          <add name="MySessionStateStore" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="contoso5.redis.cache.windows.net" 
		accessKey="..." ssl="true" />
      </providers>
    </sessionState>

Обязательно закомментируйте стандартный поставщик состояний сеансов **InProc**.

    <!-- <sessionState mode="InProc" customProvider="DefaultSessionProvider">
      <providers>
        <add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
      </providers>
    </sessionState> -->

За дополнительной информацией о настройках и использовании поставщика состояний сеансов Redis для Azure обратитесь к разделу [Поставщик состояний сеансов Redis для Azure][].

<a name="next-steps"></a>
## Дальнейшие действия

Теперь, когда вы познакомились с основами кэша Redis для Azure,
используйте следующие ссылки на дополнительную информацию, чтобы перейти к более сложным задачам кэширования.

-	Узнайте больше о клиенте StackExchange.Redis: [документация по клиенту кэша StackExchange.Redis][]
-	См. документацию по [Redis][], прочитайте о [типах данных Redis][] и ознакомьтесь с [пятнадцатиминутным введением в типы данных Redis][].
-   Справочник MSDN: [кэш Azure Redis][]


<!-- INTRA-TOPIC LINKS -->
[Дальнейшие действия]: #next-steps
[Что такое кэш Azure Redis]: #what-is
[Создание кэша Azure]: #create-cache
[Какой тип кэширования мне выбрать?]: #choosing-cache
[Подготовка проекта Visual Studio для использования Azure Caching]: #prepare-vs
[Настройка приложения для использования кэширования]: #configure-app
[Начало работы с кэшем Azure Redis]: #getting-started-cache-service
[Создание кэша]: #create-cache
[Настройка кэша]: #enable-caching
[Настройка клиентов кэша]: #NuGet
[Работа с кэшами]: #working-with-caches
[Подключение к кэшу]: #connect-to-cache
[Добавление и извлечение объектов из кэша]: #add-object
[Указание срока действия объекта в кэше]: #specify-expiration
[Сохранение состояния сеанса ASP.NET в кэше]: #store-session

  
<!-- IMAGES -->
[NewCacheMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-create.png

[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Кэши]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png

[CacheCreated]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-created.png




   
<!-- LINKS -->
[Поставщик состояний сеансов Redis для Azure]: http://go.microsoft.com/fwlink/?LinkId=398249
[Портал управления Azure]: http://windows.azure.com/
[Практическое руководство. Программная настройка клиента кэша]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg618003.aspx
[Поставщик состояний сеансов для кэша Azure]: http://go.microsoft.com/fwlink/?LinkId=320835
[Кэш Azure AppFabric: кэширование состояний сеансов]: http://www.microsoft.com/ru-ru/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Поставщик кэша вывода для кэша Azure]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg278356.aspx
[Блог группы разработчиков]: http://blogs.msdn.com/b/windowsazure/
[Кэширование Azure]: http://www.microsoft.com/ru-ru/showcase/Search.aspx?phrase=azure+caching
[Настройка размера виртуальных машин]: http://go.microsoft.com/fwlink/?LinkId=164387
[Рекомендации по планированию загрузки Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=320167
[Кэширование Azure]: http://go.microsoft.com/fwlink/?LinkId=252658
[Практическое руководство. Декларативное задание возможности кэширования страницы ASP.NET]: http://msdn.microsoft.com/ru-ru/library/zd1ysf1y.aspx
[Практическое руководство. Программное задание возможности кэширования страницы]: http://msdn.microsoft.com/ru-ru/library/z852zf6b.aspx

[Модель конфигурации StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Работа с объектами .NET в кэше]: http://msdn.microsoft.com/ru-ru/library/dn690521.aspx#Objects


[Установка диспетчера пакета NuGet]: http://go.microsoft.com/fwlink/?LinkId=240311
[Сведения о ценах - кэш]: http://www.windowsazure.com/ru-ru/pricing/details/cache/
[Портал управления]: https://manage.windowsazure.com/

[Общие сведения о кэше Redis для Azure]: http://go.microsoft.com/fwlink/?LinkId=320830
[кэш Azure Redis]: http://go.microsoft.com/fwlink/?LinkId=398247

[Переход на кэш Redis для Azure]: http://go.microsoft.com/fwlink/?LinkId=317347
[Примеры кэша Redis для Azure]: http://go.microsoft.com/fwlink/?LinkId=320840
[Использование групп ресурсов для управления ресурсами Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/azure-preview-portal-using-resource-groups/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[документация по клиенту кэша StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Типы данных Redis]: http://redis.io/topics/data-types
[пятнадцатиминутное введение в типы данных Redis]: http://redis.io/topics/data-types-intro

[Веб-сайты Microsoft Azure: как работают строки приложения и строки подключения]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
