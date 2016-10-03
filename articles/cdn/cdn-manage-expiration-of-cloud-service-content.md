<properties
 pageTitle="Управление сроком действия содержимого веб-приложений и облачных служб Azure, ASP.NET или IIS в Azure CDN | Microsoft Azure"
 description="Описание процедуры управления сроком действия содержимого облачных служб в Azure CDN"
 services="cdn"
 documentationCenter=".NET"
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="09/19/2016"
 ms.author="casoper"/>

# Управление сроком действия содержимого веб-приложений и облачных служб Azure, ASP.NET или IIS в Azure CDN

> [AZURE.SELECTOR]
- [Веб-приложения и облачные службы Azure, ASP.NET или IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Служба BLOB-объектов в службе хранилища Azure](cdn-manage-expiration-of-blob-content.md)

Файлы из любого общедоступного исходного веб-сервера могут кэшироваться в Azure CDN до истечения его срока жизни. Срок жизни определяется [заголовком *Cache-Control*](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9), указанным в HTTP-ответе исходного сервера. В этой статье описано, как настроить заголовки `Cache-Control` для веб-приложений Azure, облачных служб Azure, приложений ASP.NET и сайтов IIS, которые все настроены аналогичным образом.

>[AZURE.TIP] Вы можете не указывать срок жизни для файла. Тогда Azure CDN по умолчанию применит срок жизни длительностью семь дней.
>
>Дополнительные сведения о том, как Azure CDN ускоряет доступ к файлам и другим ресурсам, см. в статье [Общие сведения о сети доставки содержимого (CDN) Azure](./cdn-overview.md).

## Настройка заголовков Cache-Control в конфигурации

Вы можете управлять частотой обновления статического содержимого, такого как изображения и таблицы стилей, изменяя файлы **applicationHost.config** или **web.config** своего веб-приложения. Элемент **system.webServer\\staticContent\\clientCache** в файле конфигурации установит заголовок `Cache-Control` для содержимого. Параметры конфигурации файла **web.config** будут влиять на все элементы в этой папке и вложенных в нее папках, если это не переопределено на уровне вложенной папки. Например, можно установить значение по умолчанию для срока жизни в корневой папке, чтобы все статическое содержимое кэшировалось в течение 3 дней, но предусмотреть вложенную папку с содержимым, которое изменяется чаще, и задать для нее частоту кэширования 6 часов. Файл **applicationHost.config** повлияет на все приложения на сайте, но может быть переопределен в файлах **web.config** приложений.

В следующем XML-коде показан пример использования параметра **clientCache** для указания максимального возраста в 3 дня:

```xml
<configuration>
	<system.webServer>
		<staticContent>
			<clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
		</staticContent>
	</system.webServer>
</configuration>
```

При указании **UseMaxAge** в ответ добавляется заголовок `Cache-Control: max-age=<nnn>` на основе значения, указанного в атрибуте **CacheControlMaxAge**. Временной диапазон для атрибута **cacheControlMaxAge** имеет вид <дни>.<часы>:<мин>:<сек>. Дополнительные сведения об узле **clientCache** см. в разделе [Кэш клиента <кэш\_клиента>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).

## Настройка заголовков Cache-Control в коде

Для приложений ASP.NET можно настроить режим кэша CDN программно, задав свойство **HttpResponse.Cache**. Дополнительные сведения о свойстве **HttpResponse.Cache**, см. в разделах [Свойство HttpResponse.Cache](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) и [Класс HttpCachePolicy](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).

Если требуется программно кэшировать содержимое приложения в ASP.NET, убедитесь, что это содержимое помечено как кэшируемое, присвоив HttpCacheability значение *Public*. Кроме того, убедитесь, что задан проверяющий элемент управления кэша. Проверяющий элемент управления кэша может быть меткой времени последнего изменения, заданной с помощью вызова SetLastModified, или значением etag, заданным с помощью вызова SetETag. При необходимости можно также указать срок действия кэша, вызвав SetExpires, или можно положиться на эвристику кэширования по умолчанию, описанную ранее в этом документе.

Например, для кэширования содержимого на срок в один час добавьте следующие строки:

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## Дальнейшие действия

- [Сведения об элементе **clientCache**](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
- [Документация по свойству **HttpResponse.Cache**](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx)
- [Документация по **классу HttpCachePolicy**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)

<!---HONumber=AcomDC_0921_2016-->