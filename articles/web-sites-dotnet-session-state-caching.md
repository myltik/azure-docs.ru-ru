<properties linkid="video-center-index" urlDisplayName="index" pageTitle="Use ASP.NET session state with Azure Websites" metaKeywords="azure cache service session state" description="Learn how to use the Azure Cache Service to support ASP.NET session state caching." metaCanonical="" services="cache" documentationCenter=".NET" title="How to Use ASP.NET Session State with Azure Websites" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra"></tags>

# Как использовать ASP.NET Session State с веб-сайтами Azure

*Автор: [Рик Андерсон][Рик Андерсон]. Обновление: 1 апреля 2014 г.*

В этом разделе объясняется, как использовать службу кэша Azure Redis (предварительная версия) для поддержки состояния сеанса.

Если ваше веб-приложение ASP.NET использует состояние сеанса, вам потребуется настроить поставщик состояния внешнего сеанса (либо службу Redis Cache или поставщик состояния сеанса SQL Server). Если вы используете дату сеанса и не используете внешний поставщик, вы будете ограничены одним экземпляром вашего веб-приложения. Служба Redis Cache самая простая, и включить ее можно очень быстро.

Основные шаги для использования службы кэша (предварительная версия) для кэширования состояния сеанса включают:

-   [Создание кэша.][Создание кэша.]
-   [Добавьте пакет NuGet RedisSessionStateProvider в свое веб-приложение.][Добавьте пакет NuGet RedisSessionStateProvider в свое веб-приложение.]
-   [Изменение файла web.config.][Изменение файла web.config.]
-   [Использование объекта сеанса для хранения и получения кэшированных элементов.][Использование объекта сеанса для хранения и получения кэшированных элементов.]

## <span id="createcache"></span></a>Создание кэша

Следуйте [этим инструкциям][этим инструкциям], чтобы создать кэш.

## <span id="configureproject"></span></a>Добавьте пакет NuGet RedisSessionStateProvider в свое веб-приложение

Установите пакет NuGet `RedisSessionStateProvider`. Используйте следующую команду, чтобы установить из консоли менеджера пакетов (**Инструменты** \> **Диспетчер пакетов NuGet** \> **Консоль диспетчера пакетов**):

`PM> Install-Package RedisSessionStateProvider -IncludePrerelease`

Чтобы выполнить установку из меню **Инструменты** \> **Диспетчер пакетов NuGet** \> **Управление пакетами NugGet для решения**, выполните поиск по `RedisSessionStateProvider` и обязательно укажите параметр **Включить предварительный выпуск**.

Для получения дополнительных сведений см. [страницу NuGet RedisSessionStateProvider][страницу NuGet RedisSessionStateProvider] и [Настройка клиента кэша][Настройка клиента кэша].

## <span id="configurewebconfig"></span></a>Изменение файла Web.Config

Кроме внесения ссылки на сборку для кэша, NuGet-пакет добавляет записи заглушки в файл *web.config*.

1.  Откройте *web.config* и найдите элемент **sessionState**.

2.  Введите значения для `host`, `accessKey`, `port` (необходимо указать 6380 в качестве порта SSL) и установите `SSL` в значение `true`. Эти значения можно получить на портале управления Azure предварительной версии в выноске для экземпляра кэша. Дополнительные сведения см. в разделе [Подключение к кэшу][Подключение к кэшу].
    Следующая маркировка показывает изменения в файле *web.config*.

<pre class="prettyprint">
 &lt;system.web&gt; &lt;customErrors mode=&quot;Off&quot; /&gt; &lt;authentication mode=&quot;None&quot; /&gt; &lt;compilation debug=&quot;true&quot; targetFramework=&quot;4.5&quot; /&gt; &lt;httpRuntime targetFramework=&quot;4.5&quot; /&gt; &lt;sessionState mode=&quot;Custom&quot; customProvider=&quot;RedisSessionProvider&quot;&gt; &lt;providers&gt; &lt;!--&lt;add name=&quot;RedisSessionProvider&quot; host = &quot;127.0.0.1&quot; [String] port = &quot;&quot; [number] accessKey = &quot;&quot; [String] ssl = &quot;false&quot; [true|false] throwOnError = &quot;true&quot; [true|false] retryTimeoutInMilliseconds = &quot;0&quot; [number] databaseId = &quot;0&quot; [number] applicationName = &quot;&quot; [String] /&gt;--&gt; &lt;add name=&quot;RedisSessionProvider&quot; type=&quot;Microsoft.Web.Redis.RedisSessionStateProvider&quot; port=&quot;6380&quot; host=&quot;movie2.redis.cache.windows.net&quot; accessKey=&quot;m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=&quot; ssl=&quot;true&quot; /&gt; &lt;!--&lt;add name=&quot;MySessionStateStore&quot; type=&quot;Microsoft.Web.Redis.RedisSessionStateProvider&quot; host=&quot;127.0.0.1&quot; accessKey=&quot;&quot; ssl=&quot;false&quot; /&gt;--&gt; &lt;/providers&gt; &lt;/sessionState&gt; &lt;/system.web&gt;
</pre>

</p>
## <span id="usesessionobject"></span></a>Использование объекта сеанса в коде

Последним шагом является начало использования объекта сеанса в коде ASP.NET. Добавление объектов к состоянию сеанса выполняется с помощью метода **Session.Add**. Этот метод использует пары "ключ-значение" для хранения элементов в кэше состояния сеанса.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

Следующий код извлекает значение из состояния сеанса.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;  

Вы можете также использовать Redis Cache для кэширования объектов в вашем веб-приложении. Дополнительные сведения см. в разделе [Приложение для обработки фильмов MVC с кэшем Azure Redis за 15 минут][Приложение для обработки фильмов MVC с кэшем Azure Redis за 15 минут].
Для получения дополнительных сведений о том, как использовать состояние сеанса ASP.NET, см. раздел [Обзор состояния сеанса ASP.NET][Обзор состояния сеанса ASP.NET].

  [Рик Андерсон]: https://twitter.com/RickAndMSFT
  [Создание кэша.]: #createcache
  [Добавьте пакет NuGet RedisSessionStateProvider в свое веб-приложение.]: #configureproject
  [Изменение файла web.config.]: #configurewebconfig
  [Использование объекта сеанса для хранения и получения кэшированных элементов.]: #usesessionobject
  [этим инструкциям]: http://azure.microsoft.com/ru-ru/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#create-cache
  [страницу NuGet RedisSessionStateProvider]: http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/
  [Настройка клиента кэша]: http://azure.microsoft.com/ru-ru/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#NuGet
  [Подключение к кэшу]: http://azure.microsoft.com/ru-ru/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#connect-to-cache
  [Приложение для обработки фильмов MVC с кэшем Azure Redis за 15 минут]: http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
  [Обзор состояния сеанса ASP.NET]: http://msdn.microsoft.com/ru-ru/library/ms178581.aspx
