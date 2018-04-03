---
title: Включение метрик хранилища на портале Azure | Документация Майкрософт
description: Как включить метрики хранилища для служб больших двоичных объектов, очередей, таблиц и файлов.
services: storage
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 0407adfc-2a41-4126-922d-b76e90b74563
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/14/2017
ms.author: rogarana
ms.openlocfilehash: 0caa4eff80877ad4bf8d501a276e82922b1a84c7
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="enabling-azure-storage-metrics-and-viewing-metrics-data"></a>Включение метрик хранилища Azure и просмотр данных метрик
[!INCLUDE [storage-selector-portal-enable-and-view-metrics](../../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Обзор
Метрики хранилища включаются по умолчанию при создании учетной записи хранения. Вы можете настроить мониторинг с помощью [портала Azure](https://portal.azure.com), Windows PowerShell либо программно, используя клиентские библиотеки службы хранилища.

Можно настроить период хранения данных метрик. Он определяет длительность хранения метрик службой хранилища и размер оплаты за пространство, необходимое для их хранения. Как правило, следует использовать менее длительный период хранения для минутных метрик, нежели для часовых метрик, так как минутные метрики требуют значительно большего пространства. Выберите такой период хранения, чтобы было достаточно времени на анализ данных и скачивание метрик, которые требуется сохранить локально для анализа или составления отчета. Помните, что за скачивание данных метрик из учетной записи хранения также взимается плата.

## <a name="how-to-enable-metrics-using-the-azure-portal"></a>Как включить метрики с помощью портала Azure
Выполните следующие действия, чтобы включить метрики на [портале Azure](https://portal.azure.com).

1. Войдите в свою учетную запись хранения.
1. Щелкните **Диагностика** в области **Меню**.
1. Убедитесь, что в разделе **Состояние** задано значение **Вкл**.
1. Выберите метрики для служб, за которыми вы хотите наблюдать.
1. Укажите политику хранения, чтобы задать период хранения метрик и данных журналов.
1. Щелкните **Сохранить**.

Сейчас [портал Azure](https://portal.azure.com) не позволяет настраивать минутные метрики в учетной записи хранения. Их необходимо включить с помощью PowerShell или программно.

## <a name="how-to-enable-metrics-using-powershell"></a>Включение метрик с помощью PowerShell
Чтобы настроить метрики хранилища для учетной записи хранения, можно использовать PowerShell на локальном компьютере, выполнив командлет Azure PowerShell Get-AzureStorageServiceMetricsProperty для получения текущих настроек и командлет Set-AzureStorageServiceMetricsProperty для их изменения.

Командлеты, позволяющие управлять метриками хранилища, имеют следующие параметры.

* Возможными значениями MetricsType являются Hour и Minute.
* Возможными значениями ServiceType являются Blob, Queue и Table.
* Возможными значениями MetricsLevel являются None, Service и ServiceAndApi.

Например, следующая команда выключает минутные метрики для службы BLOB-объектов в учетной записи хранения по умолчанию с установкой пятидневного периода удержания:

```powershell
Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`
```

Следующая команда получает текущий уровень часовых метрик и длительность периода хранения в днях для службы BLOB-объектов в учетной записи хранения по умолчанию:

```powershell
Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob
```

Дополнительные сведения о настройке командлетов Azure PowerShell для работы с подпиской Azure и о выборе учетной записи хранения по умолчанию см. в разделе [Установка и настройка Azure PowerShell](/powershell/azure/overview).

## <a name="how-to-enable-storage-metrics-programmatically"></a>Как программно включить метрики хранилища
В следующем фрагменте кода C# показано, как включить метрики и ведение журнала для службы BLOB-объектов с помощью клиентской библиотеки хранилища для .NET.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

// Create service client for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Enable Storage Analytics logging and set retention policy to 10 days.
ServiceProperties properties = new ServiceProperties();
properties.Logging.LoggingOperations = LoggingOperations.All;
properties.Logging.RetentionDays = 10;
properties.Logging.Version = "1.0";

// Configure service properties for metrics. Both metrics and logging must be set at the same time.
properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.HourMetrics.RetentionDays = 10;
properties.HourMetrics.Version = "1.0";

properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.MinuteMetrics.RetentionDays = 10;
properties.MinuteMetrics.Version = "1.0";

// Set the default service version to be used for anonymous requests.
properties.DefaultServiceVersion = "2015-04-05";

// Set the service properties.
blobClient.SetServiceProperties(properties);
```

## <a name="viewing-storage-metrics"></a>Просмотр метрик хранилища
После настройки метрик аналитики хранилища для мониторинга учетной записи хранения функция аналитики хранилища записывает метрики в набор известных таблиц в учетной записи хранения. Вы можете настроить диаграммы для просмотра почасовых метрик на [портале Azure](https://portal.azure.com).

1. Войдите в свою учетную запись хранения на [портале Azure](https://portal.azure.com).
1. Щелкните **Метрики** в области **Меню** для службы, метрики которой вы хотите просмотреть.
1. Щелкните **Изменить** на диаграмме, которую нужно настроить.
1. В области **Изменение диаграммы** щелкните **Диапазон времени**, **Тип диаграммы** и метрики, которые необходимо отобразить на диаграмме.
1. Нажмите кнопку **ОК**.

Чтобы скачать метрики для длительного хранения или анализа в локальной среде, требуется сделать следующее.

* Используйте средство, которое поддерживает эти таблицы, а также позволяет просматривать и скачивать их.
* Напишите пользовательское приложение или сценарий для чтения и хранения этих таблиц.

Многие сторонние инструменты обзора хранилищ поддерживают эти таблицы и позволяют просматривать их напрямую.
Список доступных инструментов указан в разделе [Клиентские инструменты службы хранилища Azure](storage-explorers.md).

> [!NOTE]
> Начиная с [Microsoft Azure Storage Explorer](http://storageexplorer.com/) версии 0.8.0 можно просматривать и скачивать таблицы метрик аналитики.
> 
> 

Чтобы получить доступ к таблицам аналитики программными средствами, обратите внимание, что таблицы аналитики не отображаются в списке всех таблиц в учетной записи хранения. Можно обратиться к ним непосредственно по имени или использовать [API CloudAnalyticsClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.analytics.cloudanalyticsclient.aspx) в клиентской библиотеке .NET для запроса имен таблиц.

### <a name="hourly-metrics"></a>Часовые метрики
* $MetricsHourPrimaryTransactionsBlob
* $MetricsHourPrimaryTransactionsTable
* $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Минутные метрики
* $MetricsMinutePrimaryTransactionsBlob
* $MetricsMinutePrimaryTransactionsTable
* $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>Capacity
* $MetricsCapacityBlob

Подробные сведения о схемах для этих таблиц см. в разделе [Схема таблицы метрик аналитики хранилища](https://msdn.microsoft.com/library/azure/hh343264.aspx). В примере строк ниже отражена только часть доступных столбцов, однако он иллюстрирует некоторые важные возможности сохранения метрик с помощью метрик хранилища:

| PartitionKey | RowKey | Timestamp | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Доступность | AverageE2ELatency | AverageServerLatency | PercentSuccess |
| --- |:---:| ---:| --- | --- | --- | --- | --- | --- | --- | --- |
| 20140522T1100 |user;All |2014-05-22T11:01:16.7650250Z |7 |7 |4003 |46801 |100 |104.4286 |6.857143 |100 |
| 20140522T1100 |user;QueryEntities |2014-05-22T11:01:16.7640250Z |5 |5 |2694 |45951 |100 |143.8 |7.8 |100 |
| 20140522T1100 |user;QueryEntity |2014-05-22T11:01:16.7650250Z |1 |1 |538 |633 |100 |3 |3 |100 |
| 20140522T1100 |user;UpdateEntity |2014-05-22T11:01:16.7650250Z |1 |1 |771 |217 |100 |9 |6 |100 |

В этом примере данных минутной метрики для ключа раздела используется время с минутным разрешением. Ключ строки определяет тип информации, хранящейся в строке. Ключ строки состоит из двух частей, типа доступа и тип запроса.

* Типом доступа является либо user, либо system, где user означает все запросы пользователей к службе хранилища, а system означает запросы, выполненные с помощью аналитики хранилища.
* Типом запроса является либо all, в случае чего это строка сводки, либо он определяет конкретный API, например QueryEntity или UpdateEntity.

В примере данных выше показаны все записи за одну минуту (начиная с 11:00). Таким образом, если сложить количество запросов QueryEntities, количество запросов QueryEntity и количество запросов UpdateEntity, в сумме получится семь, и эта сумма отображается в строке user:All. Аналогичным образом можно определить среднюю сквозную задержку 104.4286 в строке user:All, вычислив ((143,8 * 5) + 3 + 9)/7.

## <a name="metrics-alerts"></a>Оповещения метрик
Рекомендуется настроить оповещения на [портале Azure](https://portal.azure.com), чтобы метрики хранилища могли автоматически уведомлять вас о всех важных изменениях в поведении служб хранилища. Если скачивание этих метрик в формате с разделителями происходит с помощью средства обозревателя хранилища, для анализа данных можно использовать Microsoft Excel. Список доступных инструментов Storage Explorer см. в разделе [Клиентские инструменты службы хранилища Azure](storage-explorers.md). Можно настроить оповещения в области **Правила оповещения**, доступной в разделе **Мониторинг** в области меню учетной записи хранения.

> [!IMPORTANT]
> Возможна задержка между событием службы хранилища и записью данных соответствующей ежечасной или ежеминутной метрики. При ведении журнала минутных метрик данные за несколько минут могут записываться одновременно. Это может привести к объединению транзакций за предшествующие минуты с транзакциями за текущую минуту. В этом случае у службы оповещений могут быть данные не всех доступных метрик за заданный интервал оповещения, что может привести к неожиданному срабатыванию оповещений.
>

## <a name="accessing-metrics-data-programmatically"></a>Программный доступ к данным метрик
В следующем списке показан пример кода на C#, в котором реализован доступ к минутным метрикам для диапазона минут с отображением результатов в окне консоли. В данном случае используется библиотека хранилища Azure версии 4, включающая класс CloudAnalyticsClient, упрощающий доступ к таблицам метрик в хранилище.

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
{
    // Convert the dates to the format used in the PartitionKey
    var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");

    var services = Enum.GetValues(typeof(StorageService));
    foreach (StorageService service in services)
    {
        Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);
        var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);
        var t = analyticsClient.GetMinuteMetricsTable(service);
        var opContext = new OperationContext();
        var query =
          from entity in metricsQuery
          // Note, you can't filter using the entity properties Time, AccessType, or TransactionType
          // because they are calculated fields in the MetricsEntity class.
          // The PartitionKey identifies the DataTime of the metrics.
          where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0
        select entity;

        // Filter on "user" transactions after fetching the metrics from Table Storage.
        // (StartsWith is not supported using LINQ with Azure table storage)
        var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));
        var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();
        Console.WriteLine(resultString);
    }
}

private static string MetricsString(MetricsEntity entity, OperationContext opContext)
{
    var entityProperties = entity.WriteEntity(opContext);
    var entityString =
      string.Format("Time: {0}, ", entity.Time) +
      string.Format("AccessType: {0}, ", entity.AccessType) +
      string.Format("TransactionType: {0}, ", entity.TransactionType) +
      string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));
    return entityString;
}
```

## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>Какова стоимость включения метрик хранилища?
За запросы записи на создание сущностей таблиц для метрик взимается плата в соответствии со стандартными тарифами, применимыми ко всем операциям службы хранилища Azure.

К запросам на чтение и удаление, формируемым клиентом в отношении данных метрик, также применяются стандартные тарифы. Если вы настроили политику хранения данных, за удаление старых данных метрик хранилищем Azure плата не взимается. Однако при удалении данных аналитики с учетной записи будет взиматься плата за операции удаления.

За пространство, занимаемое таблицами метрик, также взимается плата. Оценить объем пространства, используемый для хранения данных метрик, можно следующим образом:

* Если за каждый час служба использует каждый API в каждой службе, то каждый час в таблицах транзакций метрик сохраняется примерно 148 КБ данных, если сводка охватывает уровень службы и уровень API.
* Если за каждый час служба использует каждый API в каждой службе, то каждый час в таблицах транзакций метрик сохраняется примерно 12 КБ данных, если сводка охватывает только уровень службы.
* В таблице емкости больших двоичных объектов ежедневно добавляются две строки (если пользователь выбрал использование журналов). При этом размер этой таблицы ежедневно увеличивается примерно на 300 байтов.

## <a name="next-steps"></a>Дополнительная информация
[Включение ведения журнала и доступа к данным журнала хранилища](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data)
