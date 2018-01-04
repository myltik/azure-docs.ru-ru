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
ms.openlocfilehash: dca6ca5f21f4a4f1701af57eb40d92094b6a4754
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
---
# <a name="manage-expiration-of-web-content-in-azure-content-delivery-network"></a>Управление сроком действия веб-содержимого в сети доставки содержимого Azure
> [!div class="op_single_selector"]
> * [Веб-содержимое Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [хранилище BLOB-объектов Azure](cdn-manage-expiration-of-blob-content.md)
> 

Файлы из любого общедоступного исходного веб-сервера могут кэшироваться в Azure CDN до истечения срока их жизни. Срок жизни определяется заголовком `Cache-Control`, указанным в HTTP-ответе исходного сервера. В этой статье описано, как определить заголовки `Cache-Control` для функции веб-приложений службы приложений Microsoft Azure, облачных служб Azure, приложений ASP.NET и сайтов IIS. Все они настраиваются сходным образом. Определить заголовок `Cache-Control` можно с помощью файлов конфигурации или программным способом. 

Можно также управлять параметрами кэша на портале Azure, определив [правила кэширования CDN](cdn-caching-rules.md). Если определить одно или несколько правил кэширования и настроить их на **переопределение** или **отключение кэша**, предоставляемые системой параметры, описываемые в этой статье, будут игнорироваться. См. дополнительные сведения о [функции кэширования](cdn-how-caching-works.md).

> [!TIP]
> Срок жизни для файла можно не указывать. В этом случае Azure CDN автоматически применяет стандартное значение TTL (семь дней), если только вы не настроили правила кэширования на портале Azure. Этот срок жизни по умолчанию применяется только к оптимизациям общей веб-доставки. Для оптимизаций больших файлов срок жизни по умолчанию составляет один день, а для оптимизаций потоковой передачи срок жизни по умолчанию составляет один год.
> 
> Дополнительные сведения о том, как Azure CDN ускоряет доступ к файлам и другим ресурсам, см. в статье [Общие сведения о сети доставки содержимого (CDN) Azure](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Определение заголовков Cache-Control с помощью файлов конфигурации
Вы можете управлять частотой обновления статического содержимого, такого как изображения и таблицы стилей, изменяя файлы конфигурации **applicationHost.config** или **web.config** своего веб-приложения. Элемент `<system.webServer>/<staticContent>/<clientCache>` в обоих наборах файлов определяет заголовок `Cache-Control` для содержимого.

### <a name="using-applicationhostconfig-files"></a>Использование файла конфигурации applicationHost.config
Файл **applicationHost.config** — это корневой файл системы конфигурации IIS. Хотя параметры конфигурации в файле **applicationHost.config** влияют на все приложения на сайте, они переопределяются параметрами в любом из используемых для приложений файлов **web.config**.

### <a name="using-webconfig-files"></a>Использование файлов web.config
С помощью файла **web.config** можно настроить поведение всего веб-приложения или определенного каталога в веб-приложении. Как правило, в корневой папке веб-приложения есть хотя бы один файл **web.config**. Параметры конфигурации файлов **web.config** в определенной папке влияют на все элементы в этой папке и вложенных в нее папках, если они не переопределены другим файлом **web.config**. Например, в файле **web.config** в корневой папке веб-приложения можно определить элемент `<clientCache>`, чтобы кэшировать все статическое содержимое веб-приложения раз в три дня. Можно также включить файл **web.config** во вложенную папку с более динамическим содержимым (например, `\frequent`) и настроить его элемент `<clientCache>` для кэширования содержимого вложенной папки раз в шесть часов. В результате содержимое всего веб-сайта будет кэшироваться каждые три дня, а содержимое каталога `\frequent` — каждые шесть часов.  

В следующем XML-коде показано, как в файле конфигурации настроить элемент `<clientCache>` для определения максимального срока в три дня:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Чтобы использовать атрибут **cacheControlMaxAge**, присвойте атрибуту **cacheControlMode** значение `UseMaxAge`. Этот параметр добавляет HTTP-заголовок и директиву (`Cache-Control: max-age=<nnn>`) в ответ. У значения интервала времени для атрибута **cacheControlMaxAge** следующий формат: `<days>.<hours>:<min>:<sec>`. Это значение преобразуется в секунды и используется в качестве значения директивы `Cache-Control` `max-age`. Дополнительные сведения об элементе `<clientCache>` см. в описании [клиентского кэша <clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Определение заголовков Cache-Control программным способом
Для приложений ASP.NET можно настроить режим кэширования CDN программным способом, задав свойство **HttpResponse.Cache** для API .NET. Дополнительные сведения о свойстве **HttpResponse.Cache**, см. в описании [свойства HttpResponse.Cache](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) и [класса HttpCachePolicy](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Чтобы программным способом кэшировать содержимое приложения в ASP.NET, выполните следующие действия.
   1. Убедитесь, что содержимое отмечено как кэшируемое, установив для параметра `HttpCacheability` значение `Public`. 
   2. Установите средство проверки кэша, вызвав один из следующих методов `HttpCachePolicy`:
      - Вызовите `SetLastModified`, чтобы определить значение метки времени для заголовка `Last-Modified`.
      - Вызовите `SetETag`, чтобы определить значение для заголовка `ETag`.
   3. При необходимости укажите срок действия кэша, вызвав `SetExpires`, чтобы определить значения для заголовка `Expires`. В противном случае применяется эвристический метод кэширования по умолчанию, описанный выше в этом документе.

Например, чтобы кэшировать содержимое в течение одного часа, добавьте следующий код C#:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Проверка заголовка Cache-Control
Вы легко можете проверить установленный для веб-содержимого срок жизни. Используя встроенные в браузер [средства разработчика](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), убедитесь, что ваше веб-содержимое содержит заголовок ответа `Cache-Control`. Для просмотра заголовков ответа можно использовать и другие средства, например **wget**, [Postman](https://www.getpostman.com/) или [Fiddler](http://www.telerik.com/fiddler).

## <a name="next-steps"></a>Следующие шаги
* [Сведения об элементе **clientCache**](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).
* [Документация по свойству **HttpResponse.Cache**](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx). 
* [Документация по **классу HttpCachePolicy**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  
* [Дополнительные сведения о кэшировании](cdn-how-caching-works.md)
