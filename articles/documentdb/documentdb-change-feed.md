---
title: "Работа с поддержкой веб-канала изменений в Azure DocumentDB | Документация Майкрософт"
description: "Используйте поддержку веб-канала изменений Azure DocumentDB, чтобы отслеживать изменения в документах DocumentDB и выполнять обработку на основе событий, например триггеров, и поддерживать кэши и аналитические системы в обновленном состоянии."
keywords: "веб-канал изменений"
services: documentdb
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 12/13/2016
ms.author: b-hoedid
translationtype: Human Translation
ms.sourcegitcommit: b22e75264345bc9d155bd1abc1fdb6e978dfad04
ms.openlocfilehash: bafc50750381616ecf30c4e41090f342d82007f9


---
# <a name="working-with-the-change-feed-support-in-azure-documentdb"></a>Работа с поддержкой веб-канала изменений в Azure DocumentDB
[Azure DocumentDB](documentdb-introduction.md) — это быстрая и гибкая служба базы данных NoSQL, используемая для хранения большого объема данных транзакций и операционных данных с прогнозируемой задержкой операций чтения и записи менее&10; секунд. Благодаря этому она хорошо подходит для приложений Интернета вещей, розничной торговли, игр и приложений для ведения журнала операций. Распространенный конструктивный шаблон в этих приложениях заключается в отслеживании изменений в данных DocumentDB и обновлении материализованных представлений, выполнении аналитики в реальном времени, архивировании данных в автономном неструктурированном защищенном хранилище и активировании уведомлений для определенных событий на основе этих изменений. **Поддержка веб-канала изменений** DocumentDB дает возможность создавать эффективные и масштабируемые решения для каждого из этих шаблонов.

Благодаря поддержке веб-канала изменений DocumentDB предоставляет отсортированный список документов в коллекции DocumentDB в том порядке, в котором они были изменены. Этот канал можно использовать для прослушивания изменений данных в коллекции и выполнения следующих операций:

* активирование вызова API при вставке или изменении документа;
* выполнение обработки или обновлений в режиме реального времени (поток);
* синхронизация данных с кэшем, поисковой системой или хранилищем данных.

Изменения в DocumentDB сохраняются и обрабатываются асинхронно, а также распределяются в один или несколько потребителей для параллельной обработки. Рассмотрим интерфейсы API для веб-канала изменений и их использование для создания масштабируемых приложений, выполняющихся в реальном времени.

![Использование веб-канала изменений DocumentDB для аналитики в реальном времени и вычислений на основе событий](./media/documentdb-change-feed/changefeed.png)

## <a name="use-cases-and-scenarios"></a>Варианты использования и сценарии
Веб-канал изменений обеспечивает эффективную обработку больших наборов данных с большим объемом операций записи и представляет собой альтернативу отправке запросов на все наборы данных, чтобы определить, какие изменения были внесены. Например, можно эффективно выполнить следующие задачи:

* Обновление кэша, индекса поиска или хранилища данными, хранящимися в Azure DocumentDB.
* Реализация распределения данных по уровням и их архивирование на уровне приложений, то есть хранение "горячих данных" в DocumentDB и удаление "холодных данных" в [хранилище BLOB-объектов Azure](../storage/storage-introduction.md) или [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Реализация пакетной аналитики для данных с помощью [Apache Hadoop](documentdb-run-hadoop-with-hdinsight.md).
* Реализация [лямбда-конвейеров в Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) с помощью DocumentDB. DocumentDB предоставляет масштабируемое решение базы данных, которое может обрабатывать операции приема и запроса, а также реализовывать лямбда-архитектуры с низкой совокупной стоимостью владения. 
* Выполнение переносов в другую учетную запись Azure DocumentDB с использованием другой схемы секционирования без простоев.

**Лямбда-конвейеры при использовании Azure DocumentDB для приема и запроса**

![Лямбда-конвейер для приема и запроса на основе Azure DocumentDB](./media/documentdb-change-feed/lambda.png)

DocumentDB можно использовать для получения и хранения данных о событиях с устройств, датчиков, инфраструктуры и приложений и обрабатывать эти события в реальном времени с помощью [Azure Stream Analytics](documentdb-search-indexer.md), [Apache Storm](../hdinsight/hdinsight-storm-overview.md) или [Apache Spark](../hdinsight/hdinsight-apache-spark-overview.md). 

В веб-приложениях и мобильных приложениях вы можете отслеживать события, такие как изменения в профиле клиента, настройках или расположении, чтобы активировать определенные действия, например отправку push-уведомлений на устройства, с помощью [Функций Azure](../azure-functions/functions-bindings-documentdb.md) или [служб приложений](https://azure.microsoft.com/services/app-service/). При использовании DocumentDB для создания игр веб-канал изменений можно использовать, например, для создания списков лидеров в режиме реального времени на основе очков в завершенных играх.

## <a name="how-change-feed-works-in-azure-documentdb"></a>Работа веб-канала изменений в Azure DocumentDB
DocumentDB предоставляет возможность постепенно считывать обновления, внесенные в коллекцию DocumentDB. Этот веб-канал изменений обладает следующими свойствами:

* Изменения сохраняются в DocumentDB и могут обрабатываться асинхронно.
* Изменения в документах в коллекции сразу же становятся доступными в веб-канале изменений.
* Каждое изменение в документе отображается только единожды в веб-канале изменений. В журнал изменений вносится только самое последнее изменение в конкретном документе. Промежуточные изменения могут быть недоступны.
* Веб-канал изменений сортируется в порядке изменения в каждом значении ключа раздела. В значениях ключа раздела нет установленного порядка.
* Изменения можно синхронизировать в любой момент времени. Это означает, что фиксированный период хранения данных, для которого доступны изменения, отсутствует.
* Изменения доступны в виде фрагментов диапазонов ключей разделов. Эта возможность позволяет нескольким потребителям и серверам параллельно обрабатывать изменения из больших коллекций.
* Приложения могут запросить несколько веб-каналов изменений в одной коллекции одновременно.

Веб-канал изменений DocumentDB включен по умолчанию для всех учетных записей, и дополнительная плата за него в вашей учетной записи не взимается. Вы можете использовать свою [подготовленную пропускную способность](documentdb-request-units.md) в своем регионе записи или любом [регионе чтения](documentdb-distribute-data-globally.md) для чтения с веб-канала изменений так же, как и любую другую операцию из DocumentDB. Веб-канал изменений также отслеживает операции вставки и обновления, внесенные в документы в коллекции. Вы можете отслеживать операции удаления, установив флажок soft-delete в документах вместо удаления. Кроме того, с помощью [функции срока жизни](documentdb-time-to-live.md) для документов можно задать ограниченный срок действия, например 24 часа, и использовать значение этого свойства для отслеживания операций удаления. При использовании этого решения необходимо обрабатывать изменения в течение более короткого промежутка времени, чем срок жизни. Веб-канал изменений доступен для каждого диапазона ключей разделов в коллекции документов, и поэтому его можно распределить в один или несколько потребителей для параллельной обработки. 

![Распределенная обработка веб-канала изменений DocumentDB](./media/documentdb-change-feed/changefeedvisual.png)

В следующем разделе описывается, как получить доступ к веб-каналу изменений с помощью REST API и пакетов SDK DocumentDB.

## <a name="working-with-the-rest-api-and-sdk"></a>Работа с REST API и пакетами SDK
DocumentDB предоставляет эластичные контейнеры хранилища и пропускной способности, называемые **коллекциями**. Данные в коллекциях логически сгруппированы с помощью [ключей разделов](documentdb-partition-data.md) для масштабируемости и производительности. DocumentDB предоставляет различные интерфейсы API для доступа к этим данным, включая поиск по идентификатору (чтение или получение), запросы и чтение каналов (сканирования). Веб-канал изменений можно получить путем заполнения двух новых заголовков запроса в интерфейсе API `ReadDocumentFeed` DocumentDB, и его можно обрабатывать параллельно в диапазонах ключей разделов.

### <a name="readdocumentfeed-api"></a>Интерфейс API ReadDocumentFeed
Давайте рассмотрим, как работает ReadDocumentFeed. DocumentDB поддерживает чтение веб-канала документов в коллекции через интерфейс API `ReadDocumentFeed`. Например, следующий запрос возвращает страницу документов внутри коллекции `serverlogs`. 

    GET https://mydocumentdb.documents.azure.com/dbs/smalldb/colls/smallcoll HTTP/1.1
    x-ms-date: Tue, 22 Nov 2016 17:05:14 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dgo7JEogZDn6ritWhwc5hX%2fNTV4wwM1u9V2Is1H4%2bDRg%3d
    Cache-Control: no-cache
    x-ms-consistency-level: Strong
    User-Agent: Microsoft.Azure.Documents.Client/1.10.27.5
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

Результаты можно ограничить с помощью заголовка `x-ms-max-item-count`, а операции чтения можно возобновить, повторно подав запрос с помощью заголовка `x-ms-continuation`, возвращенного в предыдущем ответе. При выполнении из единого клиента `ReadDocumentFeed` выполняет итерацию результатов по разделам последовательно. 

**Веб-канал для последовательного чтения документов**

![Последовательное выполнение ReadDocumentFeed DocumentDB](./media/documentdb-change-feed/readfeedserial.png)

Веб-канал документов можно также получить с помощью одного из поддерживаемых [пакетов SDK для DocumentDB](documentdb-sdk-dotnet.md). Например, в следующем фрагменте кода показано, как выполнять ReadDocumentFeed в .NET.

    FeedResponse<dynamic> feedResponse = null;
    do
    {
        feedResponse = await client.ReadDocumentFeedAsync(collection, new FeedOptions { MaxItemCount = -1 });
    }
    while (feedResponse.ResponseContinuation != null);

> [!NOTE]
> Для веб-канала изменений требуется пакет SDK версии 1.11.0 и более поздних (сейчас доступны в закрытой предварительной версии).

### <a name="distributed-execution-of-readdocumentfeed"></a>Распределенное выполнение ReadDocumentFeed
Для коллекций, содержащих терабайты данных и более или принимающих большое количество обновлений, последовательное выполнение веб-канала чтения с компьютера с одним клиентом может оказаться нецелесообразным. Для поддержки этих сценариев с данными большого размера DocumentDB содержит интерфейсы API для распределения вызовов `ReadDocumentFeed` по нескольким клиентским считывателям и потребителям открытым образом. 

**Веб-канал для распределенного чтения документов**

![Распределенное выполнение ReadDocumentFeed DocumentDB](./media/documentdb-change-feed/readfeedparallel.png)

Чтобы обеспечить масштабируемую обработку добавочных изменений, DocumentDB поддерживает модель масштабирования для интерфейса API веб-канала изменений на основе диапазонов ключей разделов.

* Список диапазонов ключей разделов для коллекции можно получить, выполнив вызов `ReadPartitionKeyRanges`. 
* Для каждого диапазона ключей разделов можно выполнить `ReadDocumentFeed`, чтобы читать документы с помощью ключей разделов в пределах диапазона.

### <a name="retrieving-partition-key-ranges-for-a-collection"></a>Извлечение диапазонов ключей разделов для коллекции
Диапазоны ключей разделов можно получить путем запроса ресурса `pkranges` в коллекции. Например, следующий запрос возвращает список диапазонов ключей разделов для коллекции `serverlogs`:

    GET https://querydemo.documents.azure.com/dbs/bigdb/colls/serverlogs/pkranges HTTP/1.1
    x-ms-date: Tue, 15 Nov 2016 07:26:51 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dEConYmRgDExu6q%2bZ8GjfUGOH0AcOx%2behkancw3LsGQ8%3d
    x-ms-consistency-level: Session
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: querydemo.documents.azure.com

Этот запрос возвращает следующий ответ, содержащий метаданные о диапазонах ключей разделов:

    HTTP/1.1 200 Ok
    Content-Type: application/json
    x-ms-item-count: 25
    x-ms-schemaversion: 1.1
    Date: Tue, 15 Nov 2016 07:26:51 GMT

    {
       "_rid":"qYcAAPEvJBQ=",
       "PartitionKeyRanges":[
          {
             "_rid":"qYcAAPEvJBQCAAAAAAAAUA==",
             "id":"0",
             "_etag":"\"00002800-0000-0000-0000-580ac4ea0000\"",
             "minInclusive":"",
             "maxExclusive":"05C1CFFFFFFFF8",
             "_self":"dbs\/qYcAAA==\/colls\/qYcAAPEvJBQ=\/pkranges\/qYcAAPEvJBQCAAAAAAAAUA==\/",
             "_ts":1477100776
          },
          ...
       ],
       "_count": 25
    }


**Свойства диапазона ключей разделов.** Каждый диапазон ключей разделов содержит свойства метаданных, приведенные в следующей таблице.

<table>
    <tr>
        <th>Имя заголовка</th>
        <th>Описание</th>
    </tr>
    <tr>
        <td>id</td>
        <td>
            <p>Идентификатор для диапазона ключей разделов. Это стабильный и уникальный идентификатор в каждой коллекции.</p>
            <p>Должен использоваться в следующем вызове для считывания изменений с помощью диапазона ключей разделов.</p>
        </td>
    </tr>
    <tr>
        <td>maxExclusive</td>
        <td>Максимальное значение хэша ключа раздела для диапазона ключей разделов. Для внутреннего использования.</td>
    </tr>
    <tr>
        <td>minInclusive</td>
        <td>Минимальное значение хэша ключа раздела для диапазона ключей разделов. Для внутреннего использования.</td>
    </tr>       
</table>

Это можно сделать с помощью одного из поддерживаемых [пакетов SDK для DocumentDB](documentdb-sdk-dotnet.md). Например, в следующем фрагменте кода показано, как получить диапазон ключей разделов в .NET.

    List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();
    FeedResponse<PartitionKeyRange> response;

    do
    {
        response = await client.ReadPartitionKeyRangeFeedAsync(collection);
        partitionKeyRanges.AddRange(response);
    }
    while (response.ResponseContinuation != null);

DocumentDB поддерживает получение документов в диапазоне ключей разделов путем задания необязательного заголовка `x-ms-documentdb-partitionkeyrangeid`. 

### <a name="performing-an-incremental-readdocumentfeed"></a>Выполнение добавочного ReadDocumentFeed
ReadDocumentFeed поддерживает следующие сценарии и задачи для добавочной обработки изменений в коллекциях DocumentDB:

* Чтение всех изменений в документах с самого начала, то есть с момента создания коллекции.
* Чтение всех изменений до будущих обновлений в документах, начиная с текущего момента времени.
* Чтение всех изменений в документах из логической версии коллекции (ETag). Вы можете создать контрольную точку потребителей в зависимости от возвращаемого ETag из добавочных запросов на чтение канала.

Изменения включают операции вставки и обновлений в документах. Для отслеживания операций удаления в документах необходимо использовать свойство soft delete или [встроенное свойство срока жизни](documentdb-time-to-live.md), чтобы обозначить отложенное удаление в веб-канале изменений.

В следующей таблице перечислены заголовки запроса и ответа для операций ReadDocumentFeed.

**Заголовки запроса для добавочного ReadDocumentFeed**

<table>
    <tr>
        <th>Имя заголовка</th>
        <th>Описание</th>
    </tr>
    <tr>
        <td>A-IM</td>
        <td>Для этого параметра необходимо задать значение Incremental feed (Добавочный канал) или пропустить его</td>
    </tr>
    <tr>
        <td>If-None-Match</td>
        <td>
            <p>Без заголовка: возвращает все изменения с самого начала (создание коллекции)</p>
            <p>"*": возвращает все новые изменения данных в коллекции</p>
            <p>&lt;eTag&gt;: если для коллекции задать значение ETag, возвращает все изменения, внесенные с момента этой логической отметки времени</p>
        </td>
    </tr>
    <tr>
        <td>x-ms-documentdb-partitionkeyrangeid</td>
        <td>Идентификатор диапазона ключей разделов для считывания данных.</td>
    </tr>
</table>

**Заголовки ответа для добавочного ReadDocumentFeed**

<table>
    <tr>
        <th>Имя заголовка</th>
        <th>Описание</th>
    </tr>
    <tr>
        <td>etag</td>
        <td>
            <p>Логический порядковый номер последнего документа, возвращенного в ответе.</p>
            <p>Добавочный ReadDocumentFeed можно возобновить, повторно задав это значение в параметре If-None-Match.</p>
        </td>
    </tr>
</table>

Ниже приведен пример запроса для возврата всех добавочных изменений в коллекции из логической версии или ETag `28535` и диапазона ключей разделов `16`.

    GET https://mydocumentdb.documents.azure.com/dbs/bigdb/colls/bigcoll/docs HTTP/1.1
    x-ms-max-item-count: 1
    If-None-Match: "28535"
    A-IM: Incremental feed
    x-ms-documentdb-partitionkeyrangeid: 16
    x-ms-date: Tue, 22 Nov 2016 20:43:01 GMT
    authorization: type%3dmaster%26ver%3d1.0%26sig%3dzdpL2QQ8TCfiNbW%2fEcT88JHNvWeCgDA8gWeRZ%2btfN5o%3d
    x-ms-version: 2016-07-11
    Accept: application/json
    Host: mydocumentdb.documents.azure.com

Изменения упорядочиваются по времени в каждом значении ключа раздела в пределах диапазона ключа раздела. В значениях ключа раздела нет установленного порядка. Если результатов больше, чем может поместиться на одной странице, можно прочитать следующую страницу результатов, повторно отправив запрос с заголовком `If-None-Match` со значением, равным `etag` из предыдущего ответа. Если несколько документов обновлены транзакционно в хранимой процедуре или триггере, все они будут возвращены на одной странице ответа.

Пакет SDK для .NET предоставляет вспомогательные классы `CreateDocumentChangeFeedQuery` и `ChangeFeedOptions` для доступа к изменениям, внесенным в коллекцию. В следующем фрагменте кода показано, как получить все изменения с самого начала с использованием пакета SDK для .NET из одного клиента.

    private async Task<Dictionary<string, string>> GetChanges(
        DocumentClient client,
        string collection,
        Dictionary<string, string> checkpoints)
    {
        List<PartitionKeyRange> partitionKeyRanges = new List<PartitionKeyRange>();
        FeedResponse<PartitionKeyRange> pkRangesResponse;

        do
        {
            pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(collection);
            partitionKeyRanges.AddRange(pkRangesResponse);
        }
        while (pkRangesResponse.ResponseContinuation != null);

        foreach (PartitionKeyRange pkRange in partitionKeyRanges)
        {
            string continuation = null;
            checkpoints.TryGetValue(pkRange.Id, out continuation);

            IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
                collection,
                new ChangeFeedOptions
                {
                    PartitionKeyRangeId = pkRange.Id,
                    StartFromBeginning = true,
                    RequestContinuation = continuation,
                    MaxItemCount = 1
                });

            while (query.HasMoreResults)
            {
                FeedResponse<DeviceReading> readChangesResponse = query.ExecuteNextAsync<DeviceReading>().Result;

                foreach (DeviceReading changedDocument in readChangesResponse)
                {
                    Console.WriteLine(changedDocument.Id);
                }

                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
        }

        return checkpoints;
    }

А в следующем фрагменте показано, как обрабатывать изменения в реальном времени с помощью DocumentDB, используя поддержку веб-канала изменений и предыдущую функцию. Первый вызов возвращает все документы в коллекции, а второй — только два документа, созданные с момента создания последней контрольной точки.

    // Returns all documents in the collection.
    Dictionary<string, string> checkpoints = await GetChanges(client, collection, new Dictionary<string, string>());

    await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-201", MetricType = "Temperature", Unit = "Celsius", MetricValue = 1000 });
    await client.CreateDocumentAsync(collection, new DeviceReading { DeviceId = "xsensr-212", MetricType = "Pressure", Unit = "psi", MetricValue = 1000 });

    // Returns only the two documents created above.
    checkpoints = await GetChanges(client, collection, checkpoints);


Вы также можете фильтровать веб-канал изменений с помощью логики на стороне клиента, чтобы обрабатывать события выборочно. Например, ниже приведен фрагмент кода, в котором используется LINQ на стороне клиента для обработки только событий изменения температуры с датчиков устройств.

    FeedResponse<DeviceReading> readChangesResponse = query.ExecuteNextAsync<DeviceReading>().Result;

    foreach (DeviceReading changedDocument in 
        readChangesResponse.AsEnumerable().Where(d => d.MetricType == "Temperature" && d.MetricValue > 1000L))
    {
        // trigger an action, like call an API
    }

В этой статье предоставлено руководство по поддержке веб-канала изменений DocumentDB и отслеживанию изменений, внесенных в данные DocumentDB, с помощью REST API и (или) пакетов SDK DocumentDB. 

## <a name="next-steps"></a>Дальнейшие действия
* Попробуйте поработать с [примерами кода веб-канала изменений DocumentDB в Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed).
* Узнайте больше о [модели ресурсов и иерархии DocumentDB](documentdb-resources.md).
* Приступите к созданию кода с помощью [пакетов SDK](documentdb-sdk-dotnet.md) или [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) DocumentDB.


<!--HONumber=Dec16_HO2-->


