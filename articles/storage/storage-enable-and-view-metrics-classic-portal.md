<properties 
	pageTitle="Включение метрик хранилища на портале Azure | Microsoft Azure" 
	description="Как включить метрики хранилища для служб больших двоичных объектов, очередей, таблиц и файлов." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/01/2015" 
	ms.author="tamram"/>

# Включение метрик хранилища и просмотр данных метрик

[AZURE.INCLUDE [storage-selector-portal-enable-and-view-metrics](../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## Обзор

По умолчанию для служб хранилища метрики хранилища не включены. Вы можете включить наблюдение с помощью [классического портала Azure](manage.windowsazure.com), Windows PowerShell или программно с помощью интерфейса API хранилища.

При включении метрик хранилища необходимо выбрать период хранения данных. Он определяет длительность хранения метрик службой хранилища и размер оплаты пространства, необходимого для их хранения. Как правило, следует использовать менее длительный период хранения для минутных метрик, нежели для часовых метрик, так как минутные метрики требуют значительно большего пространства. Следует выбрать такой период хранения, чтобы было достаточно времени на анализ данных и скачивание метрик, которые требуется сохранить локально для анализа или составления отчета. Помните, что за скачивание данных метрик из учетной записи хранения также взимается плата.

## Как включить метрики хранилища с помощью классического портала Azure

На [классическом портале Azure](manage.windowsazure.com) управление метриками хранилища осуществляется на странице «Настройка» для определенной учетной записи хранения. Для выполнения мониторинга можно задать уровень и период хранения (в днях) для каждого большого двоичного объекта, таблицы и очереди. В каждом из случаев используется один из следующих уровней:

- «Выключено» — сбор метрик не осуществляется.

- Минимальный — метрики хранилища собирают базовый набор таких метрик, как исходящие и входящие данные, доступность, задержка и процент успешных операций, который агрегируется для служб BLOB-объектов, таблиц и очередей.

- «Подробный» — осуществляется сбор полного набора метрик, включая метрики для каждой операции API хранилища, а также метрик уровня службы. Подробные метрики позволяют осуществлять более тщательный анализ проблем, возникающих во время работы приложений.

Обратите внимание, что в настоящее время классический портал Azure не позволяет настраивать минутные метрики в учетной записи хранения. Их необходимо включить с помощью PowerShell или программно.


## Как включить метрики хранилища с помощью PowerShell

Чтобы настроить метрики хранилища для учетной записи хранения, можно использовать PowerShell на локальном компьютере, выполнив командлет Azure PowerShell Get-AzureStorageServiceMetricsProperty для получения текущих настроек и командлет Set-AzureStorageServiceMetricsProperty для их изменения.

Командлеты, позволяющие управлять метриками хранилища, имеют следующие параметры.

- Возможными значениями MetricsType являются Hour и Minute.

- Возможными значениями ServiceType являются Blob, Queue и Table.

- Возможными значениями MetricsLevel являются None (эквивалентно значению «Выключено» на классическом портале Azure), Service (эквивалентно значению «Минимальный» на классическом портале Azure) и ServiceAndApi (эквивалентно значению «Подробный» на классическом портале Azure).

Например, следующая команда выключает минутные метрики для службы BLOB-объектов в учетной записи хранения по умолчанию с установкой пятидневного периода хранения:

`Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`

Следующая команда получает текущий уровень часовых метрик и длительность периода хранения в днях для службы BLOB-объектов в учетной записи хранения по умолчанию:

`Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob`

Дополнительную информацию о настройке командлетов Azure PowerShell для работы с подпиской Azure и о выборе учетной записи хранения по умолчанию см. в разделе [Установка и настройка Azure PowerShell](../install-configure-powershell.md).

## Как программно включить метрики хранилища

В следующем фрагменте кода C# показано, как включить метрики и ведение журнала для службы BLOB-объектов с помощью клиентской библиотеки хранилища для .NET.

	// Parse connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

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
    
## Просмотр метрик хранилища

После настройки метрик хранилища для мониторинга учетной записи хранения метрики записываются в набор известных таблиц в учетной записи хранения. Просматривать часовые метрики по мере их появления на диаграмме можно на странице «Монитор» учетной записи хранения на классическом портале Azure. На этой странице классического портала Azure можно:

- Выбирать метрики для отображения на диаграмме (набор доступных метрик зависит от того, какой тип мониторинга службы выбран на странице «Настройка»: подробный или минимальный).


- Выбирать интервал времени для метрик, отображаемых на диаграмме.


- Выбирать абсолютную или относительную шкалу для построения метрик.


- Настраивать оповещения по электронной почте, чтобы получать уведомления при достижении определенного значения конкретной метрики.


Чтобы скачивать метрики для длительного хранения или анализа в локальной среде, необходимо использовать соответствующий инструмент или написать код для чтения таблиц. Необходимо скачать минутные метрики для анализа. Таблицы не отображаются, если в учетной записи хранения выведены все таблицы. Однако к ним можно обращаться напрямую по имени. Многие сторонние инструменты обзора хранилищ поддерживают эти таблицы и позволяют просматривать их непосредственно (см. список доступных инструментов в записи блога [Обозреватели службы хранилища Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)).

### Часовые метрики
- $MetricsHourPrimaryTransactionsBlob
- $MetricsHourPrimaryTransactionsTable
- $MetricsHourPrimaryTransactionsQueue

### Минутные метрики
- $MetricsMinutePrimaryTransactionsBlob
- $MetricsMinutePrimaryTransactionsTable
- $MetricsMinutePrimaryTransactionsQueue

### Емкость
- $MetricsCapacityBlob

Подробные сведения о схемах для этих таблиц см. в разделе [Схема таблицы метрик аналитики хранилища](https://msdn.microsoft.com/library/azure/hh343264.aspx). В примере строк ниже отражена только часть доступных столбцов, однако он иллюстрирует некоторые важные возможности сохранения метрик с помощью метрик хранилища:

| PartitionKey | RowKey | Timestamp | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Доступность | AverageE2ELatency | AverageServerLatency | PercentSuccess |
|---------------|:------------------:|-----------------------------:|---------------|-----------------------|--------------|-------------|--------------|-------------------|----------------------|----------------|
| 20140522T1100 | user;All | 2014-05-22T11:01:16.7650250Z | 7 | 7 | 4003 | 46801 | 100 | 104\.4286 | 6\.857143 | 100 |
| 20140522T1100 | user;QueryEntities | 2014-05-22T11:01:16.7640250Z | 5 | 5 | 2694 | 45951 | 100 | 143\.8 | 7\.8 | 100 |
| 20140522T1100 | user;QueryEntity | 2014-05-22T11:01:16.7650250Z | 1 | 1 | 538 | 633 | 100 | 3 | 3 | 100 |
| 20140522T1100 | user;UpdateEntity | 2014-05-22T11:01:16.7650250Z | 1 | 1 | 771 | 217 | 100 | 9 | 6 | 100 |

В этом примере данных минутной метрики для ключа раздела используется время с минутным разрешением. Ключ строки определяет тип данных, которые хранятся в строке, и состоит из двух фрагментов данных — типа доступа и типа запроса:

- Типом доступа является либо user, либо system, где user означает все запросы пользователей к службе хранилища, а system означает запросы, выполненные с помощью аналитики хранилища.

- Типом запроса является либо all, в случае чего это строка сводки, либо он определяет конкретный API, например QueryEntity или UpdateEntity.


В примере данных выше показаны все записи за одну минуту (начиная с 11:00). Таким образом, если сложить количество запросов QueryEntities, количество запросов QueryEntity и количество запросов UpdateEntity, в сумме получится семь, и эта сумма отображается в строке user:All. Аналогичным образом можно определить среднюю сквозную задержку 104.4286 в строке user:All, вычислив ((143,8 * 5) + 3 + 9)/7.

Рекомендуется настроить оповещения на странице «Монитор» классического портала Azure, чтобы автоматически получать уведомления от метрик хранилища в случае внесения важных изменений в поведение служб хранилища. Если данные метрик скачиваются в формате с разделителями через обозреватель хранилища, их можно анализировать с помощью Microsoft Excel. Список доступных инструментов обозревателя хранилища см. в записи блога [Обозреватели службы хранилища Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx).



## Программный доступ к данным метрик

В следующем списке показан пример кода на C#, в котором реализован доступ к минутным метрикам для диапазона минут с отображением результатов в окне консоли. В данном случае используется библиотека хранилища Azure версии 4, включающая класс CloudAnalyticsClient, упрощающий доступ к таблицам метрик в хранилище.

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




## Какова стоимость включения метрик хранилища?

За запросы записи на создание сущностей таблиц для метрик взимается плата в соответствии со стандартными тарифами, применимыми ко всем операциям службы хранилища Azure.

К запросам на чтение и удаление, формируемым клиентом в отношении данных метрик, также применяются стандартные тарифы. Если вы настроили политику хранения данных, за удаление старых данных метрик хранилищем Azure плата не взимается. Однако при удалении данных аналитики с учетной записи будет взиматься плата за операции удаления.

За пространство, занимаемое таблицами метрик, также взимается плата. Оценить объем пространства, используемый для хранения данных метрик, можно следующим образом:

- Если за каждый час служба использует каждый API в каждой службе, то каждый час в таблицах транзакций метрик сохраняется примерно 148 КБ данных, если сводка охватывает уровень службы и уровень API.

- Если за каждый час служба использует каждый API в каждой службе, то каждый час в таблицах транзакций метрик сохраняется примерно 12 КБ данных, если сводка охватывает только уровень службы.

- В таблице емкости больших двоичных объектов ежедневно добавляются две строки (если пользователь выбрал использование журналов). При этом размер этой таблицы ежедневно увеличивается примерно на 300 байтов.

## Дальнейшие действия:
[Включение ведения журнала аналитики хранилища и доступ к данным журнала](https://msdn.microsoft.com/library/dn782840.aspx)
 

<!---HONumber=AcomDC_1203_2015-->