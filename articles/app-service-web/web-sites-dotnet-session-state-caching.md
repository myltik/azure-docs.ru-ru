<properties 
	pageTitle="Состояние сеанса с кэшем Redis для Azure в службе приложений Azure" 
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


# Состояние сеанса с кэшем Redis для Azure в службе приложений Azure


В этом разделе объясняется, как использовать службу кэша Redis для Azure для состояния сеанса.

Если ваше веб-приложение ASP.NET использует состояние сеанса, вам потребуется настроить поставщик состояния внешнего сеанса (либо службу Redis Cache или поставщик состояния сеанса SQL Server). Если вы используете дату сеанса и не используете внешний поставщик, вы будете ограничены одним экземпляром вашего веб-приложения. Служба Redis Cache самая простая, и включить ее можно очень быстро.

<h2><a id="createcache"></a>Создание кэша</h2>
Следуйте [этим инструкциям](../cache-dotnet-how-to-use-azure-redis-cache.md#create-cache), чтобы создать кэш.

<h2><a id="configureproject"></a>Добавьте пакет NuGet RedisSessionStateProvider в свое веб-приложение</h2>
Установите пакет NuGet `RedisSessionStateProvider`. Используйте следующую команду, чтобы установить из консоли менеджера пакетов (**Сервис** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`
  
Чтобы выполнить установку из меню **Сервис** > **Диспетчер пакетов NuGet** > **Управление пакетами NugGet для решения**, выполните поиск по запросу `RedisSessionStateProvider`.

Для получения дополнительных сведений см. [страницу NuGet RedisSessionStateProvider](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/) и [Настройка клиента кэша](../cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

<h2><a id="configurewebconfig"></a>Изменение файла Web.Config</h2>
Кроме внесения ссылки на сборку для кэша, NuGet-пакет добавляет записи заглушки в файл *web.config*.

1. Откройте *web.config* и найдите элемент **sessionState**.

1. Введите значения для `host`, `accessKey`, `port` (необходимо указать 6380 в качестве порта SSL) и установите `SSL` в значение `true`. Эти значения можно получить в выноске для экземпляра кэша на [портале Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Дополнительные сведения см. в разделе [Подключение к кэшу](../cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Обратите внимание, для новых кэшей все порты, кроме SSL, по умолчанию запрещены. Дополнительную информацию о подключении без порта SSL см. в разделе [Порты доступа](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) статьи [Настройка кэша в службе кэша Redis для Azure](https://msdn.microsoft.com/library/azure/dn793612.aspx). В следующем примере показаны изменения в файле *web.config*.


  <pre class="prettyprint">  
    &lt;system.web>
    &lt;customErrors mode="Off" />
    &lt;authentication mode="None" />
    &lt;compilation debug="true" targetFramework="4.5" />
    &lt;httpRuntime targetFramework="4.5" />
  &lt;sessionState mode="Custom" customProvider="RedisSessionProvider">
      &lt;providers>  
          &lt;!--&lt;add name="RedisSessionProvider" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
          />-->
         &lt;add name="RedisSessionProvider" 
              type="Microsoft.Web.Redis.RedisSessionStateProvider" 
              <mark>port="6380"
              host="movie2.redis.cache.windows.net" 
              accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
              ssl="true"</mark> />
      &lt;!--&lt;add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />-->
      &lt;/providers>
    &lt;/sessionState>
  &lt;/system.web></pre>


<h2><a id="usesessionobject"></a>Использование объекта сеанса в коде</h2>
Последним шагом является начало использования объекта сеанса в коде ASP.NET. Добавление объектов к состоянию сеанса выполняется с помощью метода **Session.Add**. Этот метод использует пары "ключ-значение" для хранения элементов в кэше состояния сеанса.

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

Следующий код извлекает значение из состояния сеанса.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue;	

Вы можете также использовать Redis Cache для кэширования объектов в вашем веб-приложении. Дополнительные сведения см. в разделе [Приложение для обработки фильмов MVC с кэшем Redis для Azure за 15 минут](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/). Дополнительные сведения об использовании состояния сеанса ASP.NET см. в разделе [Общие сведения о состоянии сеанса ASP.NET][].

>[AZURE.NOTE]Если вы хотите приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.

## Изменения
* Указания по изменениям при переходе от веб-сайтов к службе приложений см. в разделе [Служба приложений Azure и ее влияние на существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
* Руководство по смене старого портала на новый портал см. в разделе [Справочник по навигации на предварительной версии портала](http://go.microsoft.com/fwlink/?LinkId=529715).

  *Автор: [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT)*
  
  [installed the latest]: http://www.windowsazure.com/downloads/?sdk=net
  [Общие сведения о состоянии сеанса ASP.NET]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
 

<!---HONumber=62-->