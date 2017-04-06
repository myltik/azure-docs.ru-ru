---
title: "Пакет SDK и ресурсы для .NET (Azure DocumentDB) | Документация Майкрософт"
description: "Сведения об API и пакетах SDK для .NET, в том числе даты выхода, даты выбытия и изменения, внесенные в каждую версию пакета SDK для DocumentDB .NET."
services: documentdb
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 8e239217-9085-49f5-b0a7-58d6e6b61949
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/29/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: c953615ef370dc89ed09bb8a32dc2caf7a7bcc3b
ms.lasthandoff: 03/30/2017


---
# <a name="documentdb-net-sdk-download-and-release-notes"></a>Пакет SDK для DocumentDB .NET: скачивание и заметки о выпуске
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/en-us/rest/api/documentdb/)
> * [Поставщик ресурсов REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**Скачивание пакета SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>

<tr><td>**Документация по API**</td><td>[Справочная документация по API .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>

<tr><td>**Примеры**</td><td>[Примеры кода для .NET](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**Приступая к работе**</td><td>[Приступая к работе с пакетом SDK .NET для DocumentDB](documentdb-get-started.md)</td></tr>

<tr><td>**Учебник по веб-приложениям**</td><td>[Разработка веб-приложений с использованием DocumentDB](documentdb-dotnet-application.md)</td></tr>

<tr><td>**Текущая поддерживаемая платформа**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>Заметки о выпуске

### <a name="a-name11311131httpswwwnugetorgpackagesmicrosoftazuredocumentdb1131"></a><a name="1.13.1"/>[1.13.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.13.1)
* Исправлена проблема, вызывавшая взаимоблокировки в некоторых асинхронных интерфейсах API при использовании в контексте ASP.NET.

### <a name="a-name11301130httpswwwnugetorgpackagesmicrosoftazuredocumentdb1130"></a><a name="1.13.0"/>[1.13.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.13.0)
* Исправления для повышения устойчивости пакета SDK к автоматической отработке отказа при определенных условиях.

### <a name="a-name11221122httpswwwnugetorgpackagesmicrosoftazuredocumentdb1122"></a><a name="1.12.2"/>[1.12.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.12.2)
* Исправление ошибки, приводившей к исключению WebException: "Удаленное имя не удалось разрешить".
* Добавлена поддержка непосредственного считывания типизированного документа путем добавления новых перегрузок в API ReadDocumentAsync.

### <a name="a-name11211121httpswwwnugetorgpackagesmicrosoftazuredocumentdb1121"></a><a name="1.12.1"/>[1.12.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.12.1)
* Добавлена поддержка LINQ для статистических запросов (COUNT, MIN, MAX, SUM и AVG).
* Исправление ошибки утечки памяти для объекта ConnectionPolicy, вызываемой использованием обработчика событий.
* Исправление ошибки, из-за которой метод UpsertAttachmentAsync не работал, когда использовался тег ETag.
* Исправление ошибки, из-за которой продолжение запросов orderBy между секциями не работало при сортировке по полю строки.

### <a name="a-name11201120httpswwwnugetorgpackagesmicrosoftazuredocumentdb1120"></a><a name="1.12.0"/>[1.12.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.12.0)
* Добавлена поддержка статистических запросов (COUNT, MIN, MAX, SUM и AVG). Дополнительные сведения см. в разделе [Статистические функции](documentdb-sql-query.md#Aggregates).
* Минимальная пропускная способность секционированных коллекций снижена с 10 100 ЕЗ/с до 2500 ЕЗ/с.

### <a name="a-name11141114httpswwwnugetorgpackagesmicrosoftazuredocumentdb1114"></a><a name="1.11.4"/>[1.11.4](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.11.4)
* Исправление ошибки, из-за которой происходил сбой некоторых запросов между секциями в 32-разрядном хост-процессе.
* Исправление ошибки, из-за которой в контейнере сеанса не обновлялся маркер для неудачных запросов в режиме шлюза.
* Исправление ошибки, из-за которой в некоторых случаях запрос с вызовами UDF в проекции завершался сбоем.
* Исправление производительности на стороне клиента для увеличения пропускной способности операций чтения и записи запросов.

### <a name="a-name11131113httpswwwnugetorgpackagesmicrosoftazuredocumentdb1113"></a><a name="1.11.3"/>[1.11.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.11.3)
* Исправление ошибки, из-за которой в контейнере сеанса не обновлялся маркер для неудачных запросов.
* Добавлена поддержка для работы пакета SDK в 32-разрядных хост-процессах. Обратите внимание, что при использовании запросов между секциями для повышения производительности рекомендуется использовать 64-разрядные хост-процессы.
* Повышена производительность для сценариев, применяющих запросы с большим количеством значений ключа секции в выражении IN.
* Заполнена статистика по различным квотам ресурсов в ResourceResponse для запросов на чтение коллекции документов, если задан параметр запроса PopulateQuotaInfo.

### <a name="a-name11111111httpswwwnugetorgpackagesmicrosoftazuredocumentdb1111"></a><a name="1.11.1"/>[1.11.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.11.1)
* Незначительное исправление производительности API CreateDocumentCollectionIfNotExistsAsync, представленного в 1.11.0.
* Исправление производительности в пакете SDK для сценариев, предполагающих большое количество одновременных запросов.

### <a name="a-name11101110httpswwwnugetorgpackagesmicrosoftazuredocumentdb1110"></a><a name="1.11.0"/>[1.11.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.11.0)
* Поддержка новых классов и методов для обработки [веб-канала изменений](documentdb-change-feed.md) документов в коллекции.
* Поддержка продолжения запросов между секциями и некоторые улучшения производительности запросов между секциями.
* Добавление методов CreateDatabaseIfNotExistsAsync и CreateDocumentCollectionIfNotExistsAsync.
* Поддержка LINQ для системных функций: IsDefined, IsNull и IsPrimitive.
* Исправление автоматического переноса сборок Microsoft.Azure.Documents.ServiceInterop.dll и DocumentDB.Spatial.Sql.dll в папку bin приложения посредством BinPlace, если используется пакет NuGet с проектами, которые содержат инструменты project.json.
* Поддержка выдачи трассировок ETW на стороне клиента, что может быть полезно в сценариях отладки.

### <a name="a-name11001100httpswwwnugetorgpackagesmicrosoftazuredocumentdb1100"></a><a name="1.10.0"/>[1.10.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.10.0)
* Добавлена поддержка прямое подключения для секционированных коллекций.
* Повышена производительность для уровня согласованности с ограниченным устареванием.
* Добавлены типы данных Polygon и LineString и задана политика индексирования коллекций для запросов пространственных геозон.
* Добавлена поддержка LINQ для StringEnumConverter, IsoDateTimeConverter и UnixDateTimeConverter при преобразовании предикатов.
* Исправлены различные ошибки в пакете SDK.

### <a name="a-name195195httpswwwnugetorgpackagesmicrosoftazuredocumentdb195"></a><a name="1.9.5"/>[1.9.5](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.5)
* Устранена проблема, которая вызвала следующее исключение NotFoundException: "The read session is not available for the input session token" (Сеанс чтения для входного маркера сеанса недоступен). Это исключение иногда происходило при выполнении запроса к региону чтения геораспределенной учетной записи.
* В классе ResourceResponse предоставляется свойство ResponseStream, которое обеспечивает прямой доступ к базовому потоку из ответа.

### <a name="a-name194194httpswwwnugetorgpackagesmicrosoftazuredocumentdb194"></a><a name="1.9.4"/>[1.9.4](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.4)
* Классы ResourceResponse, FeedResponse, StoredProcedureResponse и MediaResponse изменены для реализации соответствующего открытого интерфейса. Теперь их можно макетировать для развертывания через тестирование (TDD).
* Устранена проблема, из-за которой заголовок ключа секции имел неправильный формат при использовании пользовательского объекта JsonSerializerSettings для сериализации данных.

### <a name="a-name193193httpswwwnugetorgpackagesmicrosoftazuredocumentdb193"></a><a name="1.9.3"/>[1.9.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.3)
* Устранена проблема, из-за которой длительные запросы завершались сбоем и отображалось сообщение об ошибке "Authorization token is not valid at the current time" (Маркер авторизации в настоящее время недействителен).
* Устранена проблема, из-за которой исходный параметр SqlParameterCollection удалялся из запросов top/order-by между секциями.

### <a name="a-name192192httpswwwnugetorgpackagesmicrosoftazuredocumentdb192"></a><a name="1.9.2"/>[1.9.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.2)
* Добавлена поддержка параллельных запросов для секционированных коллекций.
* Добавлена поддержка запросов ORDER BY и TOP между секциями для секционированных коллекций.
* Исправлено отсутствие ссылок на файлы DocumentDB.Spatial.Sql.dll и Microsoft.Azure.Documents.ServiceInterop.dll, которые необходимы при указании ссылки на проект DocumentDB со ссылкой на пакет Nuget для DocumentDB.
* Добавлена возможность использования параметров различных типов, когда применяются определяемые пользователем функции в LINQ. 
* Исправлена ошибка в глобально реплицируемых учетных записях, где вызовы Upsert направлялись в расположения для чтения, а не расположения для записи.
* В интерфейс IDocumentClient добавлены методы, которые отсутствовали: 
  * UpsertAttachmentAsync, который принимает в качестве параметров mediaStream и варианты;
  * CreateAttachmentAsync, который принимает в качестве параметра варианты;
  * и CreateOfferQuery, который принимает в качестве параметра querySpec.
* Открыты общие классы, которые доступны в интерфейсе IDocumentClient.

### <a name="a-name180180httpswwwnugetorgpackagesmicrosoftazuredocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.8.0)
* Добавлена поддержка учетных записей базы данных в нескольких регионах.
* Добавлена поддержка повтора отрегулированных запросов.  Пользователь может задать число повторов и максимальное время ожидания, настроив свойство ConnectionPolicy.RetryOptions.
* Добавлен новый интерфейс IDocumentClient, который определяет сигнатуры всех свойств и методов DocumenClient.  В ходе этого изменения были также изменены методы расширения, которые создают IQueryable и IOrderedQueryable для методов в самом классе DocumentClient.
* Добавлен параметр конфигурации, позволяющий указать ServicePoint.ConnectionLimit для заданного универсального кода ресурса (URI) конечной точки DocumentDB.  Используйте ConnectionPolicy.MaxConnectionLimit, чтобы изменить значение по умолчанию, которое равно 50.
* Не рекомендуется использовать IPartitionResolver и его реализацию.  Теперь поддержка IPartitionResolver устарела. Рекомендуется использовать секционированные коллекции, чтобы увеличить возможности хранилища и пропускную способность.

### <a name="a-name171171httpswwwnugetorgpackagesmicrosoftazuredocumentdb171"></a><a name="1.7.1"/>[1.7.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.1)
* Добавлена перегрузка метода ExecuteStoredProcedureAsync на основе универсального кода ресурса (URI), принимающего RequestOptions в качестве параметра.

### <a name="a-name170170httpswwwnugetorgpackagesmicrosoftazuredocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.0)
* Добавлена поддержка срока жизни для документов.

### <a name="a-name163163httpswwwnugetorgpackagesmicrosoftazuredocumentdb163"></a><a name="1.6.3"/>[1.6.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.3)
* Исправлена ошибка в упаковке Nuget пакета SDK для .NET, связанная с его упаковкой в составе решения облачной службы Azure.

### <a name="a-name162162httpswwwnugetorgpackagesmicrosoftazuredocumentdb162"></a><a name="1.6.2"/>[1.6.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.2)
* Реализованы [секционированные коллекции](documentdb-partition-data.md) и [определяемые пользователем уровни производительности](documentdb-performance-levels.md). 

### <a name="a-name153153httpswwwnugetorgpackagesmicrosoftazuredocumentdb153"></a><a name="1.5.3"/>[1.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
* **[Исправлено]**. Запрос к конечной точке DocumentDB приводит к появлению исключения "System.Net.Http.HttpRequestException: произошла ошибка при копировании содержимого в поток".

### <a name="a-name152152httpswwwnugetorgpackagesmicrosoftazuredocumentdb152"></a><a name="1.5.2"/>[1.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
* Расширенная поддержка LINQ, включая новые операторы разбиения по страницам, условные выражения и сравнение диапазонов.
  * Оператор Take, позволяющий выполнить операцию SELECT TOP в LINQ.
  * Оператор CompareTo, позволяющий сравнивать диапазоны строк.
  * Условный оператор (?) и оператор объединения (??).
* **[Исправлено]**. Порождение исключения ArgumentOutOfRangeException при комбинировании проекции Model с Where-In в запросе LINQ.  [№ 81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151httpswwwnugetorgpackagesmicrosoftazuredocumentdb151"></a><a name="1.5.1"/>[1.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
* **[Исправлено]** Если операция Select не является последней, поставщик LINQ не использовал проекции и неверно определял операцию SELECT *.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150httpswwwnugetorgpackagesmicrosoftazuredocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
* Реализован метод Upsert, добавлен метод UpsertXXXAsync.
* Повышение производительности для всех запросов.
* Поддержка поставщиком LINQ условных операций, функций объединения и методов CompareTo для строковых значений.
* **[Исправлено]**. Поставщик LINQ: реализация метода Contains для списков для создания таких же запросов SQL, как для IEnumerable и массивов.
* **[Исправлено]** BackoffRetryUtility повторно использует то же сообщение HttpRequestMessage вместо создания нового при повторной попытке.
* **[Устарело]**. UriFactory.CreateCollection: теперь следует использовать UriFactory.CreateDocumentCollection.

### <a name="a-name141141httpswwwnugetorgpackagesmicrosoftazuredocumentdb141"></a><a name="1.4.1"/>[1.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
* **[Исправлено]**. Проблемы локализации при использовании региональных параметров, отличных от en, например nl-NL и т. д. 

### <a name="a-name140140httpswwwnugetorgpackagesmicrosoftazuredocumentdb140"></a><a name="1.4.0"/>[1.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
* Маршрутизация на основе идентификатора.
  * Новый вспомогательный класс UriFactory для создания ссылок на ресурсы на основании идентификатора.
  * Новые перегрузки в классе DocumentClient, принимающие URI в качестве параметров.
* Для геопространственных данных в LINQ добавлены методы IsValid() и IsValidDetailed().
* Улучшена поддержка поставщика LINQ.
  * **Математические функции:** Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan, Truncate.
  * **Строковые функции:** Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper.
  * **Функции массивов:** Concat, Contains, Count.
  * **IN** .

### <a name="a-name130130httpswwwnugetorgpackagesmicrosoftazuredocumentdb130"></a><a name="1.3.0"/>[1.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
* Добавлена поддержка изменения политики индексации.
  * Новый метод ReplaceDocumentCollectionAsync в классе DocumentClient.
  * Новое свойство IndexTransformationProgress в ResourceResponse<T> для отслеживания процесса изменения политики индексирования (в процентах).
  * Свойство DocumentCollection.IndexingPolicy теперь является изменяемым.
* Добавлена поддержка индексирования и создания запросов к пространственным типам данных.
  * Новое пространство имен Microsoft.Azure.Documents.Spatial для сериализации и десериализации пространственных типов, таких как Point и Polygon.
  * Новый класс SpatialIndex для индексирования данных GeoJSON, хранящихся в DocumentDB.
* **[Исправлено]** Из выражения LINQ [#38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="a-name120120httpswwwnugetorgpackagesmicrosoftazuredocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
* Зависимость от Newtonsoft.Json 5.0.7. 
* Изменения для поддержки Order By.
  
  * Поставщик LINQ поддерживает OrderBy() или OrderByDescending().
  * IndexingPolicy поддерживает Order By. 
    
    **Следует учесть: это изменение может быть критическим**. 
    
    Если имеется существующий код, который предоставляет коллекции с пользовательской политикой индексирования, он должен быть обновлен и должен поддерживать новый класс IndexingPolicy. Если такой политики нет, это изменение на вас не влияет.

### <a name="a-name110110httpswwwnugetorgpackagesmicrosoftazuredocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
* Новые классы HashPartitionResolver и RangePartitionResolver и интерфейс IPartitionResolver для поддержки секционирования данных.
* Сериализация DataContract.
* Поддержка GUID в поставщике LINQ.
* Поддержка пользовательских функций в LINQ.

### <a name="a-name100100httpswwwnugetorgpackagesmicrosoftazuredocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
* Пакет SDK общей доступности.

## <a name="release--retirement-dates"></a>Даты выпуска и выбытия
Корпорация Майкрософт отправит уведомление минимум за **12 месяцев** до вывода пакета SDK из эксплуатации, чтобы обеспечить более плавный переход на новую или поддерживаемую версию.

Новые функции, возможности и оптимизации добавляются только в текущую версию пакета SDK, поэтому рекомендуется как можно раньше обновлять пакет SDK до последней версии. 

Любые запросы к DocumentDB с помощью выведенного из эксплуатации SDK будут отклоняться службой.

<br/>

| Version (версия) | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [1.13.1](#1.13.1) |29 марта 2017 г. |--- |
| [1.13.0](#1.13.0) |24 марта 2017 г. |--- |
| [1.12.2](#1.12.2) |20 марта 2017 г. |--- |
| [1.12.1](#1.12.1) |14 марта 2017 г. |--- |
| [1.12.0](#1.12.0) |15 февраля 2017 г. |--- |
| [1.11.4](#1.11.4) |6 февраля 2017 г. |--- |
| [1.11.3](#1.11.3) |26 января 2017 г. |--- |
| [1.11.1](#1.11.1) |21 декабря 2016 г. |--- |
| [1.11.0](#1.11.0) |8 декабря 2016 г. |--- |
| [1.10.0](#1.10.0) |27 сентября 2016 г. |--- |
| [1.9.5](#1.9.5) |1 сентября 2016 г. |--- |
| [1.9.4](#1.9.4) |24 августа 2016 г. |--- |
| [1.9.3](#1.9.3) |15 августа 2016 г. |--- |
| [1.9.2](#1.9.2) |23 июля 2016 г. |--- |
| [1.8.0](#1.8.0) |14 июня 2016 г. |--- |
| [1.7.1](#1.7.1) |6 мая 2016 г. |--- |
| [1.7.0](#1.7.0) |26 апреля 2016 г. |--- |
| [1.6.3](#1.6.3) |8 апреля 2016 г. |--- |
| [1.6.2](#1.6.2) |29 марта 2016 г. |--- |
| [1.5.3](#1.5.3) |19 февраля 2016 г. |--- |
| [1.5.2](#1.5.2) |14 декабря 2015 г. |--- |
| [1.5.1](#1.5.1) |23 ноября 2015 г. |--- |
| [1.5.0](#1.5.0) |5 октября 2015 г. |--- |
| [1.4.1](#1.4.1) |25 августа 2015 г. |--- |
| [1.4.0](#1.4.0) |13 августа 2015 г. |--- |
| [1.3.0](#1.3.0) |5 августа 2015 г. |--- |
| [1.2.0](#1.2.0) |6 июля 2015 г. |--- |
| [1.1.0](#1.1.0) |30 апреля 2015 г. |--- |
| [1.0.0](#1.0.0) |8 апреля 2015 г. |--- |


## <a name="faq"></a>Часто задаваемые вопросы
[!INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Дополнительные материалы
Дополнительные сведения о DocumentDB см. на странице документации по службе [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/). 


