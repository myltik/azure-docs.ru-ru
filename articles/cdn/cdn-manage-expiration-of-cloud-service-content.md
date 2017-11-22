---
title: "Управление сроком действия веб-содержимого в сети доставки содержимого Azure | Microsoft Docs"
description: "Узнайте, как управлять сроком действия содержимого веб-приложений и облачных служб Azure, ASP.NET или IIS в Azure CDN."
services: cdn
documentationcenter: .NET
author: zhangmanling
manager: erikre
editor: 
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/10/2017
ms.author: mazha
ms.openlocfilehash: d58a245923242b3963b188ca869e8290d999c0a2
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2017
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>Управление сроком действия веб-содержимого в сети доставки содержимого Azure
 в Azure CDN
> [!div class="op_single_selector"]
> * [Веб-приложения и облачные службы Azure, ASP.NET или IIS](cdn-manage-expiration-of-cloud-service-content.md)
> * [хранилище BLOB-объектов Azure](cdn-manage-expiration-of-blob-content.md)
> 

Файлы из любого общедоступного исходного веб-сервера могут кэшироваться в Azure CDN до истечения срока их жизни (TTL). Срок жизни определяется [`Cache-Control`заголовком](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9), указанным в HTTP-ответе исходного сервера. В этой статье описано, как настроить заголовки `Cache-Control` для функции веб-приложений службы приложений Microsoft Azure, облачных служб Azure, приложений ASP.NET и сайтов IIS. Все они настраиваются сходным образом.

> [!TIP]
> Срок жизни для файла можно не указывать. Тогда Azure CDN по умолчанию применит срок жизни длительностью семь дней.
> 
> Дополнительные сведения о том, как Azure CDN ускоряет доступ к файлам и другим ресурсам, см. в статье [Общие сведения о сети доставки содержимого (CDN) Azure](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-in-configuration-files"></a>Настройка заголовков Cache-Control в файлах конфигурации
Вы можете управлять частотой обновления статического содержимого, такого как изображения и таблицы стилей, изменяя файлы **applicationHost.config** или **web.config** своего веб-приложения. Элемент **system.webServer\staticContent\clientCache** в файле конфигурации устанавливает заголовок `Cache-Control` для вашего содержимого. Параметры конфигурации файлов **web.config** влияют на все элементы в этой папке и вложенных в нее папках, если они не переопределены на уровне вложенной папки. Например, можно установить настройку TTL по умолчанию на уровне папки root, чтобы кэшировать все статическое содержимое в течение трех дней, а для вложенной папки с более разнообразным содержимым задать кэширование содержимого в течение только шести часов. Хотя настройки файла **applicationHost.config** влияют на все приложения на сайте, они переопределяются настройками любых существующих файлов **web.config** в приложениях.

В следующем XML-коде показан пример использования параметра **clientCache** для указания максимального возраста в три дня:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

При указании **UseMaxAge** в ответ добавляется заголовок `Cache-Control: max-age=<nnn>` на основе значения, указанного в атрибуте **CacheControlMaxAge**. Формат временного диапазона для атрибута **cacheControlMaxAge** имеет вид `<days>.<hours>:<min>:<sec>`. Дополнительные сведения об узле **clientCache** см. по [этой ссылке<clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-in-code"></a>Настройка заголовков Cache-Control в коде
Для приложений ASP.NET можно настроить режим кэширования CDN программным способом, задав свойство **HttpResponse.Cache**. Дополнительные сведения о свойстве **HttpResponse.Cache**, см. в описании [свойства HttpResponse.Cache](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) и [класса HttpCachePolicy](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Чтобы программным способом кэшировать содержимое приложения в ASP.NET, выполните следующие действия.
   1. Убедитесь, что содержимое отмечено как кэшируемое, установив для параметра `HttpCacheability` значение *Public*. 
   2. Задайте средство проверки кэша, вызвав один из следующих методов:
      - Вызовите `SetLastModified`, чтобы установить временную метку LastModified.
      - Вызовите `SetETag`, чтобы установить значение `ETag`.
   3. При необходимости укажите срок действия кэша, вызвав `SetExpires`. В противном случае применяется эвристический метод кэширования по умолчанию, описанный выше в этом документе.

Например, чтобы кэшировать содержимое в течение одного часа, добавьте следующий код C#:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="next-steps"></a>Дальнейшие действия
* [Сведения об элементе **clientCache**](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).
* [Документация по свойству **HttpResponse.Cache**](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx). 
* [Документация по **HttpCachePolicy Class**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

