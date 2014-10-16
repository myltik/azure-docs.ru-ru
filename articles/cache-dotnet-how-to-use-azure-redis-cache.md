<properties pageTitle="How to Use Azure Redis Cache" metaKeywords="" description="Learn how to create a use a cache in Azure Redis Cache" metaCanonical="" services="" documentationCenter="API Management" title="How to Use Azure Redis Cache" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="cache" ms.workload="tbd" ms.tgt_pltfrm="cache-redis" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Как использовать кэш Azure Redis

Это руководство демонстрирует, как начать работу с
**Кэш Azure Redis (предварительная версия)**. Примеры написаны на C# и используют
.NET API. Рассматриваемые сценарии включают **создание и настройку кэша**, **настройку клиентов кэша**, **добавление и удаление объектов из кэша** и **сохранение состояния сеанса ASP.NET в кэше**. За дополнительной
информацией по использованию кэша Azure Redis обратитесь к разделу [Дальнейшие действия][].

## Оглавление

-   [Что такое кэш Azure Redis][]
-   [Начало работы с кэшем Azure Redis][]

    -   [Создание кэша][]
    -   [Настройка клиентов кэша][]
-   [Работа с кэшами][]

    -   [Подключение к кэшу][]
    -   [Добавление и извлечение объектов из кэша][]
    -   [Указание срока действия объекта в кэше][]
    -   [Сохранение состояния сеанса ASP.NET в кэше][]
-   [Дальнейшие действия][]

<a name="what-is"></a>

## Что такое кэш Azure Redis

Кэш Microsoft Azure Redis, пока в предварительной версии, основывается на популярном продукте с открытым кодом — Redis Cache. Он дает доступ к защищенному выделенному кэшу Redis, управляемому Майкрософт. Кэш, созданный с использованием кэша Azure Redis, доступен из любого приложения в Microsoft Azure.

Кэш Microsoft Azure Redis доступен в двух уровнях:

-   **Basic** — один узел. Множество размеров вплоть до 26 ГБ.
-   **Standard** — двухузловой "ведущий-ведомый". Множество размеров вплоть до 26 ГБ.

Каждый уровень отличается доступными возможностями и ценой. Конкретные возможности рассматриваются ниже в этом руководстве, а дополнительные сведения о ценах см. в разделе [Сведения о ценах — кэш][].

Это руководство содержит обзор начала работы с кэшем Azure Redis. За дополнительной детальной информацией о компонентах, которые не вошли в это руководство, обратитесь к разделу [Обзор кэша Azure Redis][].

<a name="getting-started-cache-service"></a>

## Начало работы с кэшем Azure Redis

Начать работу с кэшем Azure Redis просто. Чтобы приступить к работе, подготовьте и настройте кэш. Затем следует настроить клиенты кэша, чтобы они могли обращаться к кэшу. Когда клиенты кэша настроены, с ними можно работать.

-   [Создание кэша][]
-   [Настройка кэша][]
-   [Настройка клиентов кэша][]

<a name="create-cache"></a>

## Создание кэша

Для создания кэша войдите на портал управления Azure предварительной версии и выберите пункты **Создать**, **Кэш Redis (предварительная версия)**.

![Новый кэш][]

В лезвии **Новый кэш Redis (предварительная версия)** укажите желаемую конфигурацию кэша.

![Создание кэша][1]

В поле **Имя DNS** введите имя поддомена для использования конечной точкой кэша. Конечная точка должна быть строкой длиной от шести до двадцати символов, содержать только строчные буквы и цифры, а также должна начинаться с буквы.

Используйте **Уровень цен** для выбора желаемого размера и компонентов кэша. Кэш Redis доступен в следующих двух уровнях.

-   **Basic** — один узел, множество размеров вплоть до 26 ГБ.
-   **Standard** — два узла, "ведущий-ведомый", 99,9% SLA (после предварительной версии), множество размеров вплоть до 26 ГБ.

В поле **Подписка** выберите подписку Azure, которую вы хотите использовать для кэша.

> Если у вашей учетной записи есть только одна подписка, она будет выбрана автоматически и раскрывающееся меню "Подписка" не появится.

В поле **Группа ресурсов** выберите или создайте группу ресурсов для кэша.

> За дополнительной информацией обратитесь к разделу [Использование групп ресурсов для управления ресурсами Azure][].

Используйте **Географическое положение** для указания географического расположения, где будет расположен кэш. Для оптимальной производительности Майкрософт настоятельно рекомендует размещать кэш в том же регионе, что и клиентское приложение кэша.

После задания настроек нового кэша щелкните **Создать**. Создание кэша может занять несколько минут. Для проверки состояния можно отслеживать прогресс на начальной панели. После создания новый кэш имеет состояние **Работает** и готов для использования со значениями по умолчанию.

![Кэш создан][]

После создания кэша он доступен в лезвии **Обзор**.

![Лезвие "Обзор"][]

Щелкните пункт **Кэши** для просмотра кэшей.

![Кэши][]

<a name="NuGet"></a>

## Настройка клиентов кэша

Кэш, созданный с использованием кэша Azure Redis, доступен из любого приложения Azure. Приложения .NET, разработанные в Visual Studio, могут использовать клиент кэша **StackExchange.Redis**, который можно настроить, используя пакет NuGet, упрощающий настройку клиентских приложений кэша.

> За дополнительной информацией обратитесь к странице [StackExchange.Redis][] на github и документации клиента кэша [StackExchange.Redis][2].

Чтобы настроить клиентское приложение в Visual Studio, используя пакет StackExchange.Redis из NuGet, щелкните правой кнопкой мыши **обозреватель решений** и выберите **Управление пакетами NuGet**.

![Управление пакетами NuGet][]

Введите **StackExchange.Redis** или **StackExchange.Redis.StrongName** в текстовом поле **Поиск в сети**, выберите желаемую версию из результатов и щелкните **Установить**.

> При желании использовать версию клиентской библиотеки **StackExchange.Redis** со строгими именами выберите **StackExchange.Redis.StrongName**, в противном случае выберите **StackExchange.Redis**.

![Пакет NuGet StackExchange.Redis][]

Пакет NuGet загружает и добавляет необходимые ссылки на сборки в клиентском приложении для доступа к кэшу Azure Redis из клиента кэша StackExchange.Redis.

После настройки проекта клиента для кэширования можно использовать методы, описанные в следующих разделах, для работы с кэшем.

<a name="working-with-caches"></a>

## Работа с кэшами

Действия, приведенные в этом разделе, описывают способы выполнения типовых задач при работе с кэшем.

-   [Подключение к кэшу][]
-   [Добавление и извлечение объектов из кэша][]
-   [Сохранение состояния сеанса ASP.NET в кэше][]

<a name="connect-to-cache"></a>

## Подключение к кэшу

Чтобы работать с кэшем программными средствами, требуется ссылка на кэш. Добавьте следующий код вверху любого файла, из которого будет использоваться клиент StackExchange.Redis для доступа к кэшу Azure Redis.

    using StackExchange.Redis;

> Клиенту StackExchange.Redis необходима среда .NET Framework 4 или выше.

Подключение к кэшу Redis для Azure управляется классом `ConnectionMultiplexer`. Этот класс спроектирован для общего использования и повторного использования клиентским приложением и не нуждается в создании нового экземпляра для каждой отдельной операции.

Для подключения к кэшу Redis для Azure и возврата экземпляра подключенного `ConnectionMultiplexer` вызовите статический метод `Connect` и передайте конечную точку кэша и ключ способом, аналогичным показанному в следующем примере. Используйте ключ Azure, созданный на портале службы, как параметр пароля.

    ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

> Предупреждение: Никогда не сохраняйте учетные данные в исходном коде. Для сохранения простоты примера они показаны в исходном коде. См. раздел [Веб-сайты Windows Azure: как работают строки приложений и строки подключения][] для получения информации о том, как хранить учетные данные.

Если вы не хотите использовать SSL, установите значение `ssl=false` или просто передайте конечную точку и ключ.

    connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,password=...");

> За дополнительной информацией по вариантам расширенных настроек соединения обратитесь к раз делу [Модель конфигурации StackExchange.Redis][].

Конечную точку и ключи кэша можно получить на портале управления Azure предварительной версии в лезвии для экземпляра кэша.

![Свойства кэша][]

![Управление ключами][]

После установки соединения верните ссылку на базу данных кэша Redis, вызвав метод `ConnectionMultiplexer.GetDatabase`.

    // connection referes to a previously configured ConnectionMultiplexer
    IDatabase cache = connection.GetDatabase();

> Объект, возвращенный из метода `GetDatabase` — это промежуточный сквозной объект, не требующий сохранения.

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

Элементы можно хранить и извлекать из кэша с использованием методов `StringSet` и `StringGet`.

    // If key1 exists, it is overwritten.
    cache.StringSet("key1", "value1");

    string value = cache.StringGet("key1");

> Redis хранит большинство данных в строках Redis, но эти строки могут содержать данные многих типов, включая сериализованные двоичные данные, которые можно использовать при сохранении объектов .NET в кэше.

При вызове `StringGet`, если объект существует, он возвращается, а если нет, возвращается значение NULL. В этом случае можно извлечь значение из желаемого источника данных и сохранить его в кэше для последующего использования. Это называется шаблоном "кэш на стороне".

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

> Кэш Azure Redis может кэшировать объекты .NET, а также несложные типы данных, но прежде чем объект .NET можно будет сохранить, он должен быть сериализован. Это ответственность разработчика приложения, что дает разработчику гибкость в выборе сериализатора. За дополнительной информацией обратитесь к разделу [Работа с объектами .NET в кэше][].

<a name="specify-expiration"></a>

## Указание срока действия объекта в кэше

Для указания срока действия объекта в кэше используйте параметр `TimeSpan` метода `StringSet`.

    cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

<a name="store-session"></a>

## Сохранение состояния сеанса ASP.NET в кэше

Кэш Azure Redis предоставляет поставщик состояний сеансов, который можно использовать для сохранения состояния сеансов в кэше, а не в памяти или в базе данных SQL Server. Для использования
поставщика состояния сеансов с кэшированием вначале настройте кэш, а затем настройте приложение ASP.NET для использования кэша, используя пакет Redis NuGet для состояний сеансов.

Для настройки клиентского приложения в Visual Studio используйте пакет StackExchange.Redis NuGet: щелкните правой кнопкой мыши **обозреватель решений** и выберите **Управление пакетами NuGet**.

![Управление пакетами NuGet][]

Введите **RedisSessionStateProvider** в текстовом поле **Поиск в сети**, выберите его из результатов и щелкните **Установить**.

![Пакет Redis NuGet для состояний сеансов][]

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
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="contoso5.redis.cache.windows.net" 
        accessKey="..." ssl="false" />
      </providers>
    </sessionState>

> Обратите внимание, что интервал повторения задается в миллисекундах.

Закомментируйте стандартный поставщик состояний сеансов **InProc**.

    <!-- <sessionState mode="InProc" customProvider="DefaultSessionProvider">
      <providers>
        <add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
      </providers>
    </sessionState> -->

За дополнительной информацией о настройках и использовании поставщика состояний сеансов Redis обратитесь к разделу [Поставщик состояний сеансов Azure Redis][].

<a name="next-steps"></a>

## Дальнейшие действия

После изучения основ кэша Azure Redis
просмотрите следующие ссылки для знакомства с тем, как выполнять более сложные задачи по кэшированию.

-   Больше о клиенте StackExchange.Redis: [документация по клиенту кэша StackExchange.Redis][2]
-   См. документацию по [redis][]: прочитайте [статью о типах данных redis][] и [пятнадцатиминутное введение в типы данных Redis][].
-   См. справочник MSDN: [кэш Azure Redis][]

<!-- INTRA-TOPIC LINKS --> <!-- IMAGES --> <!-- LINKS -->

  [Дальнейшие действия]: #next-steps
  [Что такое кэш Azure Redis]: #what-is
  [Начало работы с кэшем Azure Redis]: #getting-started-cache-service
  [Создание кэша]: #create-cache
  [Настройка клиентов кэша]: #NuGet
  [Работа с кэшами]: #working-with-caches
  [Подключение к кэшу]: #connect-to-cache
  [Добавление и извлечение объектов из кэша]: #add-object
  [Указание срока действия объекта в кэше]: #specify-expiration
  [Сохранение состояния сеанса ASP.NET в кэше]: #store-session
  [Сведения о ценах — кэш]: http://www.windowsazure.com/en-us/pricing/details/cache/
  [Обзор кэша Azure Redis]: http://go.microsoft.com/fwlink/?LinkId=320830
  [Настройка кэша]: #enable-caching
  [Новый кэш]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-new-cache-menu.png
  [1]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-create.png
  [Использование групп ресурсов для управления ресурсами Azure]: http://azure.microsoft.com/ru-ru/documentation/articles/azure-preview-portal-using-resource-groups/
  [Кэш создан]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-created.png
  [Лезвие "Обзор"]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png
  [Кэши]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png
  [StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
  [2]: http://github.com/StackExchange/StackExchange.Redis#documentation
  [Управление пакетами NuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png
  [Пакет NuGet StackExchange.Redis]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png
  [Веб-сайты Windows Azure: как работают строки приложений и строки подключения]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
  [Модель конфигурации StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md
  [Свойства кэша]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png
  [Управление ключами]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png
  [Работа с объектами .NET в кэше]: http://msdn.microsoft.com/en-us/library/dn690521.aspx#Objects
  [Пакет Redis NuGet для состояний сеансов]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png
  [Поставщик состояний сеансов Azure Redis]: http://go.microsoft.com/fwlink/?LinkId=398249
  [redis]: http://redis.io/documentation
  [статью о типах данных redis]: http://redis.io/topics/data-types
  [пятнадцатиминутное введение в типы данных Redis]: http://redis.io/topics/data-types-intro
  [кэш Azure Redis]: http://go.microsoft.com/fwlink/?LinkId=398247
