---
title: API и пакеты SDK для DocumentDB .NET | Microsoft Docs
description: Сведения об API и пакетах SDK для .NET, в том числе даты выхода, даты выбытия и изменения, внесенные в каждую версию пакета SDK для DocumentDB .NET.
services: documentdb
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun

ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/27/2016
ms.author: rnagpal

---
# Интерфейсы API и пакеты SDK для DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://go.microsoft.com/fwlink/?LinkId=402413)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

## API и пакеты SDK для DocumentDB .NET
<table>

<tr><td>**Скачать пакет SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>

<tr><td>**Документация по интерфейсам API**</td><td>[Справочная документация по API .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>

<tr><td>**Примеры**</td><td>[Примеры DocumentDB .NET](documentdb-dotnet-samples.md)</td></tr>

<tr><td>**Начало работы**</td><td>[Начало работы с пакетом SDK для DocumentDB .NET](documentdb-get-started.md)</td></tr>

<tr><td>**Руководство по работе веб-приложений**</td><td>[Руководство по ASP.NET MVC. Разработка веб-приложения с использованием DocumentDB](documentdb-dotnet-application.md)</td></tr>

<tr><td>**Текущая поддерживаемая платформа**</td><td>[Microsoft .NET Framework&#160;4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## Заметки о выпуске
> [!IMPORTANT]
> При выполнении запросов к секционированным коллекциям может порождаться исключение System.NotSupportedException. Во избежание этой ошибки в окне свойств проекта на вкладке "Сборка" снимите флажок "Предпочтительно 32-разр.".
> 
> 

### <a name="1.10.0"/>[1\.10.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.10.0)
* Добавлена поддержка прямое подключения для секционированных коллекций.
* Повышена производительность для уровня согласованности с ограниченным устареванием.
* Добавлена поддержка LINQ для StringEnumConverter, IsoDateTimeConverter и UnixDateTimeConverter при преобразовании предикатов.
* Исправлены различные ошибки в пакете SDK.

### <a name="1.9.5"/>[1\.9.5](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.5)
* Устранена проблема, которая вызвала следующее исключение NotFoundException: "The read session is not available for the input session token" (Сеанс чтения для входного маркера сеанса недоступен). Это исключение иногда происходило при выполнении запроса к региону чтения геораспределенной учетной записи.
* В классе ResourceResponse предоставляется свойство ResponseStream, которое обеспечивает прямой доступ к базовому потоку из ответа.

### <a name="1.9.4"/>[1\.9.4](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.4)
* Классы ResourceResponse, FeedResponse, StoredProcedureResponse и MediaResponse изменены для реализации соответствующего открытого интерфейса. Теперь их можно макетировать для развертывания через тестирование (TDD).
* Устранена проблема, из-за которой заголовок ключа секции имел неправильный формат при использовании пользовательского объекта JsonSerializerSettings для сериализации данных.

### <a name="1.9.3"/>[1\.9.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.3)
* Устранена проблема, из-за которой длительные запросы завершались сбоем и отображалось сообщение об ошибке "Authorization token is not valid at the current time" (Маркер авторизации в настоящее время недействителен).
* Устранена проблема, из-за которой исходный параметр SqlParameterCollection удалялся из запросов top/order-by между секциями.

### <a name="1.9.2"/>[1\.9.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.2)
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

### <a name="1.8.0"/>[1\.8.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.8.0)
* Добавлена поддержка учетных записей базы данных в нескольких регионах.
* Добавлена поддержка повтора отрегулированных запросов. Пользователь может задать число повторов и максимальное время ожидания, настроив свойство ConnectionPolicy.RetryOptions.
* Добавлен новый интерфейс IDocumentClient, который определяет сигнатуры всех свойств и методов DocumenClient. В ходе этого изменения были также изменены методы расширения, которые создают IQueryable и IOrderedQueryable для методов в самом классе DocumentClient.
* Добавлен параметр конфигурации, позволяющий указать ServicePoint.ConnectionLimit для заданного универсального кода ресурса (URI) конечной точки DocumentDB. Используйте ConnectionPolicy.MaxConnectionLimit, чтобы изменить значение по умолчанию, которое равно 50.
* Не рекомендуется использовать IPartitionResolver и его реализацию. Теперь поддержка IPartitionResolver устарела. Рекомендуется использовать секционированные коллекции, чтобы увеличить возможности хранилища и пропускную способность.

### <a name="1.7.1"/>[1\.7.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.1)
* Добавлена перегрузка метода ExecuteStoredProcedureAsync на основе универсального кода ресурса (URI), принимающего RequestOptions в качестве параметра.

### <a name="1.7.0"/>[1\.7.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.0)
* Добавлена поддержка срока жизни для документов.

### <a name="1.6.3"/>[1\.6.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.3)
* Исправлена ошибка в упаковке Nuget пакета SDK для .NET, связанная с его упаковкой в составе решения облачной службы Azure.

### <a name="1.6.2"/>[1\.6.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.2)
* Реализованы [секционированные коллекции](documentdb-partition-data.md) и [пользовательские уровни производительности](documentdb-performance-levels.md).

### <a name="1.5.3"/>[1\.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
* **[Исправлено]**. Запрос к конечной точке DocumentDB приводит к появлению исключения "System.Net.Http.HttpRequestException: произошла ошибка при копировании содержимого в поток".

### <a name="1.5.2"/>[1\.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
* Расширенная поддержка LINQ, включая новые операторы разбиения по страницам, условные выражения и сравнение диапазонов.
  * Оператор Take, позволяющий выполнить операцию SELECT TOP в LINQ.
  * Оператор CompareTo, позволяющий сравнивать диапазоны строк.
  * Условный оператор (?) и оператор объединения (??).
* **[Исправлено]**. Появление исключения ArgumentOutOfRangeException при комбинировании проекции Model с Where-In в запросе LINQ. [№81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="1.5.1"/>[1\.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
* **[Исправлено]** Если операция Select не является последней, поставщик LINQ не использовал проекции и неверно определял операцию SELECT *. [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58).

### <a name="1.5.0"/>[1\.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
* Реализован метод Upsert, добавлен метод UpsertXXXAsync.
* Повышение производительности для всех запросов.
* Поддержка поставщиком LINQ условных операций, функций объединения и методов CompareTo для строковых значений.
* **[Исправлено]** Поставщик LINQ: реализация метода Contains для списков для создания таких же запросов SQL, как для IEnumerable и массивов.
* **[Исправлено]** BackoffRetryUtility повторно использует то же сообщение HttpRequestMessage вместо создания нового при повторной попытке.
* **[Устарело]** UriFactory.CreateCollection: теперь следует использовать UriFactory.CreateDocumentCollection.

### <a name="1.4.1"/>[1\.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
* **[Исправлено]** Проблемы локализации при использовании региональных параметров, отличных от en, например nl-NL и т. д.

### <a name="1.4.0"/>[1\.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
* Маршрутизация на основе идентификатора.
  * Новый вспомогательный класс UriFactory для создания ссылок на ресурсы на основании идентификатора.
  * Новые перегрузки в классе DocumentClient, принимающие URI в качестве параметров.
* Для геопространственных данных в LINQ добавлены методы IsValid() и IsValidDetailed().
* Улучшена поддержка поставщика LINQ.
  * **Математические функции:** Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan, Truncate.
  * **Строковые функции:** Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper.
  * **Функции массивов:** Concat, Contains, Count.
  * Оператор **IN**.

### <a name="1.3.0"/>[1\.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
* Добавлена поддержка изменения политики индексации.
  * Новый метод ReplaceDocumentCollectionAsync в классе DocumentClient.
  * Новое свойство IndexTransformationProgress в ResourceResponse<T> для отслеживания процесса изменения политики индексирования (в процентах).
  * Свойство DocumentCollection.IndexingPolicy теперь является изменяемым.
* Добавлена поддержка индексирования и создания запросов к пространственным типам данных.
  * Новое пространство имен Microsoft.Azure.Documents.Spatial для сериализации и десериализации пространственных типов, таких как Point и Polygon.
  * Новый класс SpatialIndex для индексирования данных GeoJSON, хранящихся в DocumentDB.
* **[Исправлено]** Из выражения LINQ [#38](https://github.com/Azure/azure-documentdb-net/issues/38) создавался неверный SQL-запрос.

### <a name="1.2.0"/>[1\.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
* Зависимость от Newtonsoft.Json 5.0.7.
* Изменения для поддержки Order By.
  
  * Поставщик LINQ поддерживает OrderBy() или OrderByDescending().
  * IndexingPolicy поддерживает Order By.
    
        **NB: Possible breaking change** 
    
        If you have existing code that provisions collections with a custom indexing policy, then your existing code will need to be updated to support the new IndexingPolicy class. If you have no custom indexing policy, then this change does not affect you.

### <a name="1.1.0"/>[1\.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
* Новые классы HashPartitionResolver и RangePartitionResolver и интерфейс IPartitionResolver для поддержки секционирования данных.
* Сериализация DataContract.
* Поддержка GUID в поставщике LINQ.
* Поддержка пользовательских функций в LINQ.

### <a name="1.0.0"/>[1\.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
* Пакет SDK общей доступности.

> [!NOTE]
> Изменилось название пакета NuGet при переходе от предварительной версии к общедоступной. Имя изменилось с **Microsoft.Azure.Documents.Client** на **Microsoft.Azure.DocumentDB**. <br/>
> 
> 

### <a name="0.9.x-preview"/>[0\.9.x-preview](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
* [Устарело] Пакеты SDK для предварительной версии.

## Даты выпуска и выбытия
Корпорация Майкрософт отправит уведомление минимум за **12 месяцев** до вывода пакета SDK из эксплуатации, чтобы обеспечить более плавный переход на новую или поддерживаемую версию.

Новые функции, возможности и оптимизации добавляются только в текущую версию пакета SDK, поэтому рекомендуется как можно раньше обновлять пакет SDK до последней версии.

Любые запросы к DocumentDB с помощью выведенного из эксплуатации SDK будут отклоняться службой.

> [!WARNING]
> Все версии пакета SDK для Azure DocumentDB для .NET версии ниже **1.0.0** будут удалены **29 февраля 2016 г.**
> 
> 

.<br/>

| Version (версия) | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [1\.10.0](#1.10.0) |27 сентября 2016 г. |--- |
| [1\.9.5](#1.9.5) |1 сентября 2016 г. |--- |
| [1\.9.4](#1.9.4) |24 августа 2016 г. |--- |
| [1\.9.3](#1.9.3) |15 августа 2016 г. |--- |
| [1\.9.2](#1.9.2) |23 июля 2016 г. |--- |
| 1.9.1 |не рекомендуется |--- |
| 1.9.0 |не рекомендуется |--- |
| [1\.8.0](#1.8.0) |14 июня 2016 г. |--- |
| [1\.7.1](#1.7.1) |6 мая 2016 г. |--- |
| [1\.7.0](#1.7.0) |26 апреля 2016 г. |--- |
| [1\.6.3](#1.6.3) |8 апреля 2016 г. |--- |
| [1\.6.2](#1.6.2) |29 марта 2016 г. |--- |
| [1\.5.3](#1.5.3) |19 февраля 2016 г. |--- |
| [1\.5.2](#1.5.2) |14 декабря 2015 г. |--- |
| [1\.5.1](#1.5.1) |23 ноября 2015 г. |--- |
| [1\.5.0](#1.5.0) |5 октября 2015 г. |--- |
| [1\.4.1](#1.4.1) |25 августа 2015 г. |--- |
| [1\.4.0](#1.4.0) |13 августа 2015 г. |--- |
| [1\.3.0](#1.3.0) |5 августа 2015 г. |--- |
| [1\.2.0](#1.2.0) |6 июля 2015 г. |--- |
| [1\.1.0](#1.1.0) |30 апреля 2015 г. |--- |
| [1\.0.0](#1.0.0) |8 апреля 2015 г. |--- |
| [0\.9.3-prelease](#0.9.x-preview) |12 марта 2015 г. |29 февраля 2016 г. |
| [0\.9.2-prelease](#0.9.x-preview) |Январь 2015 г. |29 февраля 2016 г. |
| [.9.1-prelease](#0.9.x-preview) |13 октября 2014 г. |29 февраля 2016 г. |
| [0\.9.0-prelease](#0.9.x-preview) |21 августа 2014 г. |29 февраля 2016 г. |

## Часто задаваемые вопросы
[!INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## См. также
Дополнительные сведения о DocumentDB см. на странице документации по [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0928_2016-->