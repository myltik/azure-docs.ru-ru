---
title: "Состояние сеанса с кэшем Redis для Azure в службе приложений Azure"
description: "Узнайте, как использовать службу кэша Azure для поддержки кэширования состояний сеансов ASP.NET."
services: app-service\web
documentationcenter: .net
author: Rick-Anderson
manager: wpickett
editor: none
ms.assetid: 4f98d289-2698-464d-85cd-99ec40fad1f2
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 06/27/2016
ms.author: riande
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7ac0fde19d61a9c3a9f54b42982c7096434cb965


---
# <a name="session-state-with-azure-redis-cache-in-azure-app-service"></a>Состояние сеанса с кэшем Redis для Azure в службе приложений Azure
В этом разделе объясняется, как использовать службу кэша Redis для Azure для состояния сеанса.

Если ваше веб-приложение ASP.NET использует состояние сеанса, вам потребуется настроить поставщик состояния внешнего сеанса (либо службу Redis Cache или поставщик состояния сеанса SQL Server). Если вы используете дату сеанса и не используете внешний поставщик, вы будете ограничены одним экземпляром вашего веб-приложения. Служба Redis Cache самая простая, и включить ее можно очень быстро.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="a-idcreatecacheacreate-the-cache"></a><a id="createcache"></a>Создание кэша
Следуйте [этим инструкциям](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-cache), чтобы создать кэш.

## <a name="a-idconfigureprojectaadd-the-redissessionstateprovider-nuget-package-to-your-web-app"></a><a id="configureproject"></a>Добавление пакета NuGet RedisSessionStateProvider в веб-приложение
Установите пакет NuGet `RedisSessionStateProvider` .  Используйте следующую команду, чтобы установить из консоли менеджера пакетов (**Инструменты** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`

Чтобы выполнить установку из меню **Сервис** > **Диспетчер пакетов NuGet** > **Управление пакетами NugGet для решения**, выполните поиск по запросу `RedisSessionStateProvider`.

Для получения дополнительных сведений см. [страницу NuGet RedisSessionStateProvider](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/) и [Настройка клиента кэша](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

## <a name="a-idconfigurewebconfigamodify-the-webconfig-file"></a><a id="configurewebconfig"></a>Изменение файла Web.Config
Кроме внесения ссылки на сборку для кэша, NuGet-пакет добавляет записи заглушки в файл *web.config* . 

1. Откройте *web.config* и найдите элемент **sessionState** .
2. Введите значения для `host`, `accessKey`, `port` (необходимо указать 6380 в качестве порта SSL) и установите `SSL` в значение `true`. Эти значения можно получить в выноске для экземпляра кэша на [портале Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Дополнительные сведения см. в разделе [Подключение к кэшу](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Обратите внимание, для новых кэшей все порты, кроме SSL, по умолчанию запрещены. Дополнительные сведения о том, как настроить использование порта без SSL, см. в статье [Настройка кэша Redis для Azure](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) в разделе [Порты доступа](https://msdn.microsoft.com/library/azure/dn793612.aspx). В следующем примере цветом выделены изменения в файле *web.config*. В частности, изменены значения *port*, *host*, accessKey* и *ssl*.
   
          <system.web>;
            <customErrors mode="Off" />;
            <authentication mode="None" />;
            <compilation debug="true" targetFramework="4.5" />;
            <httpRuntime targetFramework="4.5" />;
            <sessionState mode="Custom" customProvider="RedisSessionProvider">;
              <providers>;  
                  <!--<add name="RedisSessionProvider" 
                    host = "127.0.0.1" [String]
                    port = "" [number]
                    accessKey = "" [String]
                    ssl = "false" [true|false]
                    throwOnError = "true" [true|false]
                    retryTimeoutInMilliseconds = "0" [number]
                    databaseId = "0" [number]
                    applicationName = "" [String]
                  />;-->;
                 <add name="RedisSessionProvider" 
                      type="Microsoft.Web.Redis.RedisSessionStateProvider" 
                      port="6380"
                      host="movie2.redis.cache.windows.net" 
                      accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
                      ssl="true" />;
              <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />;-->;
              </providers>;
            </sessionState>;
          </system.web>;

## <a name="a-idusesessionobjecta-use-the-session-object-in-code"></a><a id="usesessionobject"></a> Использование объекта сеанса в коде
Последним шагом является начало использования объекта сеанса в коде ASP.NET. Добавление объектов к состоянию сеанса выполняется с помощью метода **Session.Add** . Этот метод использует пары "ключ-значение" для хранения элементов в кэше состояния сеанса.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

Следующий код извлекает значение из состояния сеанса.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue;    

Вы можете также использовать Redis Cache для кэширования объектов в вашем веб-приложении. Дополнительные сведения см. в статье о [приложении для обработки фильмов MVC с кэшем Redis для Azure за 15 минут](https://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/).
Дополнительные сведения об использовании состояния сеанса ASP.NET см. в [Обзоре состояния сеанса ASP.NET][ASP.NET Session State Overview].

> [!NOTE]
> Если вы хотите приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](http://go.microsoft.com/fwlink/?LinkId=523751), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.
> 
> 

## <a name="whats-changed"></a>Изменения
* Руководство по переходу от веб-сайтов к службе приложений см. в статье [Служба приложений Azure и существующие службы Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
  
  *Автор: [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT)*

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




<!--HONumber=Nov16_HO2-->


