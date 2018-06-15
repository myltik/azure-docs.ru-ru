---
title: Секционирование и масштабирование в базе данных Azure Cosmos DB | Документация Майкрософт
description: Сведения о работе секционирования в базе данных Azure Cosmos DB, настройке секционирования и ключей секций, а также о выборе подходящего ключа секции для вашего приложения.
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2017
ms.author: rafats
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7e3c15ce201cee88d400b9d5fb9272b584293a2f
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34797438"
---
# <a name="partitioning-in-azure-cosmos-db-using-the-sql-api"></a>Секционирование в Azure Cosmos DB с помощью API SQL

[База данных Microsoft Azure Cosmos DB](../cosmos-db/introduction.md) — это глобально распределенная, многомодельная служба базы данных, созданная для обеспечения высокой производительности и прогнозируемости, а также легкой масштабируемости по мере расширения вашего приложения. 

В этой статье описаны способы работы с секционированием контейнеров Cosmos DB с помощью API SQL. Обзор концепций и рекомендации для секционирования с использованием любого API базы данных Azure Cosmos DB см. [в этой статье](../cosmos-db/partition-data.md). 

Чтобы начать работу с кодом, скачайте проект с [Github](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

После прочтения этой статьи вы сможете ответить на следующие вопросы:   

* Как работает секционирование в Azure Cosmos DB?
* Как настроить секционирование в Azure Cosmos DB
* Что такое ключи секций и как выбрать правильный ключ секции для моего приложения?

Чтобы начать работу с кодом, скачайте проект со страницы [примера драйвера для тестирования производительности Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

<!-- placeholder until we have a permanent solution-->
<a name="partition-keys"></a>
<a name="single-partition-and-partitioned-collections"></a>
<a name="migrating-from-single-partition"></a>

## <a name="partition-keys"></a>Ключи секции

В API SQL укажите определение ключа секции в формате пути к JSON-файлу. В следующей таблице приведены примеры определений для ключей секции и соответствующие значения. Ключ секции указывается в виде пути, например `/department` представляет свойство отдела. 

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Ключ секции</strong></p></td>
            <td valign="top"><p><strong>Описание</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/department</p></td>
            <td valign="top"><p>Соответствует значению doc.department, где doc — это элемент.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/properties/name</p></td>
            <td valign="top"><p>Соответствует значению doc.properties.name, где doc — это элемент (вложенное свойство).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/id</p></td>
            <td valign="top"><p>Соответствует значению doc.id (идентификатор и ключ секции — это одно и то же свойство).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/"имя отдела"</p></td>
            <td valign="top"><p>Соответствует значению [doc.department], где doc — это элемент.</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE]
> Синтаксис ключа секции аналогичен политике индексации. Основное отличие состоит в том, что путь соответствует свойству, а не значению, т. е. в конце нет подстановочного знака. Например, для индексации значений в отделе используется /department/?, а в качестве определения ключа секции — /department. Ключ секции индексируется неявно и не может быть исключен из индексирования с помощью переопределений политики индексирования.
> 
> 

Давайте рассмотрим, как выбор ключа секции влияет на производительность приложения.

## <a name="working-with-the-azure-cosmos-db-sdks"></a>Работа с пакетами SDK для базы данных Azure Cosmos DB
В Azure Cosmos DB добавлена поддержка автоматического секционирования с использованием [REST API версии 2015-12-16](/rest/api/cosmos-db/). Для создания секционированных контейнеров необходимо скачать пакет SDK версии 1.6.0 или более поздней для одной из поддерживаемых платформ SDK (.NET, Node.js, Java, Python и MongoDB). 

### <a name="creating-containers"></a>Создание контейнеров
В приведенном ниже примере показан фрагмент кода .NET для создания контейнеров, предназначенных для хранения данных телеметрии устройств, которой выделяется пропускная способность в 20 000 единиц запроса в секунду. Пакет SDK устанавливает значение OfferThroughput (которое в свою очередь задает заголовок запроса `x-ms-offer-throughput` в REST API). Здесь вы задаете `/deviceId` в качестве ключа секции. Выбранный ключ секции сохраняется вместе с остальными метаданными контейнера, такими как имя и политики индексирования.

Для этого примера вы используете `deviceId`, исходя из следующих данных: (а) большое количество устройств позволяет распределить операции записи равномерно между секциями, а значит успешно масштабировать базу данных для приема больших объемов данных; (б) многие из запросов, например получение последних показаний устройства, относятся к одному deviceId и могут быть получены только из одной секции.

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

// Container for device telemetry. Here the property deviceId will be used as the partition key to 
// spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
// sorting against any number or string property.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

Этот метод выполняет вызов REST API в Cosmos DB, а служба подготавливает число секций, основываясь на запрошенной пропускной способности. Пропускную способность контейнера или набора контейнеров можно изменять при повышении требований к производительности. 

### <a name="reading-and-writing-items"></a>Чтение и запись элементов
Пора перейти к вставке данных в Cosmos DB. Ниже приведен пример класса, содержащего показание устройства, и вызов CreateDocumentAsync для добавления нового показания устройства в контейнер. Ниже приведен пример кода, в котором используется API-интерфейс SQL.

```csharp
public class DeviceReading
{
    [JsonProperty("id")]
    public string Id;

    [JsonProperty("deviceId")]
    public string DeviceId;

    [JsonConverter(typeof(IsoDateTimeConverter))]
    [JsonProperty("readingTime")]
    public DateTime ReadingTime;

    [JsonProperty("metricType")]
    public string MetricType;

    [JsonProperty("unit")]
    public string Unit;

    [JsonProperty("metricValue")]
    public double MetricValue;
  }

// Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new DeviceReading
    {
        Id = "XMS-001-FE24C",
        DeviceId = "XMS-0001",
        MetricType = "Temperature",
        MetricValue = 105.00,
        Unit = "Fahrenheit",
        ReadingTime = DateTime.UtcNow
    });
```

Давайте прочитаем элемент по ключу секции и идентификатору, обновим его, а затем удалим с помощью ключа секции и идентификатора. Операции чтения содержат значение PartitionKey (соответствующее заголовку запроса `x-ms-documentdb-partitionkey` в REST API).

```csharp
// Read document. Needs the partition key and the ID to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;

// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);

// Delete document. Needs partition key
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="querying-partitioned-containers"></a>Запрос секционированных контейнеров
При запросе данных из секционированных контейнеров Azure Cosmos DB автоматически направляет запрос в секции, соответствующие значению ключа секции, указанному в фильтре (если таковой имеется). Например, этот запрос направляется только в секцию, которая содержит ключ секции XMS-0001.

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```
    
Следующий запрос не имеет фильтра ключа секции (DeviceId) и "прочесывает" все секции, где он выполняется с использованием индекса секции. Чтобы пакет SDK распределял запросы по секциям, необходимо указать параметр EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` в REST API).

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Cosmos DB поддерживает [агрегатные функции](sql-api-sql-query.md#Aggregates) `COUNT`, `MIN`, `MAX` и `AVG` с секционированными контейнерами при использовании пакета SDK для SQL версии 1.12.0 и выше. Запросы должны содержать один статистический оператор и одно значение в проекции.

### <a name="parallel-query-execution"></a>Параллельное выполнение запросов
Пакеты SDK для Cosmos DB версии 1.9.0 и более поздних версий поддерживают параллельное выполнение запросов, что позволяет выполнять запросы с низким уровнем задержки к секционированным коллекциям, даже если число секций в них очень велико. Например, следующий запрос настроен на выполнение параллельно по секциям.

```csharp
// Cross-partition Order By Queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
Вы можете управлять параллельным выполнением запросов, регулируя следующие параметры.

* Параметр `MaxDegreeOfParallelism` позволяет управлять степенью параллелизма, то есть устанавливать максимальное число одновременных сетевых подключений к секциям контейнера. Если для параметра установить значение -1, степень параллелизма будет регулироваться пакетом SDK. Если параметр `MaxDegreeOfParallelism` не указан или имеет значение 0 (значение по умолчанию), к секциям контейнера будет установлено одно сетевое подключение.
* Параметр `MaxBufferedItemCount` обеспечивает баланс между задержкой запросов и использованием памяти на стороне клиента. Если этот параметр не указан или имеет значение -1, то число помещенных в буфер элементов при параллельном выполнении запросов будет регулироваться пакетом SDK.

При неизменном состоянии коллекции параллельный запрос будет возвращать результаты в том же порядке, что и при последовательном выполнении. При выполнении межсекционного запроса, включающего в себя сортировку (ORDER BY и/или TOP), пакет SDK для Azure Cosmos DB параллельно выполняет запрос между секциями и объединяет частично отсортированные результаты на стороне клиента для получения глобально упорядоченных результатов.

### <a name="executing-stored-procedures"></a>Выполнение хранимых процедур
Кроме того, вы можете выполнять атомарные транзакции с документами с одинаковым идентификатором устройства, например, если вы храните статистические выражения или последнее состояние устройства в одном элементе. 

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```
   
В следующем разделе вы узнаете, как перейти на секционированные контейнеры с односекционных контейнеров.

## <a name="next-steps"></a>Дополнительная информация
В этой статье вы получили обзорные сведения о работе с секционированием контейнеров Azure Cosmos DB с помощью API SQL. Обзор концепций и рекомендации для секционирования с использованием любого API базы данных Azure Cosmos DB см. [в этой статье](../cosmos-db/partition-data.md). 

* Выполняйте проверку масштабирования и производительности с помощью базы данных Azure Cosmos DB. Пример см. в статье [Проверка производительности и масштабирования с помощью Azure DocumentDB](performance-testing.md).
* Приступите к созданию кода с помощью [пакетов SDK](sql-api-sdk-dotnet.md) или [REST API](/rest/api/cosmos-db/).
* Дополнительные сведения о подготовленной пропускной способности в базе данных Azure Cosmos DB см. в [этой статье](request-units.md).

