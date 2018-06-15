---
title: Журнал диагностики производительности Intelligent Insights для базы данных SQL Azure | Документация Майкрософт
description: Intelligent Insights предоставляет журнал диагностики проблем производительности базы данных SQL Azure.
services: sql-database
author: danimir
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: v-daljep
ms.openlocfilehash: 3d1a9045ed89bd3e5714762add7cb9568c46e44f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648247"
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Использование журнала диагностики производительности Intelligent Insights для базы данных SQL Azure

Эта страница содержит сведения об использовании журнала диагностики производительности базы данных SQL Azure, созданного [Intelligent Insights](sql-database-intelligent-insights.md), о его формате и содержащихся в нем данных для различных задач разработки. Этот журнал диагностики можно отправить в [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md), [концентраторы событий Azure](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [службу хранилища Azure](sql-database-metrics-diag-logging.md#stream-into-storage) или стороннее решение для разработки настраиваемых функций оповещения и отчетности в соответствии с процедурами DevOps.

## <a name="log-header"></a>Заголовок журнала

Журнал диагностики использует стандартный формат JSON для вывода результатов Intelligent Insights. Для доступа к журналу Intelligent Insights используется фиксированное значение свойства категории "SQLInsights".

Заголовок журнала является типичным и состоит из метки времени (TimeGenerated), которая показывает, когда была создана запись. Он также включает идентификатор ресурса (ResourceId), который относится к конкретной базе данных SQL, с которой связана запись. Категория (Category), уровень (Level) и имя операции (OperationName) являются фиксированными свойствами, значения которых не изменяются. Они указывают, что запись журнала является информационной и что она сгенерирована в Intelligent Insights (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>ИД проблемы и затронутая база данных

Свойство идентификации проблемы (issueId_d) позволяет однозначно отслеживать проблемы с производительностью вплоть до их устранения. Intelligent Insights рассматривает жизненный цикл каждой проблемы следующим образом: активность, проверка и завершение. В рамках каждого из этих этапов состояния Intelligent Insights может записать в журнал несколько записей о событиях. Для каждой из этих записей идентификатор проблемы будет уникальным. Intelligent Insights отслеживает проблему на протяжении ее жизненного цикла и добавляет аналитические сведения в журнал диагностики каждые 15 минут.

После выявления проблемы с производительностью и до тех пор, пока она существует, свойство состояния (status_s) имеет значение "Active" (Активная). После устранения проблемы выполняется ее проверка и ее свойству состояния (status_s) присваивается значение "Verifying" (Проверка). Если проблема устранена, свойству состояния (status_s) присваивается значение "Complete" (Завершено).

Помимо идентификатора проблемы в журнал диагностики записываются метки времени начала (intervalStartTime_t) и окончания (intervalEndTme_t) определенного события, связанного с проблемой.

Свойство эластичного пула (elasticPoolName_s) указывает, какому эластичному пулу принадлежит база данных, в которой обнаружена проблема. Если база данных не является частью эластичного пула, этому свойству значение не присваивается. База данных, в которой выявлена проблема, указывается в свойстве имени базы данных (databaseName_s).

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Обнаруженные проблемы

Следующий раздел журнала производительности Intelligent Insights содержит проблемы с производительностью, выявленные с помощью встроенного искусственного интеллекта. Выявленные проблемы указываются в свойствах в журнале диагностики формата JSON вместе с категорией проблемы, ее влиянием, затронутыми запросами и метриками. Свойства обнаружений могут содержать несколько выявленных проблем с производительностью.

Выявленные проблемы с производительностью отображаются посредством следующей структуры свойства обнаружений.

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

В следующей таблице приведены выявляемые шаблоны снижения производительности и сведения, выводимые в журнал диагностики.

### <a name="detection-category"></a>Категория выявленной проблемы

Свойство категории (category) описывает категорию выявляемых шаблонов снижения производительности. В следующей таблице приведены все возможные категории выявляемых шаблонов снижения производительности. Дополнительные сведения см. в статье [Устранение проблем с производительностью базы данных SQL Azure с помощью Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).

Сведения, выводимые в файл журнала диагностики, зависят от выявленной проблемы с производительностью.

| Выявляемые шаблоны снижения производительности | Выводимые сведения |
| :------------------- | ------------------- |
| достижение лимитов ресурсов; | <li>Затронутые ресурсы</li><li>Хэши запросов</li><li>Процент потребления ресурсов</li> |
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
| Снижение эффективности плана | <li>Хэши запросов</li><li>Идентификаторы высокоэффективных планов</li><li>Идентификаторы низкоэффективных планов</li> |
| Изменение значения конфигурации уровня базы данных | <li>Изменения конфигурации уровня базы данных по сравнению со значениями по умолчанию</li> |
| Медленная работа клиента | <li>Хэши запросов</li><li>Время ожидания</li> |
| Переход на более низкую ценовую категорию | <li>Текстовое уведомление</li> |

### <a name="impact"></a>Влияние

Свойство влияния (impact) показывает, насколько база данных подвержена выявленной проблеме по шкале от 1 до 3, где 3 — это наибольшая подверженность, 2 — умеренная, а 1 — наименьшая. Значение impact может использоваться в качестве входных данных для автоматизации настраиваемых оповещений, в зависимости от конкретных потребностей. Свойство затронутых запросов (QueryHashes) содержит список хэшей запросов, затронутых выявленной проблемой.

### <a name="impacted-queries"></a>Затронутые запросы

Следующий раздел журнала Intelligent Insights содержит сведения о конкретных запросах, которые были затронуты выявленной проблемой с производительностью. Эти сведения предоставляются в виде массива объектов, внедренного в свойство impact_s. Свойство влияния состоит из сущностей и метрик. Сущности относятся к определенному запросу (Type: Query). Уникальный хэш запроса указывается в свойстве значения (Value). Кроме того, каждый указанный запрос сопровождается метрикой и значением, которые описывают выявленную проблему с производительностью.

В следующем примере журнала было обнаружено, что время выполнения запроса с хэшем 0x9102EXZ4 увеличилось (Metric: DurationIncreaseSeconds). Значение 110 секунд означает, что этот запрос выполнялся на 110 секунд дольше. Так как могут быть выявлены несколько запросов, этот раздел журнала может содержать несколько записей о запросах.

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

Единицы измерения каждой полученной метрики предоставляются в свойстве метрики (metric). Его возможные значения: seconds, number и percentage. Значение измеряемой метрики указывается в свойстве значения (value).

Свойство DurationIncreaseSeconds указывается в секундах, а для CriticalErrorCount единицей измерения является число, представляющее собой количество ошибок.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Анализ первопричин и рекомендации по повышению производительности

Последняя часть журнала производительности Intelligent Insights содержит результат автоматического анализа первопричин выявленной проблемы снижения производительности. Эта информация отображается в понятном виде в свойстве анализа первопричин (rootCauseAnalysis_s). Везде, где это возможно, в журнале содержатся рекомендации по улучшению.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Этот журнал производительности Intelligent Insights можно передать в [Azure Log Analytics]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) или стороннее решение для разработки настраиваемых функций оповещения и отчетности в соответствии с процедурами DevOps.

## <a name="next-steps"></a>Дополнительная информация
- Ознакомьтесь с понятиями [Intelligent Insights](sql-database-intelligent-insights.md).
- Узнайте как [устранять проблемы с производительностью базы данных SQL Azure с помощью Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
- Ознакомьтесь с [мониторингом базы данных SQL Azure с помощью службы "Аналитика SQL Azure"](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql).
- Узнайте, как [собирать и использовать данные журнала из ресурсов Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).



