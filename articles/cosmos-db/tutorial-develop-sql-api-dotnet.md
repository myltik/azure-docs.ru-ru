---
title: Разработка с помощью API SQL в Azure Cosmos DB на .NET | Документация Майкрософт
description: Сведения о разработке с помощью API SQL службы Azure Cosmos DB на языке .NET
services: cosmos-db
documentationcenter: ''
author: rafats
manager: kfile
editor: ''
tags: ''
ms.assetid: ''
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 05/10/2017
ms.author: rafats
ms.custom: mvc
ms.openlocfilehash: a6ed74de159593003e8a18daefce2eb9a5945481
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-develop-with-the-sql-api-in-net"></a>Azure Cosmos DB. Разработка с помощью API SQL на .NET

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ — значение" и базы данных графов, используя преимущества возможностей глобального распределения и горизонтального масштабирования базы данных Azure Cosmos DB.

В этом руководстве показано, как создать учетную запись Azure Cosmos DB на портале Azure, а затем базу данных документов и коллекцию с [ключом секции](sql-api-partition-data.md#partition-keys) с помощью [API SQL для .NET](sql-api-introduction.md). Определив ключ секции при создании коллекции, вы сможете легко масштабировать приложение по мере увеличения объема данных.

В этом руководстве описано, как выполнить следующие задачи с помощью [API SQL для .NET](sql-api-sdk-dotnet.md):

> [!div class="checklist"]
> * создание учетной записи Azure Cosmos DB;
> * создание базы данных и коллекции с ключом секции;
> * создание документов JSON;
> * обновление документа;
> * запрос секционированных коллекций;
> * выполнение хранимых процедур;
> * Удаление документа
> * удаление базы данных.

## <a name="prerequisites"></a>предварительным требованиям
Чтобы начать работу, у вас должны быть следующие компоненты:

* Доступ к учетной записи Azure Cosmos DB

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

  Вы также можете использовать свою подписку Azure, зарегистрировав [бесплатную учетную запись Azure](https://azure.microsoft.com/free/). Затем вы сможете [создать учетную запись Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account).

* Если вы еще не установили Visual Studio 2017, вы можете скачать и использовать **бесплатный** [выпуск Community для Visual Studio 2017](https://www.visualstudio.com/downloads/). При установке Visual Studio необходимо включить возможность **разработки для Azure**.


> [!TIP]
> * Если вы хотите использовать эмулятор Azure Cosmos DB, [настройте его](local-emulator.md).
>
>


## <a id="SetupVS"></a>Настройка решения Visual Studio
1. Откройте **Visual Studio** у себя на компьютере.
2. В меню **Файл** выберите пункт **Создать**, а затем — **Проект**.
3. В диалоговом окне **Новый проект** выберите **Шаблоны** / **Visual C#** / **Консольное приложение (.NET Framework)**, а затем укажите имя проекта и нажмите кнопку **ОК**.
   ![Снимок экрана: диалоговое окно «Новый проект»](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-new-project-2.png)

4. В **обозревателе решений** щелкните правой кнопкой мыши новое консольное приложение (оно находится в решении Visual Studio). Затем щелкните **Управление пакетами NuGet...**

    ![Снимок экрана: меню «Проект», вызванное щелчком правой кнопки мыши](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-manage-nuget-pacakges.png)
5. На вкладке **NuGet** щелкните **Обзор** и в поле поиска введите **documentdb**.
<!---stopped here--->
6. В результатах найдите **Microsoft.Azure.DocumentDB** и нажмите кнопку **Установить**.
   Идентификатором пакета для клиентской библиотеки Azure Cosmos DB является [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB).
   ![Снимок экрана: меню NuGet для поиска пакета SDK для клиента Azure Cosmos DB](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-manage-nuget-pacakges-2.png)

    Если появится сообщение о просмотре изменений в решении, нажмите кнопку **ОК**. Если появится сообщение о принятии условий лицензионного соглашения, щелкните **Принимаю**.

## <a id="Connect"></a>Добавление ссылок в проект
На остальных шагах в этом руководстве приведены фрагменты кода API SQL, необходимые для создания и обновления ресурсов Azure Cosmos DB в проекте.

Сначала добавьте эти ссылки в приложение.
<!---These aren't added by default when you install the pkg?--->

```csharp
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

## <a id="add-references"></a>Подключение приложения

Добавьте эти две константы и переменную *client* в приложение.

```csharp
private const string EndpointUrl = "<your endpoint URL>";
private const string PrimaryKey = "<your primary key>";
private DocumentClient client;
```

Затем вернитесь на [портал Azure](https://portal.azure.com), чтобы получить URL-адрес конечной точки и первичный ключ. URL-адрес конечной точки и первичный ключ позволяют приложению предоставлять данные о расположении, в котором будет устанавливаться подключение, что делает подключение вашего приложения доверенным для Azure Cosmos DB.

На портале Azure перейдите к учетной записи Azure Cosmos DB. Выберите в меню слева **Ключи**, а затем — **Ключи записи-чтения**.

Скопируйте универсальный код ресурса (URI) с портала и вставьте его в параметр `<your endpoint URL>` в файле program.cs. Затем скопируйте на портале значение поля "Первичный ключ" и вставьте его в параметр `<your primary key>`. Не забудьте удалить `<` и `>` из значений.

![Снимок экрана портала Azure в ходе работы с руководством по NoSQL при создании консольного приложения C#. Показана учетная запись Azure Cosmos DB со следующими выделенными элементами: кнопка "Ключи" в разделе учетной записи Azure Cosmos DB и значения универсального кода ресурса и первичного ключа в разделе "Ключи".](./media/tutorial-develop-sql-api-dotnet/nosql-tutorial-keys.png)

## <a id="instantiate"></a>Создание экземпляра DocumentClient

Теперь создайте экземпляр **DocumentClient**.

```csharp
DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
```

## <a id="create-database"></a>Создание базы данных

Затем создайте [базу данных](sql-api-resources.md#databases) Azure Cosmos DB с помощью метода [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) или [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) класса **DocumentClient** из [пакета SDK .NET для SQL](sql-api-sdk-dotnet.md). База данных представляет собой логический контейнер для хранения документов JSON, разделенных между коллекциями.

```csharp
await client.CreateDatabaseAsync(new Database { Id = "db" });
```
## <a name="decide-on-a-partition-key"></a>Выбор ключа секции

Коллекции — это контейнеры для хранения документов. Они являются логическими ресурсами и [могут включать в себя одну или несколько физических секций](partition-data.md). [Ключ секции](sql-api-partition-data.md) — это свойство (или путь) в документах, используемое для распределения данных между серверами или секциями. Все документы с одинаковым ключом секции хранятся в одной секции.

Определение ключа секции — важное решение, которое необходимо принять до создания коллекции. Ключи секции — это свойства (или пути) в документах, которые могут использоваться Azure Cosmos DB для распределения данных между несколькими серверами или секциями. База данных Cosmos DB хэширует значение ключа секции и использует хэшированный результат, чтобы определить, в какой секции сохранить документ. Все документы с одинаковым ключом секции хранятся в одной секции. Ключи секции невозможно изменить после создания коллекции.

В этом руководстве нужно задать ключ секции `/deviceId`, чтобы все данные одного устройства хранились в одной секции. Чтобы база данных Cosmos DB могла балансировать нагрузку по мере увеличения объема данных и достичь полной пропускной способности коллекции, необходимо выбрать ключ секции, содержащий большое количество значений, каждое из которых используется примерно с одинаковой частотой.

Дополнительные сведения о секционировании см. в статье [Секционирование, ключи секции и масштабирование в DocumentDB](partition-data.md).

## <a id="CreateColl"></a>Создание коллекции

Теперь, когда ключ секции `/deviceId` задан, давайте создадим [коллекцию](sql-api-resources.md#collections) с помощью метода [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) или [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) класса **DocumentClient**. Коллекция представляет собой контейнер документов JSON и любую связанную с ними логику в виде приложения JavaScript.

> [!WARNING]
> Создание коллекции связано с ценовыми требованиями, так как для взаимодействия с базой данных Azure Cosmos DB для приложения резервируется пропускная способность. Дополнительные сведения см. на [странице цен](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

```csharp
// Collection for device telemetry. Here the JSON property deviceId is used  
// as the partition key to spread across partitions. Configured for 2500 RU/s  
// throughput and an indexing policy that supports sorting against any  
// number or string property. .
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 2500 });
```

Этот метод выполняет вызов REST API в Azure Cosmos DB, а служба подготавливает число секций, основываясь на запрошенной пропускной способности. Пропускную способность коллекции можно изменить, когда требуется повысить ее производительность с помощью пакета SDK или [портала Azure](set-throughput.md).

## <a id="CreateDoc"></a>Создание документов JSON
Вставьте несколько документов JSON в базу данных Azure Cosmos DB. Вы можете создать [документ](sql-api-resources.md#documents) с помощью метода [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx), который относится к классу **DocumentClient**. Документы относятся к пользовательскому (произвольному) содержимому JSON. Этот пример класса содержит показание устройства и вызов CreateDocumentAsync для добавления нового показания устройства в коллекцию.

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

// Create a document. Here the partition key is extracted
// as "XMS-0001" based on the collection definition
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
## <a name="read-data"></a>Считывание данных

Давайте считаем документ по ключу секции и идентификатору с помощью метода ReadDocumentAsync. Обратите внимание, что операции чтения включают в себя значение PartitionKey (соответствующее заголовку запроса `x-ms-documentdb-partitionkey` в REST API).

```csharp
// Read document. Needs the partition key and the Id to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"),
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;
```

## <a name="update-data"></a>Обновление данных

Теперь давайте обновим некоторые данные с помощью метода ReplaceDocumentAsync.

```csharp
// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"),
  reading);
```

## <a name="delete-data"></a>Удаление данных

Теперь удалите документ с помощью ключа секции и идентификатора, используя метод DeleteDocumentAsync.

```csharp
// Delete a document. The partition key is required.
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"),
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```
## <a name="query-partitioned-collections"></a>Запрос секционированных коллекций

При запросе данных из секционированных коллекций Azure Cosmos DB автоматически направляет запрос в секции, соответствующие значению ключа секции, указанному в фильтре (если таковой имеется). Например, этот запрос направляется только в секцию, которая содержит ключ секции XMS-0001.

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

Следующий запрос не имеет фильтра ключа секции (DeviceId) и "прочесывает" все секции, где он выполняется с использованием индекса секции. Обратите внимание, что требуется указать EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` в REST API), чтобы пакет SDK выполнил запрос в секциях.

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

## <a name="parallel-query-execution"></a>Параллельное выполнение запросов
Пакеты SDK для SQL в Azure Cosmos DB версии 1.9.0 и более поздних версий поддерживают параллельное выполнение запросов, что позволяет выполнять запросы с низким уровнем задержки к секционированным коллекциям, даже если число секций в них очень велико. Например, следующий запрос настроен на выполнение параллельно по секциям.

```csharp
// Cross-partition Order By queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

Вы можете управлять параллельным выполнением запросов, регулируя следующие параметры.

* Параметр `MaxDegreeOfParallelism` позволяет управлять степенью параллелизма, т. е. устанавливать максимальное число одновременных сетевых подключений к секциям коллекции. Если установить для параметра значение -1, степень параллелизма будет регулироваться пакетом SDK. Если параметр `MaxDegreeOfParallelism` не указан или имеет значение 0 (значение по умолчанию), к секциям коллекции будет установлено одно сетевое подключение.
* Параметр `MaxBufferedItemCount` обеспечивает баланс между задержкой запросов и использованием памяти на стороне клиента. Если пропустить этот параметр или задать значение -1, число буферизованных элементов при параллельном выполнении запросов будет регулироваться пакетом SDK.

При неизменном состоянии коллекции параллельный запрос будет возвращать результаты в том же порядке, что и при последовательном выполнении. При выполнении межсекционного запроса, включающего в себя сортировку (ORDER BY и/или TOP), пакет SDK для SQL параллельно выполняет запрос между секциями и объединяет частично отсортированные результаты на стороне клиента для получения глобально упорядоченных результатов.

## <a name="execute-stored-procedures"></a>Выполнение хранимых процедур
Наконец, можно выполнять атомарные транзакции с документами с одинаковым идентификатором устройства, например, если вы храните статистические выражения или последнее состояние устройства в одном документе, добавив следующий код в проект.

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") },
    "XMS-001-FE24C");
```

Вот и все! Это основные компоненты приложения базы данных Azure Cosmos DB, использующего ключ секции для эффективного масштабирования распределения данных по секциям.  

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение дальше, удалите все ресурсы, созданные в ходе работы с этим руководством, на портале Azure, выполнив следующие действия:

1. В меню слева на портале Azure щелкните **Группы ресурсов**, а затем выберите уникальное имя созданного ресурса.
2. На странице группы ресурсов щелкните **Удалить**, в текстовом поле введите имя ресурса для удаления и щелкните **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы выполнили следующее:

> [!div class="checklist"]
> * создали учетную запись Azure Cosmos DB;
> * создали базу данных и коллекцию с ключом секции;
> * создали документы JSON;
> * обновили документ;
> * запросили секционированные коллекции;
> * выполнили хранимую процедуру;
> * удалили документ;
> * удалили базу данных.

Теперь вы можете перейти к следующему руководству и импортировать дополнительные данные в учетную запись Cosmos DB.

> [!div class="nextstepaction"]
> [Импорт данных в Azure Cosmos DB](import-data.md)
