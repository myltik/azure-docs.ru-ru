---
title: "Журнал диагностики производительности Intelligent Insights для базы данных SQL Azure | Документация Майкрософт"
description: "Intelligent Insights предоставляет журнал диагностики проблем производительности базы данных SQL Azure."
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
editor: carlrab
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/25/2017
ms.author: v-daljep
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 4890baa4ead3323834a82b3f9340cf751bf0c755
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---

# <a name="use-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Использование журнала диагностики производительности Intelligent Insights для базы данных SQL Azure

Эта страница содержит сведения об использовании журнала диагностики производительности базы данных SQL Azure, созданного [Intelligent Insights](sql-database-intelligent-insights.md), его формате и данных для различных задач разработки, которые он содержит. Этот журнал диагностики можно отправить в [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md), [концентратор событий Azure](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [службу хранилища Azure](sql-database-metrics-diag-logging.md#stream-into-azure-storage) или стороннее решение для разработки настраиваемых функций оповещения и создания отчетов в соответствии с процедурами DevOps.

## <a name="log-header"></a>Заголовок журнала

Журнал диагностики использует стандартный формат JSON для вывода результатов Intelligent Insights. Для доступа к журналу Intelligent Insights используется точное фиксированное значение свойства категории, &#8220;**SQLInsights**&#8221;.

Заголовок журнала является общим и состоит из метки времени (TimeGenerated) создания записи и идентификатора ресурса (ResourceId), относящегося к определенной базе данных SQL Azure, к которой относится запись. Категория (Category), уровень (Level) и имя операции (OperationName) являются фиксированными свойствами, значение которых не изменяется. Они указывают, что запись журнала является информационной и ее источником является Intelligent Insights (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>ИД проблемы и затронутая база данных

Свойства идентификации проблемы (issueId_d) позволяет однозначно отслеживать проблемы с производительностью вплоть до их устранения. Intelligent Insights рассматривает жизненный цикл каждой проблемы следующим образом: активность, проверка и завершение. С помощью этих этапов состояния Intelligent Insights может записать в журнал несколько записей о событиях, и идентификатор проблемы каждой из этих записей будет уникальным. Intelligent Insights отслеживает проблему на протяжении ее жизненного цикла и добавляет аналитику в журнал диагностики каждые 15 минут.

После обнаружения продолжительной проблемы с производительностью она отображается как "Active" (Активная) в свойстве состояния (Status). После устранения обнаруженной проблемы выполняется ее проверка и ее свойству состояния (Status) присваивается значение "Verifying" (Проверка). Если проблема устранена, свойству состояния (Status) присваивается значение "Completed" (Завершено).
Помимо идентификатора проблемы в журнал диагностики записываются метки времени начала (intervalStartTime_t) и окончания (intervalEndTme_t) определенного события, связанного с проблемой.

Свойство эластичного пула (elasticPoolName_s) указывает, какому эластичному пулу принадлежит база данных, в которой обнаружена проблема. Если базы данных не является частью эластичного пула, это свойство не имеет значения. База данных, в которой обнаружена проблема, указывается в свойстве имени базы данных (databaseName_s).

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported timestamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported timestamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: Active, Verifying and Complete
```

## <a name="detected-issues"></a>Обнаруженные проблемы

Следующий раздел журнала производительности Intelligent Insights содержит проблемы с производительностью, обнаруженные с помощью встроенного искусственного интеллекта. Обнаруженные проблемы указываются в свойстве обнаружений в формате JSON, содержащем категорию проблемы, последствия проблемы, затронутые запросы и метрики. Следует отметить, что свойство обнаружений может содержать несколько обнаруженных проблем с производительностью.

Выявленные проблемы с производительностью отображаются посредством следующей структуры свойства обнаружений.

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // tperformance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

В следующей таблице приведены шаблоны снижения производительности и сведения, выводимые в журнал диагностики.

### <a name="detection-category"></a>Категория обнаружения

Свойство категории (category) описывает категорию выявляемых шаблонов снижения производительности. В следующей таблице приведены все возможные категории выявляемых шаблонов снижения производительности. Дополнительные сведения можно найти на странице [Устранение проблем с производительностью базы данных SQL Azure с помощью Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).

Сведения, выводимые в файл журнала диагностики, зависят от обнаруженной проблемы с производительностью.

| Выявляемые шаблоны снижения производительности | Выводимые сведения |
| :------------------- | ------------------- |
| Достижение лимитов ресурсов | <li>Затронутые ресурсы</li><li>Хэши запросов</li><li>Процент потребления ресурсов</li> |
| Увеличение рабочей нагрузки | <li>Количество запросов, объем выполнения которых увеличился</li><li>Хэши запросов, больше всего повлиявших на увеличение рабочей нагрузки</li> |
| Нехватка памяти | <li>Клерк памяти</li> |
| Блокировка | <li>Хэши затронутых запросов</li><li>Хэши блокирующих запросов</li> |
| Повышенное значение MAXDOP | <li>Хэши запросов</li><li>Время ожидания CXP</li><li>Время ожидания</li> |
| Состязание за кратковременную блокировку страниц | <li>Хэши запросов, которые приводят к состязанию</li> |
| Отсутствующий индекс | <li>Хэши запросов</li> |
| Новый запрос | <li>Хэши новых запросов</li> |
| Необычная статистика ожидания | <li>Типы необычного времени ожидания</li><li>Хэши запросов</li><li>Время ожидания запросов</li> |
| Состязание за tempdb | <li>Хэши запросов, которые приводят к состязанию</li><li>Сопоставление запросов и общего времени ожидания из-за состязания за кратковременную блокировку страниц базы данных [%]</li> |
| Нехватка DTU эластичного пула | <li>Эластичный пул</li><li>Базы данных, использующие больше всего DTU</li><li>Процент DTU, используемых наиболее ресурсоемким участником пула</li> |
| Снижение эффективности плана | <li>Хэш запроса</li><li>Идентификаторы высокоэффективных планов</li><li>Идентификаторы низкоэффективных планов</li><li>Хэши запросов</li> |
| Изменение значения конфигурации уровня базы данных | <li>Изменение конфигурации уровня базы данных по сравнению со значениями по умолчанию</li> |
| Медленная работа клиента | <li>Хэши запросов</li><li>Время ожидания</li> |
| Переход на более низкую ценовую категорию | <li>Текстовое уведомление</li> |

### <a name="impact"></a>Влияние

Свойство последствий (impact) указывает, насколько база данных подвержена обнаруженной проблеме по шкале от 1 до 3, где 3 — это наибольшая подверженность, 2 — умеренная, а 1 — наименьшая. Значение impact может использоваться в качестве входных данных для автоматизации настраиваемых оповещений, в зависимости от конкретных потребностей. Свойство затронутых запросов (QueryHashes) содержит список хэшей запросов, затронутых обнаруженной проблемой.

### <a name="impacted-queries"></a>Затронутые запросы

Следующий раздел журнала Intelligent Insights содержит сведения о конкретных запросах, которые были затронуты обнаруженной проблемой с производительностью. Эти сведения предоставляются в виде массива объектов, внедренного в свойство impact_s. Свойство последствий состоит из сущностей и метрик. Сущности относятся к определенному запросу (type: Query), а уникальный хэш запроса указывается в свойстве значения (Value). Кроме того, для каждого указанного запроса приводятся метрика и значение, связанные с обнаруженной проблемой с производительностью.

Как можно видеть в следующем примере журнала, было обнаружено, что время выполнения запроса с хэшем 0x9102EXZ4 увеличилось (metric: DurationIncreaseSeconds) на 110 секунд. Это значит, что этот запрос выполнялся на 110 секунд дольше. Следует отметить, что возможно обнаружение нескольких запросов, поэтому этот раздел журнала может содержать несколько записей запросов.

```json
"impact" : [{
"entity" : { 
"Type" : "Query", // type of entity - query
"Value" : "query hash value", // for example "0x9102EXZ4" query hash value },
"Metric" : "DurationIncreaseSeconds", // measured metric and the measurement unit (in this case seconds)
"Value" : 110 // value of the measured metric (in this case seconds)
}]
```

### <a name="metrics"></a>Метрики

Единицы измерения каждой указываемой метрики предоставляются в свойстве метрики (metric). Его возможные значения: seconds, number и percentage. Значение измеренной метрики указывается в свойстве значения (value).

Свойство DurationIncreaseSeconds указывается в секундах, а для CriticalErrorCount единицей измерения является число, представляющее собой количество ошибок.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Анализ первопричин и рекомендации по повышению производительности

Последняя часть журнала производительности Intelligent Insights содержит результат автоматического анализа первопричин обнаруженной проблемы снижения производительности в понятном виде, который указан в свойстве анализа первопричин (rootCauseAnalysis_s). В случаях, когда это возможно, к записанному тексту добавляются рекомендации по повышению производительности.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Этот журнал производительности Intelligent Insights можно передать в [Azure Log Analytics]( https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql) или стороннее решение для разработки настраиваемых функций оповещения и создания отчетов в соответствии с процедурами DevOps.

## <a name="next-steps"></a>Дальнейшие действия
- Изучите основные понятия [Intelligent Insights](sql-database-intelligent-insights.md).
- Изучите [устранение проблем с производительностью базы данных SQL Azure с помощью Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
- Ознакомьтесь с [мониторингом базы данных SQL Azure с помощью службы "Аналитика SQL Azure"](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql).
- Изучите [сбор и использование данных журнала из ресурсов Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).




