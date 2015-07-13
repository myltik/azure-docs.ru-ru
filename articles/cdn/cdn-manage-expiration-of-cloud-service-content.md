<properties 
 pageTitle="Управление сроком действия содержимого облачных служб в сети доставки содержимого (CDN) Azure" 
 description="" 
 services="cdn" 
 documentationCenter=".NET" 
 authors="zhangmanling" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="cdn" 
 ms.workload="media" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="04/25/2015" 
 ms.author="mazha"/>

#Управление сроком действия содержимого облачных служб в сети доставки содержимого (CDN) Azure

Наибольшие преимущества от кэширования Azure CDN получают объекты, которые часто запрашиваются в течение их срока жизни (TTL). Объект остается в кэше в течение срока жизни, а затем обновляется из облачной службы по истечении этого времени. Затем процесс повторяется.

Если значения кэша не указаны, срок жизни объекта составляет 7 дней.

Для статического содержимого, такого как изображения и таблицы стилей, можно управлять частотой обновления, поместив файл web.config в папку CDN с содержимым и изменив параметры **clientCache** для управления заголовком Cache-Control. Параметры файла web.config будут влиять на все элементы в данной папке и вложенных в нее папках, если это не переопределено в одной из вложенных папок. Например, можно установить значение по умолчанию для срока жизни в корневой папке, чтобы все статическое содержимое кэшировалось в течение 3 дней, но предусмотреть вложенную папку с содержимым, которое изменяется чаще, и задать для нее частоту кэширования 6 часов.

В следующем XML-коде показан пример использования параметра **clientCache** для указания максимального возраста в 3 дня:

	<configuration> 
	  <system.webServer> 
	        <staticContent> 
	            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" /> 
	        </staticContent> 
	  </system.webServer> 
	</configuration>

При указании **UseMaxAge** в ответ добавляется Cache-Control: max-age=<nnn> на основе значения, указанного в атрибуте **CacheControlMaxAge**. Формат временного диапазона для атрибута **cacheControlMaxAge** имеет вид <days>.<hours>:<min>:<sec>. Дополнительные сведения об узле **clientCache** см. в разделе [Кэш клиента<clientCache>](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).

Для получения содержимого, возвращенного из приложения, такого как ASPX-страницы, можно настроить режим кэша CDN программно, задав свойство **HttpResponse.Cache**. Дополнительные сведения о свойстве **HttpResponse.Cache**, см. в разделах [Свойство HttpResponse.Cache](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) и [Класс HttpCachePolicy](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).

Если требуется программно кэшировать содержимое приложения, убедитесь, что это содержимое помечено как кэшируемое, присвоив HttpCacheability значение *Public*. Кроме того, убедитесь, что задан проверяющий элемент управления кэша. Проверяющий элемент управления кэша может быть меткой времени последнего изменения, заданной с помощью вызова SetLastModified, или значением etag, заданным с помощью вызова SetETag. При необходимости можно также указать срок действия кэша, вызвав SetExpires, или можно положиться на эвристику кэширования по умолчанию, описанную ранее в этом документе.

Например, для кэширования содержимого на срок в один час добавьте следующие строки:

            // Set the caching parameters.
            Response.Cache.SetExpires(DateTime.Now.AddHours(1));
            Response.Cache.SetCacheability(HttpCacheability.Public);
            Response.Cache.SetLastModified(DateTime.Now);

##См. также

[Управление сроком действия содержимого BLOB-объекта в сети доставки содержимого (CDN) Azure](./cdn-manage-expiration-of-blob-content.md)

<!---HONumber=62-->