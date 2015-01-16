<properties urlDisplayName="index" pageTitle="Использование состояния сеанса ASP.NET с веб-сайтов Azure" metaKeywords="azure cache service session state" description="Узнайте, как использовать службу кэша Azure для поддержки кэширования состояний сеансов ASP.NET." metaCanonical="" services="cache" documentationCenter=".NET" title="How to Use ASP.NET Session State with Azure Websites" authors="riande"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/13/2014" ms.author="riande" />


# Как использовать ASP.NET Session State с веб-сайтами Azure

*Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT). Обновление: 1 июля 2014 г.*

В этом разделе объясняется, как использовать службу кэша Azure Redis (предварительная версия) для поддержки состояния сеанса.

Если веб-приложение ASP.NET использует состояние сеанса, то придется настроить поставщик состояния внешнего сеанса (либо службу Redis Cache или поставщик состояния сеансов SQL Server). Если вы используете дату сеанса и не используете внешний поставщик, вы будете ограничены одним экземпляром вашего веб-приложения. Служба Redis Cache самая простая из этих вариантов, и включить ее можно очень быстро.

Основные шаги для использования службы кэша (предварительная версия) для кэширования состояния сеанса включают:

* [Создание кэша.](#createcache)
* [Добавьте пакет NuGet RedisSessionStateProvider в свое веб-приложение.](#configureproject)
* [Изменение файла web.config.](#configurewebconfig)
* [Использование объекта сеанса для хранения и получения кэшированных элементов.](#usesessionobject)

<h2><a id="createcache"></a>Создание кэша</h2>
Выполните [следующие инструкции](http://azure.microsoft.com/ru-ru/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#create-cache), чтобы создать кэш.

<h2><a id="configureproject"></a>Добавьте пакет NuGet RedisSessionStateProvider в свое веб-приложение.</h2>
Установите пакет NuGet "RedisSessionStateProvider".  С помощью следующей команды установите консоль диспетчера пакетов (**Инструменты** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**):

  "PM > пакет установки RedisSessionStateProvider -IncludePrerelease"
  
Для установки из пункта **Инструменты** > **Диспетчер пакетов NuGet** > **Управление пакетами NugGet для разрешения**, найдите "RedisSessionStateProvider" и убедитесь в том, что задано **Include Prerelease**.

Дополнительную информацию см. на страницах [NuGet RedisSessionStateProvider](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ ) и [Настройка клиента кэша](http://azure.microsoft.com/ru-ru/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#NuGet).

<h2><a id="configurewebconfig"></a>Изменение файла Web.Config</h2>
Помимо внесения ссылки на сборку для кэша, пакет NuGet добавляет записи заглушки в файл *web.config*. 

1. Откройте файл *web.config* и найдите элемент **sessionState**.

1. Введите значения для параметров "host", "accessKey", "port" (SSL-порт должен иметь значение 6380), а также установите "SSL" в значение "true". Эти значения можно получить на портале управления Azure предварительной версии в выноске для экземпляра кэша. Дополнительные сведения см. в разделе [Подключение к кэшу](http://azure.microsoft.com/ru-ru/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#connect-to-cache).
В следующем примере показаны изменения в файле *web.config*.


  <pre class="prettyprint">  
    <system.web>
    <customErrors mode="Off" />
    <authentication mode="None" />
    <compilation debug="true" targetFramework="4.5" />
    <httpRuntime targetFramework="4.5" />
  <sessionState mode="Custom" customProvider="RedisSessionProvider">
      <providers>  
          <!--<add name="RedisSessionProvider" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
          />-->
         <add name="RedisSessionProvider" 
              type="Microsoft.Web.Redis.RedisSessionStateProvider" 
              <mark>port="6380"
              host="movie2.redis.cache.windows.net" 
              accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
              ssl="true"</mark> />
      <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />-->
      </providers>
    </sessionState>
  </system.web></pre>


<h2><a id="usesessionobject"></a>Использование объекта сеанса в коде</h2>
Последний шаг - начать использование объекта сеанса в коде ASP.NET. Добавление объектов к состоянию сеанса выполняется с помощью метода **Session.Add**. В данном методе используется пара "ключ-значение" для хранения элементов в кэше состояния сеанса.

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

Следующий код извлекает значение из состояния сеанса.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;	

Можно также использовать Redis Cache для кэширования объектов в своем веб-приложении. Дополнительные сведения см. в разделе [Создание приложения для фильмов MVC с кэшем Azure Redis за 15 минут](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/).
Дополнительные сведения об использовании состояния сеанса ASP.NET см. в разделе [Общие сведения о состоянии сеанса ASP.NET][].

  
  
  [установлена последняя версия]: http://www.windowsazure.com/ru-ru/downloads/?sdk=net  
  [Общие сведения о состоянии сеанса ASP.NET]: http://msdn.microsoft.com/ru-ru/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
