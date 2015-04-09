<properties 
	pageTitle="Состояние сеанса с кэшем Azure Redis в службе приложений Azure" 
	description="Узнайте, как использовать службу кэша Azure для поддержки кэширования состояний сеансов ASP.NET." 
	services="app-service\web" 
	documentationCenter=".net" 
 	authors="Rick-Anderson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="riande"/>


# Состояние сеанса с кэшем Azure Redis в службе приложений Azure


В этом разделе объясняется, как использовать службу кэша Azure Redis для поддержки состояния сеанса.

Если веб-приложение ASP.NET использует состояние сеанса, то придется настроить поставщик состояния внешнего сеанса (либо службу Redis Cache или поставщик состояния сеансов SQL Server). Если вы используете дату сеанса и не используете внешний поставщик, вы будете ограничены одним экземпляром вашего веб-приложения. Служба Redis Cache самая простая из этих вариантов, и включить ее можно очень быстро.

<h2><a id="createcache"></a>Создание кэша</h2>
Чтобы создать кэш, следуйте [этим инструкциям](cache-dotnet-how-to-use-azure-redis-cache.md#create-cache).

<h2><a id="configureproject"></a>Добавление пакета NuGet RedisSessionStateProvider в свое веб-приложение</h2>
Установите пакет NuGet `RedisSessionStateProvider`.  С помощью следующей команды установите консоль диспетчера пакетов (**Инструменты** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`
  
Чтобы выполнить установку из меню **Инструменты** > **Диспетчер пакетов NuGet** > **Управление пакетами NugGet для решения**, выполните поиск по запросу `RedisSessionStateProvider`.

Дополнительную информацию см. на [странице NuGet RedisSessionStateProvider](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ ) и в статье [Настройка клиента кэша ](cache-dotnet-how-to-use-azure-redis-cache.md#ыыNuGet).

<h2><a id="configurewebconfig"></a>Изменение файла Web.Config</h2>
Кроме внесения ссылки на сборку для кэша, NuGet-пакет добавляет записи заглушки в файл *web.config*. 

1. Откройте  *web.config* и найдите элемент **sessionState**.

1. Введите значения для  `host`, `accessKey`, `port` (необходимо указать 6380 в качестве порта SSL) и установите для `SSL` значение `true`. Эти значения можно получить в выноске для экземпляра кэша на [портале Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Дополнительную информацию см. в статье [Подключение к кэшу](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Обратите внимание, для новых кэшей все порты, кроме SSL, по умолчанию запрещены. Дополнительную информацию о подключении без порта SSL см. в разделе [Порты доступа](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) статьи [Настройка кэша в службе кэша Azure Redis](https://msdn.microsoft.com/library/azure/dn793612.aspx). В следующем примере показаны изменения в файле *web.config*.


  <pre class="prettyprint">  
    &lt;system.web&gt;
    &lt;customErrors mode="Off" /&gt;
    &lt;authentication mode="None" /&gt;
    &lt;compilation debug="true" targetFramework="4.5" /&gt;
    &lt;httpRuntime targetFramework="4.5" /&gt;
  &lt;sessionState mode="Custom" customProvider="RedisSessionProvider"&gt;
      &lt;providers&gt;  
          &lt;!--&lt;add name="RedisSessionProvider" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
          /&gt;--&gt;
         &lt;add name="RedisSessionProvider" 
              type="Microsoft.Web.Redis.RedisSessionStateProvider" 
              <mark>port="6380"
              host="movie2.redis.cache.windows.net" 
              accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
              ssl="true"</mark> /&gt;
      &lt;!--&lt;add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" /&gt;--&gt;
      &lt;/providers&gt;
    &lt;/sessionState&gt;
  &lt;/system.web&gt;</pre>


<h2><a id="usesessionobject"></a>Использование объекта сеанса в коде</h2>
Последний шаг - начать использование объекта сеанса в коде ASP.NET. Добавление объектов к состоянию сеанса выполняется с помощью метода **Session.Add**. В данном методе используется пара "ключ-значение" для хранения элементов в кэше состояния сеанса.

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

The following code retrieves this value from session state.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue;	

Можно также использовать Redis Cache для кэширования объектов в своем веб-приложении. Дополнительную информацию см. в разделе [Создание приложения для фильмов MVC с кэшем Azure Redis за 15 минут](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/).
Дополнительную информацию об использовании состояния сеанса ASP.NET см. в разделе [Общая информация о состоянии сеанса ASP.NET][].

>[AZURE.NOTE] Если вы хотите начать работу со службой приложений Azure еще до создания учетной записи Azure, перейдите на страницу [ознакомительной версии службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751). Там вы сможете сразу создать свое первое веб-приложение, правда срок его службы будет ограничен. Никаких кредитных карт и обязательств.

## Изменения
* Сведения об изменении веб-сайтов на службу приложений см. в статье [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714)
* Сведения о переходе со старого портала на новый см. в статье [Справочная информация о навигации по предварительной версии портала](http://go.microsoft.com/fwlink/?LinkId=529715).

  Автор: [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT)
  
  [установлена последняя версия]: http://www.windowsazure.com/downloads/?sdk=net  
  [Общие сведения о состоянии сеанса ASP.NET]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png

<!--HONumber=49-->