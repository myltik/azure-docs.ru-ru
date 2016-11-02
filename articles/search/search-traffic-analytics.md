<properties 
    pageTitle="Аналитика поискового трафика для поиска Azure | Microsoft Azure" 
    description="Включите аналитику поискового трафика для поиска Azure, облачной поисковой службы Microsoft Azure, чтобы получить дополнительные сведения о пользователях и данных." 
    services="search" 
    documentationCenter="" 
    authors="bernitorres" 
    manager="pablocas" 
    editor=""
/>

<tags 
    ms.service="search" 
    ms.devlang="multiple" 
    ms.workload="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="09/23/2016" 
    ms.author="betorres"
/>



# <a name="enabling-and-using-search-traffic-analytics"></a>Включение и использование аналитики поискового трафика

Аналитика поискового трафика — функция поиска Azure, которая позволяет обеспечить видимость службы поиска и получить сведения о пользователях и их поведении. При включении этой функции данные службы поиска копируются в учетную запись хранения по вашему выбору. Эти данные включают журналы службы поиска и сводные рабочие показатели, которые можно обрабатывать и использовать для дальнейшего анализа.

## <a name="how-to-enable-search-traffic-analytics"></a>Включение аналитики поискового трафика

Учетная запись хранения должна находиться в том же регионе и той же подписке, что и служба поиска.

> [AZURE.IMPORTANT] К этой учетной записи хранения применяется стандартная тарификация.

Аналитику поискового трафика можно включить на портале или с помощью PowerShell. В течение 5-10 минут после включения аналитики данные начинают поступать в учетную запись хранения и передаваться в следующие два контейнера больших двоичных объектов:

    insights-logs-operationlogs: search traffic logs
    insights-metrics-pt1m: aggregated metrics


### <a name="a.-using-the-portal"></a>О. С помощью портала
Откройте службу поиска Azure на [портале Azure](http://portal.azure.com). В меню "Параметры" выберите пункт "Поиск аналитики трафика". 

![][1]

Измените состояние на **Включено**, выберите учетную запись хранения Azure, которая будет использоваться, и выберите данные, которые необходимо скопировать: журналы, метрики или и то, и другое. Мы рекомендуем копировать и журналы, и метрику. Вы можете задать политику хранения данных в диапазоне от 1 до 365 дней. Если вы не хотите хранить данные определенное время, задайте значение (количество дней) 0.

![][2]

### <a name="b.-using-powershell"></a>B. с использованием PowerShell.

В первую очередь убедитесь, что у вас установлен последний [пакет командлетов Azure PowerShell](https://github.com/Azure/azure-powershell/releases) .

Затем получите идентификаторы ресурсов для службы поиска и учетной записи хранения. Их можно найти на портале, последовательно выбрав пункты "Параметры" > "Свойства" > "ResourceId".

![][3]

```PowerShell
Login-AzureRmAccount
$SearchServiceResourceId = "Your Search service resource id"
$StorageAccountResourceId = "Your Storage account resource id"
Set-AzureRmDiagnosticSetting -ResourceId $SearchServiceResourceId StorageAccountId $StorageAccountResourceId -Enabled $true
```

## <a name="understanding-the-data"></a>Основные сведения о данных

Данные хранятся в больших двоичных объектах Azure в формате JSON.

Для каждого часа и каждого контейнера создается один большой двоичный объект.
  
Пример пути: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

### <a name="logs"></a>Журналы

Большие двоичные объекты журналов содержат журналы трафика службы поиска.
Каждый большой двоичный объект имеет один корневой объект под названием **records** , который содержит массив объектов журнала.
Каждый большой двоичный объект содержит записи по всем операциям, которые выполнялась в течение одного часа.

####<a name="log-schema"></a>Схема журнала

Имя |Тип |Пример |Примечания 
------|-----|----|-----
time |datetime; |"2015-12-07T00:00:43.6872559Z" |Метка времени операции
resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>  MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |Идентификатор вашего ресурса
operationName |string |"Query.Search" |Имя операции
operationVersion |string |"2015-02-28"|Используемая версия API
category |string |"OperationLogs" |константа 
resultType |string |Success |Возможные значения: Success или Failure 
resultSignature |int |200 |Код результата HTTP 
durationMS |int |50 |Время выполнения операции в миллисекундах 
properties |object |См. следующую таблицу |Объект, содержащий данные об операции

####<a name="properties-schema"></a>Схема свойств

|Имя |Тип |Пример |Примечания|
|------|-----|----|-----|
|Описание|string |"GET /indexes('content')/docs" |Конечная точка операции |
|Запрос |string |"?search=AzureSearch&$count=true&api-version=2015-02-28" |Параметры запроса |
|Документы |int |42 |Количество обработанных документов|
|IndexName |string |"testindex"|Имя индекса, связанного с операцией |

### <a name="metrics"></a>Метрики

Большие двоичные объекты метрики содержат статистические значения для службы поиска. Каждый файл имеет один корневой объект под названием **records** , который содержит массив объектов метрики. Этот корневой объект содержит метрики для каждой минуты, в течение которой данные были доступны. 

Доступные метрики:

- SearchLatency: время, потребовавшееся службе поиска для обработки запросов поиска (данные агрегируются поминутно).
- SearchQueriesPerSecond: число запросов поиска, получаемых в секунду (данные агрегируются поминутно).
- ThrottledSearchQueriesPercentage: процент отрегулированных запросов поиска (данные агрегируются поминутно).

> [AZURE.IMPORTANT] Регулирование происходит при отправке слишком большого количества запросов, превышающего возможности подготовленного ресурса службы. В этом случае следует рассмотреть добавление дополнительных реплик в службу.

####<a name="metrics-schema"></a>Схема метрик

|Имя |Тип |Пример |Примечания|
|------|-----|----|-----|
|resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE"  |Идентификатор вашего ресурса |
|metricName |string |"Latency" |имя метрики |
|time|datetime; |"2015-12-07T00:00:43.6872559Z" |метка времени операции |
|average |int |64|Среднее количество необработанных выборок в течение интервала времени метрики |
|minimum |int |37 |Минимальное количество необработанных выборок в течение интервала времени метрики |
|maximum |int |78 |Максимальное количество необработанных выборок в течение интервала времени метрики |
|total |int |258 |Общее количество необработанных выборок в течение интервала времени метрики |
|count |int |4 |Количество необработанных выборок, использованных для создания метрики |
|timegrain |string |"PT1M" |Интервал времени в формате метрики ISO 8601|

Все метрики передают данные с интервалом в одну минуту. Каждая метрика отражает минимальное, максимальное и среднее значения за минуту.

Для метрики SearchQueriesPerSecond минимальным является наименьшее значение числа поисковых запросов в секунду, которое было зарегистрировано за эту минуту. То же относится и к максимальному значению. Средним является совокупное значение за всю минуту. Представьте такой сценарий: в течение одной минуты может быть одна секунда высокой загрузки, определяющая максимальное значение для SearchQueriesPerSecond, за которой следует 58 секунд средней загрузки, и наконец — одна секунда всего с одним запросом, который определяет минимальное значение.

Для ThrottledSearchQueriesPercentage минимальное, максимальное, среднее и общее значения одинаковы, так как это процент запросов поиска, которые были отрегулированы, от общего числа запросов поиска за одну минуту.

## <a name="analyzing-your-data"></a>Анализ данных

Данные находятся в вашей собственной учетной записи хранения, и мы рекомендуем исследовать их наиболее удобным для вас способом.

В качестве отправной точки рекомендуем использовать [Power BI](https://powerbi.microsoft.com) для изучения и визуализации данных. Вы можете легко подключиться к вашей учетной записи хранения Azure и приступить к анализу данных. 

#### <a name="power-bi-online"></a>Power BI Online

[Пакет содержимого Power BI](https://app.powerbi.com/getdata/services/azure-search): создайте панель мониторинга Power BI и настройте отчеты Power BI, которые будут автоматически отражать ваши данные и визуально представлять результаты работы вашей службы поиска. См. [страницу справки по пакету содержимого](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-search/).

![][4]

#### <a name="power-bi-desktop"></a>Power BI Desktop

[Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop): анализируйте данные и создавайте собственные представления данных. См. стартовый запрос в следующем разделе:

1. Откройте новый отчет PowerBI Desktop.
2. Выберите "Получить данные" -> "Подробнее...".

    ![][5]

3. Выберите "Хранилище больших двоичных объектов Azure" и затем "Подключиться".

    ![][6]

4. Введите "Имя" и "Ключ учетной записи" для своей учетной записи хранения.
5. Выберите строки insight-logs-operationlogs и insights-metrics-pt1m, и нажмите кнопку "Изменить".
6. Когда откроется редактор запросов, убедитесь, что в его левой части выбрана строка insight-logs-operationlogs. Теперь откройте Расширенный редактор, выбрав "Вид" -> "Расширенный редактор".

    ![][7]

7. Сохраните первые две строки, а остальные замените следующим запросом:

    >     #"insights-logs-operationlogs" = Source{[Name="insights-logs-operationlogs"]}[Data],
    >     #"Sorted Rows" = Table.Sort(#"insights-logs-operationlogs",{{"Date modified", Order.Descending}}),
    >     #"Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
    >     #"Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
    >     #"Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
    >     #"Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
    >     #"Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
    >     #"Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}, {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}),
    >     #"Expanded properties" = Table.ExpandRecordColumn(#"Expanded records1", "properties", {"Description", "Query", "IndexName", "Documents"}, {"Description", "Query", "IndexName", "Documents"}),
    >     #"Renamed Columns" = Table.RenameColumns(#"Expanded properties",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"operationName", "OperationName"}, {"operationVersion", "OperationVersion"}, {"category", "Category"}, {"resultType", "ResultType"}, {"resultSignature", "ResultSignature"}, {"durationMS", "Duration"}}),
    >     #"Added Custom2" = Table.AddColumn(#"Renamed Columns", "QueryParameters", each Uri.Parts("http://tmp" & [Query])),
    >     #"Expanded QueryParameters" = Table.ExpandRecordColumn(#"Added Custom2", "QueryParameters", {"Query"}, {"Query.1"}),
    >     #"Expanded Query.1" = Table.ExpandRecordColumn(#"Expanded QueryParameters", "Query.1", {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}, {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}),
    >     #"Removed Columns1" = Table.RemoveColumns(#"Expanded Query.1",{"OperationVersion"}),
    >     #"Changed Type" = Table.TransformColumnTypes(#"Removed Columns1",{{"Datetime", type datetimezone}, {"ResourceId", type text}, {"OperationName", type text}, {"Category", type text}, {"ResultType", type text}, {"ResultSignature", type text}, {"Duration", Int64.Type}, {"Description", type text}, {"Query", type text}, {"IndexName", type text}, {"Documents", Int64.Type}, {"search", type text}, {"$skip", Int64.Type}, {"$top", Int64.Type}, {"$count", type logical}, {"api-version", type text}, {"searchMode", type text}, {"$filter", type text}}),
    >     #"Inserted Date" = Table.AddColumn(#"Changed Type", "Date", each DateTime.Date([Datetime]), type date),
    >     #"Duplicated Column" = Table.DuplicateColumn(#"Inserted Date", "ResourceId", "Copy of ResourceId"),
    >     #"Split Column by Delimiter" = Table.SplitColumn(#"Duplicated Column","Copy of ResourceId",Splitter.SplitTextByEachDelimiter({"/"}, null, true),{"Copy of ResourceId.1", "Copy of ResourceId.2"}),
    >     #"Changed Type1" = Table.TransformColumnTypes(#"Split Column by Delimiter",{{"Copy of ResourceId.1", type text}, {"Copy of ResourceId.2", type text}}),
    >     #"Removed Columns2" = Table.RemoveColumns(#"Changed Type1",{"Copy of ResourceId.1"}),
    >     #"Renamed Columns1" = Table.RenameColumns(#"Removed Columns2",{{"Copy of ResourceId.2", "ServiceName"}}),
    >     #"Lowercased Text" = Table.TransformColumns(#"Renamed Columns1",{{"ServiceName", Text.Lower}}),
    >     #"Added Custom" = Table.AddColumn(#"Lowercased Text", "DaysFromToday", each Duration.Days(DateTimeZone.UtcNow() - [Datetime])),
    >     #"Changed Type2" = Table.TransformColumnTypes(#"Added Custom",{{"DaysFromToday", Int64.Type}})
    >     in
    >     #"Changed Type2"

8. Нажмите кнопку "Готово".

9. Теперь выберите в списке запросов слева строку insights-metrics-pt1m и снова откройте расширенный редактор. Сохраните первые две строки, а остальные замените следующим запросом: 

    >     #"insights-metrics-pt1m1" = Source{[Name="insights-metrics-pt1m"]}[Data],
    >     #"Sorted Rows" = Table.Sort(#"insights-metrics-pt1m1",{{"Date modified", Order.Descending}}),
    >     #"Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
        #"Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
    >     #"Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
    >     #"Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
    >     #"Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
    >     #"Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}, {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}),
    >     #"Filtered Rows" = Table.SelectRows(#"Expanded records1", each ([metricName] = "Latency")),
    >     #"Removed Columns1" = Table.RemoveColumns(#"Filtered Rows",{"timeGrain"}),
    >     #"Renamed Columns" = Table.RenameColumns(#"Removed Columns1",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"metricName", "MetricName"}, {"average", "Average"}, {"minimum", "Minimum"}, {"maximum", "Maximum"}, {"total", "Total"}, {"count", "Count"}}),
    >     #"Changed Type" = Table.TransformColumnTypes(#"Renamed Columns",{{"ResourceId", type text}, {"MetricName", type text}, {"Datetime", type datetimezone}, {"Average", type number}, {"Minimum", Int64.Type}, {"Maximum", Int64.Type}, {"Total", Int64.Type}, {"Count", Int64.Type}}),
    >         Rounding = Table.TransformColumns(#"Changed Type",{{"Average", each Number.Round(_, 2)}}),
    >     #"Changed Type1" = Table.TransformColumnTypes(Rounding,{{"Average", type number}}),
    >     #"Inserted Date" = Table.AddColumn(#"Changed Type1", "Date", each DateTime.Date([Datetime]), type date)
    >     in
        #"Inserted Date"

10. Нажмите кнопку "Готово" и выберите "Закрыть и применить" на вкладке "Главная".

11. Данные за последние 30 дней будут готовы к использованию. Создайте несколько [визуализаций](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-report-view/).

## <a name="next-steps"></a>Дальнейшие действия

Изучите дополнительные сведения о синтаксисе и параметрах поисковых запросов. См. статью [Поиск документов (API REST службы поиска Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx).

Узнайте больше о создании удивительных отчетов. См. статью [Начало работы с Power BI Desktop](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-getting-started/).

<!--Image references-->

[1]: ./media/search-traffic-analytics/SettingsBlade.png
[2]: ./media/search-traffic-analytics/DiagnosticsBlade.png
[3]: ./media/search-traffic-analytics/ResourceId.png
[4]: ./media/search-traffic-analytics/Dashboard.png
[5]: ./media/search-traffic-analytics/GetData.png
[6]: ./media/search-traffic-analytics/BlobStorage.png
[7]: ./media/search-traffic-analytics/QueryEditor.png




<!--HONumber=Oct16_HO2-->


