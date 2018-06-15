---
title: API-интерфейс, пакет SDK и ресурсы для SQL .NET Core (Azure Cosmos DB) | Документация Майкрософт
description: Сведения об API-интерфейсе и пакете SDK для SQL .NET Core, в том числе даты выхода, даты прекращения использования и внесенные изменения по каждой версии пакета SDK .NET Core для Azure Cosmos DB.
services: cosmos-db
author: rnagpal
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c62869d1ec0dca1859972e817fe1e5e2c1f4c1eb
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34797868"
---
# <a name="azure-cosmos-db-net-core-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB: заметки о выпуске и материалы по пакету SDK для .NET Core для API-интерфейса SQL
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Веб-канал изменений в .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Поставщик ресурсов REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor — .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor — Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**Скачивание пакета SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**Документация по API**</td><td>[Справочная документация по API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**Примеры**</td><td>[Примеры кода для .NET](sql-api-dotnet-samples.md)</td></tr>

<tr><td>**Начало работы**</td><td>[Azure Cosmos DB. Приступая к работе с API DocumentDB и .NET Core](sql-api-dotnetcore-get-started.md)</td></tr>

<tr><td>**Учебник по веб-приложениям**</td><td>[Руководство по ASP.NET MVC. Разработка веб-приложений в Azure Cosmos DB](sql-api-dotnet-application.md)</td></tr>

<tr><td>**Текущая поддерживаемая платформа**</td><td>[.NET Standard 1.6 и .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>Заметки о выпуске

Пакет SDK .NET Core для Azure Cosmos DB функционально полностью эквивалентен последней версии [пакета SDK .NET для Azure Cosmos DB](sql-api-sdk-dotnet.md).

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0

* Добавлено свойство ConsistencyLevel в FeedOptions.
* Добавлен класс JsonSerializerSettings в RequestOptions и FeedOptions.
* Добавлено свойство EnableReadRequestsFallback для ConnectionPolicy.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1

* Исправлено исключение KeyNotFoundException в сложных случаях для запросов ORDER BY между секциями.
* Исправлена ошибка, из-за которой не учитывался атрибут JsonPropery в выбранном предложении запросов LINQ.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* Исправлена ошибка, которая происходила для определенного состояния гонки, в результате чего при использовании модели согласованности на уровне сеанса периодически возникали ошибки Microsoft.Azure.Documents.NotFoundException: The read session is not available for the input session token (Сеанс чтения для входного маркера сеанса недоступен).

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1

* Исправлена регрессия, где FeedOptions.MaxItemCount = -1 вызвало исключение System.ArithmeticException: размер страницы является отрицательным значением.
* Добавлена новая функция ToString() в QueryMetrics.
* Предоставлена статистика по секциям для считывания коллекций.
* Добавлено свойство PartitionKey в ChangeFeedOptions.
* Исправлены незначительные ошибки.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
 
 * Добавлена возможность указывать уникальные индексы для документов с помощью применения свойства UniqueKeyPolicy к коллекции DocumentCollection.
 * Исправлена ошибка, когда пользовательские параметры JsonSerializer не учитывались при выполнении некоторых запросов и хранимых процедур.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
 
 * Изменена фирменная символика с Azure DocumentDB на Azure Cosmos DB в справочной документации по API, сведениях о метаданных в сборках и пакете NuGet. 
 * Предоставлены данные диагностики и сведения о задержке ответов на запросы, отправленные в режиме прямого соединения. Имена свойств: RequestDiagnosticsString и RequestLatency в классе ResourceResponse.
 * Для этой версии пакета SDK требуется последняя версия эмулятора Azure Cosmos DB. Ее можно скачать по адресу https://aka.ms/cosmosdb-emulator.
 
### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Добавлено несколько исправлений и улучшений для повышения надежности.

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1 

* Внутренние изменения, связанные с поддержкой [Microsoft.Azure.Graphs](https://docs.microsoft.com/azure/cosmos-db/graph-sdk-dotnet)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0 

* Добавлена поддержка PartitionKeyRangeId как FeedOption для ограничения области результатов запроса определенным диапазоном ключа секции. 
* Добавлена поддержка StartTime как ChangeFeedOption для поиска изменений после указанного периода. 

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

*   Устранена проблема в классе JsonSerializable, которая могла порождать исключение переполнения стека.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

*   Добавлена поддержка для указания пользовательских параметров JsonSerializerSettings при создании экземпляра [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   Поддержка .NET Standard 1.5 в качестве одной из требуемых версий .NET Framework.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   Устранена проблема, возникающая на компьютерах под управлением 64-разрядной ОС без поддержки инструкций SSE4, которая приводила к исключению SEHException при выполнении запросов Azure Cosmos DB.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   Добавлена поддержка нового уровня согласованности с именем ConsistentPrefix.
*   Добавлена поддержка запроса метрик отдельных секций.
*   Добавлена поддержка ограничения размера маркера продолжения запросов.
*   Добавлена поддержка более подробной трассировки невыполненных запросов.
*   Внесены некоторые улучшения производительности в пакет SDK.

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* Устранена проблема, когда не учитывалось значение PartitionKey в FeedOptions для статистических запросов.
* Устранена проблема, связанная с прозрачной обработкой управления при промежуточном выполнении запроса Order By между разделами.

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* Исправлена проблема, вызывавшая взаимоблокировки в некоторых асинхронных интерфейсах API при использовании в контексте ASP.NET.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Исправления для повышения устойчивости пакета SDK к автоматической отработке отказа при определенных условиях.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Исправление ошибки, приводившей к исключению WebException: "Удаленное имя не удалось разрешить".
* Добавлена поддержка непосредственного считывания типизированного документа путем добавления новых перегрузок в API ReadDocumentAsync.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Добавлена поддержка LINQ для статистических запросов (COUNT, MIN, MAX, SUM и AVG).
* Исправление ошибки утечки памяти для объекта ConnectionPolicy, вызываемой использованием обработчика событий.
* Исправление ошибки, из-за которой метод UpsertAttachmentAsync не работал, когда использовался тег ETag.
* Исправление ошибки, из-за которой продолжение запросов orderBy между секциями не работало при сортировке по полю строки.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Добавлена поддержка статистических запросов (COUNT, MIN, MAX, SUM и AVG). Дополнительные сведения см. в разделе [Статистические функции](sql-api-sql-query.md#Aggregates).
* Минимальная пропускная способность секционированных коллекций снижена с 10 100 ЕЗ/с до 2500 ЕЗ/с.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

Пакет SDK .NET Core для Azure Cosmos DB позволяет создавать быстрые кроссплатформенные приложения [ASP.NET Core](https://www.asp.net/core) и [.NET Core](https://www.microsoft.com/net/core#windows) для Windows, Mac и Linux. Последний выпуск пакета SDK .NET Core для Azure Cosmos DB полностью совместим с [Хamarin](https://www.xamarin.com) и пригоден для создания приложений для iOS, Android и Mono (Linux).  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview

Пакет SDK .NET Core для Azure Cosmos DB (предварительная версия) позволяет создавать быстрые кроссплатформенные приложения [ASP.NET Core](https://www.asp.net/core) и [.NET Core](https://www.microsoft.com/net/core#windows) для Windows, Mac и Linux.

Пакет SDK .NET Core для Azure Cosmos DB (предварительная версия) функционально полностью эквивалентен последней версии [пакета SDK .NET для Azure Cosmos DB](sql-api-sdk-dotnet.md) и поддерживает следующие возможности:
* все [режимы подключения](performance-tips.md#networking): режим шлюза, прямые TCP- и HTTP-подключения; 
* все [уровни согласованности](consistency-levels.md): строгая, ограниченное устаревание, согласованность сеанса, окончательная;
* [секционированные коллекции](partition-data.md); 
* [георепликация данных и межрегиональные учетные записи баз данных](distribute-data-globally.md).

Если у вас возникли вопросы об этом пакете SDK, опубликуйте их на форуме сайта [StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb) или сообщите о проблеме в [репозитории GitHub](https://github.com/Azure/azure-documentdb-dotnet/issues). 

## <a name="release--retirement-dates"></a>Даты выпуска и выбытия

| Version (версия) | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [1.9.1](#1.9.1) |9 марта 2018 г. |--- |
| [1.8.2](#1.8.2) |21 февраля 2018 г. |--- |
| [1.8.1](#1.8.1) |5 февраля 2018 г. |--- |
| [1.7.1](#1.7.1) |16 ноября 2017 г. |--- |
| [1.7.0](#1.7.0) |10 ноября 2017 г. |--- |
| [1.6.0](#1.6.0) |17 октября 2017 г. |--- |
| [1.5.1](#1.5.1) |2 октября 2017 г. |--- |
| [1.5.0](#1.5.0) |10 августа 2017 г. |--- | 
| [1.4.1](#1.4.1) |7 августа 2017 г. |--- |
| [1.4.0](#1.4.0) |2 августа 2017 г. |--- |
| [1.3.2](#1.3.2) |12 июня 2017 г. |--- |
| [1.3.1](#1.3.1) |23 мая 2017 г. |--- |
| [1.3.0](#1.3.0) |10 мая 2017 г. |--- |
| [1.2.2](#1.2.2) |19 апреля 2017 г. |--- |
| [1.2.1](#1.2.1) |29 марта 2017 г. |--- |
| [1.2.0](#1.2.0) |25 марта 2017 г. |--- |
| [1.1.2](#1.1.2) |20 марта 2017 г. |--- |
| [1.1.1](#1.1.1) |14 марта 2017 г. |--- |
| [1.1.0](#1.1.0) |16 февраля 2017 г. |--- |
| [1.0.0](#1.0.0) |21 декабря 2016 г. |--- |
| [0.1.0-preview](#0.1.0-preview) |15 ноября 2016 г. |31 декабря 2016 г. |

## <a name="see-also"></a>См. также
Дополнительные сведения о Cosmos DB см. на странице службы [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

