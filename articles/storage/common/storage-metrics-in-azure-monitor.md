---
title: Метрики службы хранилища Azure в Azure Monitor | Документация Майкрософт
description: Узнайте о новых метриках, которые предлагает Azure Monitor.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 09/05/2017
ms.author: fryu
ms.openlocfilehash: 3c4c9a330286eb00aa6a0da6e95b56b49b6eaa6c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Метрики службы хранилища Azure в Azure Monitor

С помощью метрик службы хранилища Azure вы можете анализировать тенденции использования, выполнять трассировку запросов и диагностику проблем учетной записи хранения.

Azure Monitor предоставляет унифицированный пользовательский интерфейс для мониторинга различных служб Azure. Дополнительные сведения см. в статье [Обзор мониторинга в Microsoft Azure](../../monitoring-and-diagnostics/monitoring-overview.md). Интеграция службы хранилища Azure с Azure Monitor осуществляется путем отправки данных метрик на платформу Azure Monitor.

## <a name="access-metrics"></a>Доступ к метрикам

Azure Monitor предоставляет несколько способов доступа к метрикам. Для такого доступа вы можете использовать [портал Azure](https://portal.azure.com), API-интерфейсы Azure Monitor (REST и .NET), а также решения для анализа, например Operation Management Suite и концентраторы событий. Дополнительные сведения см. в статье [Обзор метрик в Microsoft Azure](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Метрики включены по умолчанию, и вы можете получить доступ к данным за последние 30 дней. Если необходимо хранить данные метрик в течение длительного периода времени, вы можете архивировать их в учетную запись хранения Azure. Для настройки используйте [параметры диагностики](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) в Azure Monitor.

### <a name="access-metrics-in-the-azure-portal"></a>Доступ к метрикам на портале Azure

На портале Azure можно отслеживать метрики в течение продолжительного периода времени. В примере ниже рассматривается, как просмотреть **используемую емкость** на уровне учетной записи.

![снимок экрана доступа к метрикам на портале Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Для метрик с поддержкой измерений необходимо выполнить фильтрацию по нужному значению измерения. В примере ниже объясняется, как просмотреть **транзакции** на уровне учетной записи с типом ответа **Успешно**.

![снимок экрана доступа к метрикам с поддержкой измерений на портале Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Доступ к метрикам через REST API

Azure Monitor предоставляет [интерфейсы REST API](/rest/api/monitor/) для считывания определения и значений метрик. В этом разделе рассматривается, как считывать метрики хранилища. Идентификатор ресурса используется во всех интерфейсах REST API. Дополнительные сведения см. в статье, посвященной [обзору идентификатора ресурса для служб в службе хранилища](#understanding-resource-id-for-services-in-storage).

В примере ниже рассматривается, как использовать [ArmClient](https://github.com/projectkudu/ARMClient) в командной строке, чтобы упростить тестирование с помощью REST API.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Получение списка определений метрик на уровне учетной записи с помощью REST API

В примере ниже показано, как получить список определений метрик на уровне учетной записи.

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2017-05-01-preview

```

Если вы хотите получить список определений метрик для большого двоичного объекта, таблицы, файла или очереди, вам необходимо указать разные идентификаторы ресурсов для каждой службы с помощью REST API.

Ответ содержит определение метрики в формате JSON:

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Считывание значений метрик на уровне учетной записи с помощью REST API

В примере ниже показано, как считать данные метрики на уровне учетной записи.

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metric=Availability&api-version=2017-05-01-preview&aggregation=Average&interval=PT1H"

```

Если вы хотите считать значения метрик для большого двоичного объекта, таблицы, файла или очереди, вам необходимо указать разные идентификаторы ресурсов для каждой службы с помощью REST API (см. пример выше).

Ответ ниже содержит значения метрик в формате JSON:

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

### <a name="access-metrics-with-the-net-sdk"></a>Доступ к метрикам с помощью пакета SDK для .NET

Azure Monitor предоставляет [пакет SDK для .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) для считывания определения и значений метрик. [Пример кода](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) демонстрирует использование пакета SDK с различными параметрами. Необходимо использовать `0.18.0-preview` или более поздней версии для метрик хранилища. Идентификатор ресурса используется в пакете SDK для .NET Дополнительные сведения см. в статье, посвященной [обзору идентификатора ресурса для служб в службе хранилища](#understanding-resource-id-for-services-in-storage).

В примере ниже показано, как использовать пакет SDK для .NET Azure Monitor для считывания метрик хранилища.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Получение списка определений метрик на уровне учетной записи с помощью пакета SDK для .NET

В примере ниже показано, как получить список определений метрик на уровне учетной записи.

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        //How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            //Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

Если вы хотите получить список определений метрик для большого двоичного объекта, таблицы, файла или очереди, вам необходимо указать разные идентификаторы ресурсов для каждой службы с помощью REST API.

#### <a name="read-metric-values-with-the-net-sdk"></a>Считывание значений метрик с помощью пакета SDK для .NET

В примере ниже показано, как считать данные `UsedCapacity` на уровне учетной записи.

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        //How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToString("o");
        string endDate = DateTime.Now.ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metric: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

Если вы хотите считать значения метрик для большого двоичного объекта, таблицы, файла или очереди, вам необходимо указать разные идентификаторы ресурсов для каждой службы с помощью REST API (см. пример выше).

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Считывание значений многомерных метрик с помощью пакета SDK для .NET

Для многомерных метрик необходимо определить фильтр по метаданным, если требуется считать данные метрик для конкретного значения измерения.

В примере ниже показано, как считать данные метрики в метрике, поддерживающей многомерные значения.

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        //How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToString("o");
        string endDate = DateTime.Now.ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metric: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Обзор идентификатора ресурса для служб в службе хранилища Azure

Идентификатор ресурса представляет собой уникальный идентификатор ресурса в Azure. При использовании REST API Azure Monitor для считывания определений метрик или их значений необходимо использовать идентификатор ресурса для ресурса, который вы планируете использовать. Шаблон идентификатора ресурса имеет следующий формат:

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
`

С помощью Azure Monitor хранилище предоставляет метрики на уровне учетной записи хранения и уровне службы. Например, вы можете извлечь метрики только хранилища BLOB-объектов. Каждый уровень имеет свой собственный идентификатор ресурса, который используется для получения метрик только этого уровня.

### <a name="resource-id-for-a-storage-account"></a>Идентификатор ресурса для учетной записи хранения

Ниже рассматривается формат указания идентификатора ресурса для учетной записи хранения.

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
`

### <a name="resource-id-for-the-storage-services"></a>Идентификатор ресурса для служб хранилища

Ниже рассматривается формат указания идентификатора ресурса для каждой службы хранилища.

* Идентификатор ресурса службы BLOB-объектов: `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
`
* Идентификатор ресурса службы таблиц: `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
`
* Идентификатор ресурса службы очередей: `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
`
* Идентификатор ресурса файловой службы: `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
`

### <a name="resource-id-in-azure-monitor-rest-api"></a>Идентификатор ресурса в REST API Azure Monitor

Ниже приведен шаблон, используемый при вызове REST API Azure Monitor.

`
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
`

## <a name="capacity-metrics"></a>Метрики емкости
Значения метрик емкости отправляются в Azure Monitor каждый час. Значения обновляются каждый день. Интервал времени определяет интервал, за который представлены значения метрик. Поддерживаемый интервал времени для всех метрик емкости — один час (PT1H).

Служба хранилища Azure предоставляет следующие метрики емкости в Azure Monitor.

### <a name="account-level"></a>На уровне учетной записи

| Имя метрики | ОПИСАНИЕ |
| ------------------- | ----------------- |
| UsedCapacity | Объем хранилища, используемый учетной записью хранения. Для стандартных учетных записей хранения это общая емкость, используемая большим двоичным объектом, таблицей, файлом или очередью. Для учетных записей хранения уровня "Премиум" и учетных записей хранения больших двоичных объектов эта емкость сопоставима с емкостью метрики BlobCapacity. <br/><br/> Единица измерения: байт <br/> Тип агрегирования: средний <br/> Пример значения: 1024 |

### <a name="blob-storage"></a>Хранилище BLOB-объектов

| Имя метрики | ОПИСАНИЕ |
| ------------------- | ----------------- |
| BlobCapacity | Объем хранилища BLOB-объектов, используемый в учетной записи хранения. <br/><br/> Единица измерения: байт <br/> Тип агрегирования: средний <br/> Пример значения: 1024 <br/> Измерение: BlobType ([определение](#metrics-dimensions)) |
| BlobCount    | Количество больших двоичных объектов, хранящихся в учетной записи хранения. <br/><br/> Единица измерения: число <br/> Тип агрегирования: средний <br/> Пример значения: 1024 <br/> Измерение: BlobType ([определение](#metrics-dimensions)) |
| ContainerCount    | Число контейнеров в учетной записи хранения. <br/><br/> Единица измерения: число <br/> Тип агрегирования: средний <br/> Пример значения: 1024 |

### <a name="table-storage"></a>Хранилище таблиц

| Имя метрики | ОПИСАНИЕ |
| ------------------- | ----------------- |
| TableCapacity | Объем хранилища таблиц, используемый учетной записью хранения. <br/><br/> Единица измерения: байт <br/> Тип агрегирования: средний <br/> Пример значения: 1024 |
| TableCount   | Количество таблиц в учетной записи хранения. <br/><br/> Единица измерения: число <br/> Тип агрегирования: средний <br/> Пример значения: 1024 |
| TableEntityCount | Количество сущностей таблицы в учетной записи хранения. <br/><br/> Единица измерения: число <br/> Тип агрегирования: средний <br/> Пример значения: 1024 |

### <a name="queue-storage"></a>Хранилище очередей

| Имя метрики | ОПИСАНИЕ |
| ------------------- | ----------------- |
| QueueCapacity | Объем хранилища очередей, используемый учетной записью хранения. <br/><br/> Единица измерения: байт <br/> Тип агрегирования: средний <br/> Пример значения: 1024 |
| QueueCount   | Количество очередей в учетной записи хранения. <br/><br/> Единица измерения: число <br/> Тип агрегирования: средний <br/> Пример значения: 1024 |
| QueueMessageCount | Количество действительных сообщений очереди в учетной записи хранения. <br/><br/>Единица измерения: число <br/> Тип агрегирования: средний <br/> Пример значения: 1024 |

### <a name="file-storage"></a>Хранилище файлов

| Имя метрики | ОПИСАНИЕ |
| ------------------- | ----------------- |
| FileCapacity | Объем хранилища файлов, используемый учетной записью хранения. <br/><br/> Единица измерения: байт <br/> Тип агрегирования: средний <br/> Пример значения: 1024 |
| FileCount   | Количество файлов в учетной записи хранения. <br/><br/> Единица измерения: число <br/> Тип агрегирования: средний <br/> Пример значения: 1024 |
| FileShareCount | Количество общих папок в учетной записи хранения. <br/><br/> Единица измерения: число <br/> Тип агрегирования: средний <br/> Пример значения: 1024 |

## <a name="transaction-metrics"></a>Метрики транзакций

Метрики транзакций отправляются из службы хранилища Azure в Azure Monitor каждую минуту. Все метрики транзакций доступны на уровне учетной записи и службы (хранилище BLOB-объектов, хранилище таблиц, файлы Azure и хранилище очередей). Интервал времени определяет интервал, за который представлены значения метрик. Поддерживаемые интервалы времени для всех метрик транзакций — PT1H и PT1M.

Служба хранилища Azure предоставляет следующие метрики транзакций в Azure Monitor.

| Имя метрики | ОПИСАНИЕ |
| ------------------- | ----------------- |
| Транзакции | Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. <br/><br/> Единица измерения: число <br/> Тип агрегирования: итог <br/> Применимые измерения: ResponseType, GeoType, ApiName ([определение](#metrics-dimensions))<br/> Пример значения: 1024 |
| Входящие | Объем входящих данных. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure. <br/><br/> Единица измерения: байт <br/> Тип агрегирования: итог <br/> Применимые измерения: GeoType, ApiName ([определение](#metrics-dimensions)) <br/> Пример значения: 1024 |
| Исходящие | Объем исходящих данных. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации. <br/><br/> Единица измерения: байт <br/> Тип агрегирования: итог <br/> Применимые измерения: GeoType, ApiName ([определение](#metrics-dimensions)) <br/> Пример значения: 1024 |
| SuccessServerLatency | Среднее время, используемое для обработки успешного запроса службы хранилища Azure. Это значение не включает в себя сетевую задержку, указанную в метрике SuccessE2ELatency. <br/><br/> Единица измерения: миллисекунды <br/> Тип агрегирования: средний <br/> Применимые измерения: GeoType, ApiName ([определение](#metrics-dimensions)) <br/> Пример значения: 1024 |
| SuccessE2ELatency | Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа. <br/><br/> Единица измерения: миллисекунды <br/> Тип агрегирования: средний <br/> Применимые измерения: GeoType, ApiName ([определение](#metrics-dimensions)) <br/> Пример значения: 1024 |
| Доступность | Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая запросы, вызвавшие непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению доступности службы хранения или указанной операции API. <br/><br/> Единица измерения: % <br/> Тип агрегирования: средний <br/> Применимые измерения: GeoType, ApiName ([определение](#metrics-dimensions)) <br/> Пример значения: 99,99 |

## <a name="metrics-dimensions"></a>Измерения метрик

Служба хранилища Azure поддерживает следующие измерения метрик в Azure Monitor.

| Название измерения | ОПИСАНИЕ |
| ------------------- | ----------------- |
| BlobType | Тип большого двоичного объекта только для метрик больших двоичных объектов. Поддерживаемые значения: **BlockBlob** и **PageBlob**. Расширенный большой двоичный объект включен в BlockBlob. |
| ResponseType | Тип ответа транзакции. Доступные значения: <br/><br/> <li>ServerOtherError: все остальные ошибки на стороне сервера, за исключением описанных </li> <li> ServerBusyError: запрос с проверкой подлинности, который вернул код состояния HTTP 503. </li> <li> ServerTimeoutError: запрос с проверкой подлинности (время ожидания которого истекло), который вернул код состояния HTTP 500. Время ожидания истекло из-за ошибки на стороне сервера. </li> <li> AuthorizationError: запрос с проверкой подлинности, завершившийся сбоем из-за несанкционированного доступа к данным или сбоя авторизации. </li> <li> NetworkError: запрос с проверкой подлинности, завершившийся сбоем из-за ошибок сети. Чаще всего происходит, когда клиент преждевременно закрывает подключение до истечения времени ожидания. </li> <li>    ClientThrottlingError. Ошибка регулирования на стороне клиента. </li> <li> ClientTimeoutError: запрос с проверкой подлинности (время ожидания которого истекло), который вернул код состояния HTTP 500. Если для времени ожидания сети клиента или времени ожидания запроса задано меньшее значение, чем ожидалось службой хранения, то это ожидаемое время ожидания. В противном случае оно будет отмечено как ServerTimeoutError. </li> <li> ClientOtherError: все остальные ошибки на стороне клиента, за исключением описанных. </li> <li> Успешно: успешный запрос|
| GeoType | Транзакции из основного или дополнительного кластера. Допустимые значения: Primary и Secondary. Применимо к геоизбыточному хранилищу с доступом только для чтения (RA-GRS) при считывании объектов из дополнительного клиента. |
| ApiName | Имя операции. Например:  <br/> <li>CreateContainer</li> <li>DeleteBlob</li> <li>GetBlob</li> Для всех имен операций. Дополнительные сведения см. в этой [статье](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages#logged-operations.md). |

Для метрик с поддержкой измерений необходимо указать значение измерения, чтобы увидеть соответствующие значения метрик. Например, если вы хотите просмотреть значения **Transactions** для поиска успешных ответов, необходимо выполнить фильтрацию по измерению **ResponseType** со значением **Success**. Если вы хотите просмотреть значения **BlobCount** для блочного BLOB-объекта, необходимо выполнить фильтрацию по измерению **BlobType** со значением **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Непрерывная работа службы метрик прежних версий

Устаревшие метрики доступны параллельно с управляемыми метриками Azure Monitor. Предусмотрена та же поддержка, пока в службе хранилища Azure обслуживаются устаревшие метрики.

## <a name="next-steps"></a>Дополнительная информация

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
