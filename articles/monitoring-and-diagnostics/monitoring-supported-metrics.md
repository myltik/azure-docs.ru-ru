---
title: "Azure Monitor: поддерживаемые метрики для каждого типа ресурсов | Документация Майкрософт"
description: "Список метрик, доступных для каждого типа ресурса в Azure Monitor."
author: anirudhcavale
manager: ashwink
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 63d4ac65-1688-40d1-85c8-7cd408285b0f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: ancav
ms.openlocfilehash: 673f5a5cd6832adb031ef72ce25f8a1622717cfd
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2017
---
# <a name="supported-metrics-with-azure-monitor"></a>Метрики, поддерживаемые Azure Monitor
Azure Monitor обеспечивает несколько способов взаимодействия с метриками, включая создание диаграмм метрик на портале, доступ к метрикам через REST API или запрос метрик с помощью PowerShell или интерфейса командной строки. Ниже приведен полный список метрик, доступных в настоящее время в конвейере метрик Azure Monitor.

> [!NOTE]
> Другие метрики могут быть доступны на портале или при использовании интерфейсов API прежних версий. Этот список содержит только метрики, которые доступны при использовании объединенного конвейера метрик Azure Monitor. Чтобы запросить метрики с измерениями и получить к ним доступ, воспользуйтесь [предварительной версией API (2017-05-01-preview api-version)](https://docs.microsoft.com/rest/api/monitor/metricdefinitions).
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|qpu_metric|QPU|Количество|Средние|QPU. Диапазоны: 0–100 для S1, 0–200 для S2 и 0–400 для S4.|ServerResourceType|
|memory_metric|Память|Байты|Средние|Память. Диапазоны: 0–25 ГБ для S1, 0–50 ГБ для S2 и 0–100 ГБ для S4.|ServerResourceType|
|TotalConnectionRequests|Общее число запросов на подключение|Количество|Средние|Общее число запросов на подключение. Поступившие запросы.|ServerResourceType|
|SuccessfullConnectionsPerSec|Число успешных подключений в секунду|Число/с|Средние|Частота успешных установок подключений.|ServerResourceType|
|TotalConnectionFailures|Общее число неудачных подключений|Количество|Средние|Общее число неудачных попыток подключения.|ServerResourceType|
|CurrentUserSessions|Текущие пользовательские сеансы|Количество|Средние|Текущее число установленных пользовательских сеансов.|ServerResourceType|
|QueryPoolBusyThreads|Занятые потоки в пуле запросов|Количество|Средние|Число занятых потоков в пуле потоков запросов.|ServerResourceType|
|CommandPoolJobQueueLength|Длина очереди заданий пула команд|Количество|Средние|Число заданий в очереди пула потоков команд.|ServerResourceType|
|ProcessingPoolJobQueueLength|Длина очереди заданий пула обработки|Количество|Средние|Количество заданий, не связанных с вводом-выводом, в очереди пула потоков обработки.|ServerResourceType|
|CurrentConnections|Подключение: текущие подключения|Количество|Средние|Текущее число установленных клиентских подключений.|ServerResourceType|
|CleanerCurrentPrice|Память: текущая цена очистки|Количество|Средние|Текущая цена памяти, $/байт/время, нормализованная до 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Память: сжимаемая память очистки|Байты|Средние|Объем памяти (в байтах), доступной для очистки с помощью средства фоновой очистки.|ServerResourceType|
|CleanerMemoryNonshrinkable|Память: несжимаемая память очистки|Байты|Средние|Объем памяти (в байтах), недоступной для очистки с помощью средства фоновой очистки.|ServerResourceType|
|MemoryUsage|Память: использование памяти|Байты|Средние|Использование памяти процессом сервера согласно вычислениям стоимости памяти очистки. Равно сумме значений счетчика Process\PrivateBytes и объема сопоставленных в памяти данных за вычетом памяти, сопоставленной или выделенной xVelocity в подсистеме, выполняющейся в памяти аналитики (VertiPaq) сверх предела памяти подсистемы VertiPaq.|ServerResourceType|
|MemoryLimitHard|Память: твердый лимит памяти|Байты|Средняя|Твердый лимит памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitHigh|Память: верхний предел памяти|Байты|Средние|Верхняя граница объема памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitLow|Память: нижний предел памяти|Байты|Средние|Нижняя граница объема памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitVertiPaq|Память: ограничение памяти VertiPaq|Байты|Средние|Ограничение памяти, из файла конфигурации.|ServerResourceType|
|Quota|Память: квота|Байты|Средняя|Текущая квота памяти, в байтах. Квота памяти также называется выделением или резервированием памяти.|ServerResourceType|
|QuotaBlocked|Память: заблокировано квот|Количество|Средняя|Текущее число запросов на квоту, заблокированных до высвобождения других квот памяти.|ServerResourceType|
|VertiPaqNonpaged|Память: размер нестраничных данных VertiPaq|Байты|Средние|Размер памяти (в байтах), заблокированной в рабочем множестве для использования в подсистеме памяти.|ServerResourceType|
|VertiPaqPaged|Память: размер страничных данных VertiPaq|Байты|Средние|Размер памяти (в байтах), занятой страничными данными в памяти.|ServerResourceType|
|RowsReadPerSec|Обработка: считано строк в секунду|Число/с|Средние|Интенсивность считывания строк из всех реляционных баз данных.|ServerResourceType|
|RowsConvertedPerSec|Обработка: преобразовано строк в секунду|Число/с|Средняя|Интенсивность преобразованных в ходе обработки строк.|ServerResourceType|
|RowsWrittenPerSec|Обработка: записано строк в секунду|Число/с|Средние|Интенсивность записанных в ходе обработки строк.|ServerResourceType|
|CommandPoolBusyThreads|Потоки: занятые потоки в пуле команд|Количество|Средняя|Число занятых потоков в пуле потоков команд.|ServerResourceType|
|CommandPoolIdleThreads|Потоки: бездействующие потоки в пуле команд|Количество|Средние|Число бездействующих потоков в пуле потоков команд.|ServerResourceType|
|LongParsingBusyThreads|Потоки: занятые потоки продолжительного анализа|Количество|Средние|Число занятых потоков в пуле потоков продолжительного анализа.|ServerResourceType|
|LongParsingIdleThreads|Потоки: бездействующие потоки продолжительного анализа|Количество|Средняя|Число бездействующих потоков в пуле потоков продолжительного анализа.|ServerResourceType|
|LongParsingJobQueueLength|Потоки: длина очереди заданий продолжительного анализа|Количество|Средняя|Число заданий в очереди пула потоков продолжительного анализа.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Потоки: занятые потоки ввода-вывода в пуле обработки заданий|Количество|Средние|Количество потоков, выполняющих задания ввода-вывода, в пуле потоков обработки.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Потоки: занятые потоки в пуле обработки, не связанные с вводом-выводом|Количество|Средние|Количество потоков, выполняющих задания, не связанные с вводом-выводом в пуле потоков обработки.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Потоки: длина очереди заданий ввода-вывода пула обработки|Количество|Средняя|Количество заданий ввода-вывода в очереди пула потоков обработки.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Потоки: бездействующие потоки ввода-вывода в пуле обработки заданий|Количество|Средние|Количество бездействующих потоков, выполняющих задания ввода-вывода, в пуле потоков обработки.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Потоки: бездействующие потоки в пуле обработки, не связанные с вводом-выводом|Количество|Средняя|Количество бездействующих потоков в пуле потоков обработки, выделенных для заданий, не связанных с вводом-выводом.|ServerResourceType|
|QueryPoolIdleThreads|Потоки: бездействующие потоки в пуле запросов|Количество|Средние|Количество бездействующих потоков, выполняющих задания ввода-вывода, в пуле потоков обработки.|ServerResourceType|
|QueryPoolJobQueueLength|Потоки: длина очереди заданий пула запросов|Количество|Средние|Число заданий в очереди пула потоков запросов.|ServerResourceType|
|ShortParsingBusyThreads|Потоки: занятые потоки быстрого анализа|Количество|Средняя|Число занятых потоков в пуле потоков быстрого анализа.|ServerResourceType|
|ShortParsingIdleThreads|Потоки: бездействующие потоки быстрого анализа|Количество|Средние|Число бездействующих потоков в пуле потоков быстрого анализа.|ServerResourceType|
|ShortParsingJobQueueLength|Потоки: длина очереди заданий быстрого анализа|Количество|Средняя|Число заданий в очереди пула потоков быстрого анализа.|ServerResourceType|
|memory_thrashing_metric|Пробуксовка памяти|Процент|Средние|Среднее значение пробуксовки памяти.|ServerResourceType|
|mashup_engine_qpu_metric|QPU подсистемы M|Количество|Средняя|Использование QPU процессами подсистемы гибридных веб-приложений|ServerResourceType|
|mashup_engine_memory_metric|Память подсистемы M|Байты|Средние|Использование памяти процессами подсистемы гибридных веб-приложений|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|TotalRequests|Total Gateway Requests|Количество|Итого|Число запросов к шлюзу|Location, Hostname|
|SuccessfulRequests|Successful Gateway Requests|Количество|Итого|Число успешных запросов к шлюзу|Location, Hostname|
|UnauthorizedRequests|Неавторизованные запросы к шлюзу|Количество|Итого|Число неавторизованных запросов к шлюзу|Location, Hostname|
|FailedRequests|Failed Gateway Requests|Количество|Итого|Количество сбоев при обработке запросов к шлюзу.|Location, Hostname|
|OtherRequests|Другие запросы к шлюзу|Количество|Итого|Количество других запросов к шлюзу|Location, Hostname|
|Duration|Общая длительность запросов шлюза|Миллисекунды|Средняя|Общая длительность запросов шлюза в миллисекундах|Location, Hostname|
|Capacity|Емкость (предварительная версия)|Процент|Максимальная|Метрика использования для службы ApiManagement|Расположение|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|TotalJob|Всего заданий|Количество|Итого|Общее количество заданий|RunbookName состояния|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|CoreCount|Dedicated Core Count|Количество|Итого|Общее число выделенных ядер в учетной записи пакетной службы.|Нет измерений|
|TotalNodeCount|Dedicated Node Count|Количество|Итого|Общее число выделенных узлов в учетной записи пакетной службы.|Нет измерений|
|LowPriorityCoreCount|LowPriority Core Count|Количество|Итого|Общее число низкоприоритетных ядер в учетной записи пакетной службы.|Нет измерений|
|TotalLowPriorityNodeCount|Low-Priority Node Count|Количество|Итого|Общее число низкоприоритетных узлов в учетной записи пакетной службы.|Нет измерений|
|CreatingNodeCount|Creating Node Count|Количество|Итого|Количество создаваемых узлов.|Нет измерений|
|StartingNodeCount|Starting Node Count|Количество|Итого|Число узлов, которые запускаются.|Нет измерений|
|WaitingForStartTaskNodeCount|Waiting For Start Task Node Count|Количество|Итого|Число узлов, ожидающих завершения задачи запуска.|Нет измерений|
|StartTaskFailedNodeCount|Start Task Failed Node Count|Количество|Итого|Число узлов, на которых произошел сбой задачи запуска.|Нет измерений|
|IdleNodeCount|Idle Node Count|Количество|Итого|Количество узлов в неактивном состоянии.|Нет измерений|
|OfflineNodeCount|Offline Node Count|Количество|Всего|Количество узлов не в сети.|Нет измерений|
|RebootingNodeCount|Rebooting Node Count|Количество|Итого|Количество перезапускаемых узлов.|Нет измерений|
|ReimagingNodeCount|Reimaging Node Count|Количество|Итого|Число узлов, для которых пересоздается образ.|Нет измерений|
|RunningNodeCount|Running Node Count|Количество|Итого|Число работающих узлов.|Нет измерений|
|LeavingPoolNodeCount|Leaving Pool Node Count|Количество|Итого|Количество узлов, покидающих пул.|Нет измерений|
|UnusableNodeCount|Unusable Node Count|Количество|Итого|Число узлов, непригодных для использования.|Нет измерений|
|PreemptedNodeCount|Preempted Node Count|Количество|Итого|Количество замещенных узлов.|Нет измерений|
|TaskStartEvent|Task Start Events|Количество|Итого|Общее число запущенных задач.|Нет измерений|
|TaskCompleteEvent|Task Complete Events|Количество|Итого|Общее число завершенных задач.|Нет измерений|
|TaskFailEvent|Task Fail Events|Количество|Итого|Общее число задач, завершившихся сбоем.|Нет измерений|
|PoolCreateEvent|Pool Create Events|Количество|Итого|Общее число созданных пулов.|Нет измерений|
|PoolResizeStartEvent|Pool Resize Start Events|Количество|Итого|Общее число запущенных задач изменения размера пула.|Нет измерений|
|PoolResizeCompleteEvent|Pool Resize Complete Events|Количество|Итого|Общее число завершенных задач изменения размера пула.|Нет измерений|
|PoolDeleteStartEvent|Pool Delete Start Events|Количество|Итого|Общее число запущенных задач удаления пула.|Нет измерений|
|PoolDeleteCompleteEvent|Pool Delete Complete Events|Количество|Итого|Общее число завершенных задач удаления пула.|Нет измерений|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|connectedclients|Подключенные клиенты|Количество|Максимальная||Нет измерений|
|totalcommandsprocessed|Всего операций|Количество|Итого||Нет измерений|
|cachehits|Попаданий в кэш|Количество|Итого||Нет измерений|
|cachemisses|Промахов в кэше|Количество|Итого||Нет измерений|
|getcommands|Операций считывания|Количество|Итого||Нет измерений|
|setcommands|Операций записи|Количество|Итого||Нет измерений|
|evictedkeys|Исключенные ключи|Количество|Итого||Нет измерений|
|totalkeys|Всего ключей|Количество|Максимальная||Нет измерений|
|expiredkeys|Ключи с истекшим сроком действия|Количество|Итого||Нет измерений|
|usedmemory|Используемая память|Байты|Максимальная||Нет измерений|
|usedmemoryRss|RSS используемой памяти|Байты|Максимальная||Нет измерений|
|serverLoad|Загрузка сервера|Процент|Максимальная||Нет измерений|
|cacheWrite|Запись в кэш|Байт/с|Максимальная||Нет измерений|
|cacheRead|Чтение из кэша|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime|ЦП|Процент|Максимальная||Нет измерений|
|connectedclients0|Connected Clients (Shard 0)|Количество|Максимальная||Нет измерений|
|totalcommandsprocessed0|Total Operations (Shard 0)|Количество|Итого||Нет измерений|
|cachehits0|Cache Hits (Shard 0)|Количество|Итого||Нет измерений|
|cachemisses0|Cache Misses (Shard 0)|Количество|Итого||Нет измерений|
|getcommands0|Gets (Shard 0)|Количество|Итого||Нет измерений|
|setcommands0|Sets (Shard 0)|Количество|Итого||Нет измерений|
|evictedkeys0|Evicted Keys (Shard 0)|Количество|Итого||Нет измерений|
|totalkeys0|Total Keys (Shard 0)|Количество|Максимальная||Нет измерений|
|expiredkeys0|Expired Keys (Shard 0)|Количество|Итого||Нет измерений|
|usedmemory0|Used Memory (Shard 0)|Байты|Максимальная||Нет измерений|
|usedmemoryRss0|Used Memory RSS (Shard 0)|Байты|Максимальная||Нет измерений|
|serverLoad0|Server Load (Shard 0)|Процент|Максимальная||Нет измерений|
|cacheWrite0|Cache Write (Shard 0)|Байт/с|Максимальная||Нет измерений|
|cacheRead0|Cache Read (Shard 0)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime0|CPU (Shard 0)|Процент|Максимальная||Нет измерений|
|connectedclients1|Connected Clients (Shard 1)|Количество|Максимальная||Нет измерений|
|totalcommandsprocessed1|Total Operations (Shard 1)|Количество|Итого||Нет измерений|
|cachehits1|Cache Hits (Shard 1)|Количество|Итого||Нет измерений|
|cachemisses1|Cache Misses (Shard 1)|Количество|Итого||Нет измерений|
|getcommands1|Gets (Shard 1)|Количество|Итого||Нет измерений|
|getcommands1|Sets (Shard 1)|Количество|Итого||Нет измерений|
|evictedkeys1|Evicted Keys (Shard 1)|Количество|Итого||Нет измерений|
|totalkeys1|Total Keys (Shard 1)|Количество|Максимальная||Нет измерений|
|expiredkeys1|Expired Keys (Shard 1)|Количество|Итого||Нет измерений|
|usedmemory1|Used Memory (Shard 1)|Байты|Максимальная||Нет измерений|
|usedmemoryRss1|Used Memory RSS (Shard 1)|Байты|Максимальная||Нет измерений|
|serverLoad1|Server Load (Shard 1)|Процент|Максимальная||Нет измерений|
|cacheWrite1|Cache Write (Shard 1)|Байт/с|Максимальная||Нет измерений|
|cacheRead1|Cache Read (Shard 1)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime1|CPU (Shard 1)|Процент|Максимальная||Нет измерений|
|connectedclients2|Connected Clients (Shard 2)|Количество|Максимальная||Нет измерений|
|totalcommandsprocessed2|Total Operations (Shard 2)|Количество|Итого||Нет измерений|
|cachehits2|Cache Hits (Shard 2)|Количество|Итого||Нет измерений|
|cachemisses2|Cache Misses (Shard 2)|Количество|Итого||Нет измерений|
|getcommands2|Gets (Shard 2)|Количество|Итого||Нет измерений|
|getcommands2|Sets (Shard 2)|Количество|Итого||Нет измерений|
|evictedkeys2|Evicted Keys (Shard 2)|Количество|Итого||Нет измерений|
|totalkeys2|Total Keys (Shard 2)|Количество|Максимальная||Нет измерений|
|expiredkeys2|Expired Keys (Shard 2)|Количество|Итого||Нет измерений|
|usedmemory2|Used Memory (Shard 2)|Байты|Максимальная||Нет измерений|
|usedmemoryRss2|Used Memory RSS (Shard 2)|Байты|Максимальная||Нет измерений|
|serverLoad2|Server Load (Shard 2)|Процент|Максимальная||Нет измерений|
|cacheWrite2|Cache Write (Shard 2)|Байт/с|Максимальная||Нет измерений|
|cacheRead2|Cache Read (Shard 2)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime2|CPU (Shard 2)|Процент|Максимальная||Нет измерений|
|connectedclients3|Connected Clients (Shard 3)|Количество|Максимальная||Нет измерений|
|totalcommandsprocessed3|Total Operations (Shard 3)|Количество|Итого||Нет измерений|
|cachehits3|Cache Hits (Shard 3)|Количество|Итого||Нет измерений|
|cachemisses3|Cache Misses (Shard 3)|Количество|Итого||Нет измерений|
|getcommands3|Gets (Shard 3)|Количество|Итого||Нет измерений|
|setcommands3|Sets (Shard 3)|Количество|Итого||Нет измерений|
|evictedkeys3|Evicted Keys (Shard 3)|Количество|Итого||Нет измерений|
|totalkeys3|Total Keys (Shard 3)|Количество|Максимальная||Нет измерений|
|expiredkeys3|Expired Keys (Shard 3)|Количество|Итого||Нет измерений|
|usedmemory3|Used Memory (Shard 3)|Байты|Максимальная||Нет измерений|
|usedmemoryRss3|Used Memory RSS (Shard 3)|Байты|Максимальная||Нет измерений|
|serverLoad3|Server Load (Shard 3)|Процент|Максимальная||Нет измерений|
|cacheWrite3|Cache Write (Shard 3)|Байт/с|Максимальная||Нет измерений|
|cacheRead3|Cache Read (Shard 3)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime3|CPU (Shard 3)|Процент|Максимальная||Нет измерений|
|connectedclients4|Connected Clients (Shard 4)|Количество|Максимальная||Нет измерений|
|totalcommandsprocessed4|Total Operations (Shard 4)|Количество|Итого||Нет измерений|
|cachehits4|Cache Hits (Shard 4)|Количество|Итого||Нет измерений|
|cachemisses4|Cache Misses (Shard 4)|Количество|Итого||Нет измерений|
|getcommands4|Gets (Shard 4)|Количество|Итого||Нет измерений|
|setcommands4|Sets (Shard 4)|Количество|Итого||Нет измерений|
|evictedkeys4|Evicted Keys (Shard 4)|Количество|Итого||Нет измерений|
|totalkeys4|Total Keys (Shard 4)|Количество|Максимальная||Нет измерений|
|expiredkeys4|Expired Keys (Shard 4)|Количество|Итого||Нет измерений|
|usedmemory4|Used Memory (Shard 4)|Байты|Максимальная||Нет измерений|
|usedmemoryRss4|Used Memory RSS (Shard 4)|Байты|Максимальная||Нет измерений|
|serverLoad4|Server Load (Shard 4)|Процент|Максимальная||Нет измерений|
|cacheWrite4|Cache Write (Shard 4)|Байт/с|Максимальная||Нет измерений|
|cacheRead4|Cache Read (Shard 4)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime4|CPU (Shard 4)|Процент|Максимальная||Нет измерений|
|connectedclients5|Connected Clients (Shard 5)|Количество|Максимальная||Нет измерений|
|totalcommandsprocessed5|Total Operations (Shard 5)|Количество|Итого||Нет измерений|
|cachehits5|Cache Hits (Shard 5)|Количество|Итого||Нет измерений|
|cachemisses5|Cache Misses (Shard 5)|Количество|Итого||Нет измерений|
|getcommands5|Gets (Shard 5)|Количество|Итого||Нет измерений|
|getcommands5|Sets (Shard 5)|Количество|Итого||Нет измерений|
|evictedkeys5|Evicted Keys (Shard 5)|Количество|Итого||Нет измерений|
|totalkeys5|Total Keys (Shard 5)|Количество|Максимальная||Нет измерений|
|expiredkeys5|Expired Keys (Shard 5)|Количество|Итого||Нет измерений|
|usedmemory5|Used Memory (Shard 5)|Байты|Максимальная||Нет измерений|
|usedmemoryRss5|Used Memory RSS (Shard 5)|Байты|Максимальная||Нет измерений|
|serverLoad5|Server Load (Shard 5)|Процент|Максимальная||Нет измерений|
|cacheWrite5|Cache Write (Shard 5)|Байт/с|Максимальная||Нет измерений|
|cacheRead5|Cache Read (Shard 5)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime5|CPU (Shard 5)|Процент|Максимальная||Нет измерений|
|connectedclients6|Connected Clients (Shard 6)|Количество|Максимальная||Нет измерений|
|totalcommandsprocessed6|Total Operations (Shard 6)|Количество|Итого||Нет измерений|
|cachehits6|Cache Hits (Shard 6)|Количество|Итого||Нет измерений|
|cachemisses6|Cache Misses (Shard 6)|Количество|Итого||Нет измерений|
|getcommands6|Gets (Shard 6)|Количество|Итого||Нет измерений|
|setcommands6|Sets (Shard 6)|Количество|Итого||Нет измерений|
|evictedkeys6|Evicted Keys (Shard 6)|Количество|Итого||Нет измерений|
|totalkeys6|Total Keys (Shard 6)|Количество|Максимальная||Нет измерений|
|expiredkeys6|Expired Keys (Shard 6)|Количество|Итого||Нет измерений|
|usedmemory6|Used Memory (Shard 6)|Байты|Максимальная||Нет измерений|
|usedmemoryRss6|Used Memory RSS (Shard 6)|Байты|Максимальная||Нет измерений|
|serverLoad6|Server Load (Shard 6)|Процент|Максимальная||Нет измерений|
|cacheWrite6|Cache Write (Shard 6)|Байт/с|Максимальная||Нет измерений|
|cacheRead6|Cache Read (Shard 6)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime6|CPU (Shard 6)|Процент|Максимальная||Нет измерений|
|connectedclients7|Connected Clients (Shard 7)|Количество|Максимальная||Нет измерений|
|totalcommandsprocessed7|Total Operations (Shard 7)|Количество|Итого||Нет измерений|
|cachehits7|Cache Hits (Shard 7)|Количество|Итого||Нет измерений|
|cachemisses7|Cache Misses (Shard 7)|Количество|Итого||Нет измерений|
|getcommands7|Gets (Shard 7)|Количество|Итого||Нет измерений|
|setcommands7|Sets (Shard 7)|Количество|Итого||Нет измерений|
|evictedkeys7|Evicted Keys (Shard 7)|Количество|Итого||Нет измерений|
|totalkeys7|Total Keys (Shard 7)|Количество|Максимальная||Нет измерений|
|expiredkeys7|Expired Keys (Shard 7)|Количество|Итого||Нет измерений|
|usedmemory7|Used Memory (Shard 7)|Байты|Максимальная||Нет измерений|
|usedmemoryRss7|Used Memory RSS (Shard 7)|Байты|Максимальная||Нет измерений|
|serverLoad7|Server Load (Shard 7)|Процент|Максимальная||Нет измерений|
|cacheWrite7|Cache Write (Shard 7)|Байт/с|Максимальная||Нет измерений|
|cacheRead7|Cache Read (Shard 7)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime7|CPU (Shard 7)|Процент|Максимальная||Нет измерений|
|connectedclients8|Connected Clients (Shard 8)|Количество|Максимальная||Нет измерений|
|totalcommandsprocessed8|Total Operations (Shard 8)|Количество|Итого||Нет измерений|
|cachehits8|Cache Hits (Shard 8)|Количество|Итого||Нет измерений|
|cachemisses8|Cache Misses (Shard 8)|Количество|Итого||Нет измерений|
|getcommands8|Gets (Shard 8)|Количество|Итого||Нет измерений|
|setcommands8|Sets (Shard 8)|Количество|Итого||Нет измерений|
|evictedkeys8|Evicted Keys (Shard 8)|Количество|Итого||Нет измерений|
|totalkeys8|Total Keys (Shard 8)|Количество|Максимальная||Нет измерений|
|expiredkeys8|Expired Keys (Shard 8)|Количество|Итого||Нет измерений|
|usedmemory8|Used Memory (Shard 8)|Байты|Максимальная||Нет измерений|
|usedmemoryRss8|Used Memory RSS (Shard 8)|Байты|Максимальная||Нет измерений|
|serverLoad8|Server Load (Shard 8)|Процент|Максимальная||Нет измерений|
|cacheWrite8|Cache Write (Shard 8)|Байт/с|Максимальная||Нет измерений|
|cacheRead8|Cache Read (Shard 8)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime8|CPU (Shard 8)|Процент|Максимальная||Нет измерений|
|connectedclients9|Connected Clients (Shard 9)|Количество|Максимальная||Нет измерений|
|totalcommandsprocessed9|Total Operations (Shard 9)|Количество|Итого||Нет измерений|
|cachehits9|Cache Hits (Shard 9)|Количество|Итого||Нет измерений|
|cachemisses9|Cache Misses (Shard 9)|Количество|Итого||Нет измерений|
|getcommands9|Gets (Shard 9)|Количество|Итого||Нет измерений|
|setcommands9|Sets (Shard 9)|Count|Итого||Нет измерений|
|evictedkeys9|Evicted Keys (Shard 9)|Количество|Итого||Нет измерений|
|totalkeys9|Total Keys (Shard 9)|Количество|Максимальная||Нет измерений|
|expiredkeys9|Expired Keys (Shard 9)|Количество|Итого||Нет измерений|
|usedmemory9|Used Memory (Shard 9)|Байты|Максимальная||Нет измерений|
|usedmemoryRss9|Used Memory RSS (Shard 9)|Байты|Максимальная||Нет измерений|
|serverLoad9|Server Load (Shard 9)|Процент|Максимальная||Нет измерений|
|cacheWrite9|Cache Write (Shard 9)|Байт/с|Максимальная||Нет измерений|
|cacheRead9|Cache Read (Shard 9)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime9|CPU (Shard 9)|Процент|Максимальная||Нет измерений|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classicСompute/virtualMachines

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Загрузка ЦП|Процент|Средние|Процент выделенных вычислительных единиц, используемых в настоящее время виртуальными машинами.|Нет измерений|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Итого|Количество байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|Нет измерений|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Итого|Количество байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|Нет измерений|
|Disk Read Bytes/Sec|Скорость чтения с диска|Байт/с|Средние|Среднее количество байтов, считанных с диска за период мониторинга.|Нет измерений|
|Disk Write Bytes/Sec|Запись на диск|Байт/с|Средние|Среднее количество байтов, записанных на диск за период мониторинга.|Нет измерений|
|Disk Read Operations/Sec|Чтение с дисков (операций/с)|Число/с|Средние|Количество операций вода-вывода в секунду при чтении с диска.|Нет измерений|
|Disk Write Operations/Sec|Запись на диски (операций/с)|Число/с|Средние|Количество операций вода-вывода в секунду при записи на диск.|Нет измерений|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|TotalCalls|Total Calls|Количество|Итого|Общее число вызовов.|Нет измерений|
|SuccessfulCalls|Successful Calls|Количество|Итого|Число успешных вызовов.|Нет измерений|
|TotalErrors|Total Errors|Количество|Итого|Общее число вызовов, вернувших сообщение об ошибке (код ответа HTTP 4xx или 5xx).|Нет измерений|
|BlockedCalls|Blocked Calls|Количество|Итого|Число вызовов, превысивших ограничение скорости или квоты.|Нет измерений|
|ServerErrors|Server Errors|Количество|Итого|Число вызовов, приведших к внутренней ошибке сервера (код ответа HTTP 5xx).|Нет измерений|
|ClientErrors|Client Errors|Количество|Итого|Число вызовов, приведших к ошибке на стороне клиента (код ответа HTTP 4xx).|Нет измерений|
|DataIn|Входящие данные:|Байты|Итого|Размер входящих данных в байтах.|Нет измерений|
|DataOut|Выходные данные|Байты|Итого|Размер исходящих данных в байтах.|Нет измерений|
|Latency|Latency|MilliSeconds|Средние|Время задержки в миллисекундах.|Нет измерений|
|CharactersTranslated|Преобразованные символы|Количество|Итого|Общее количество символов во входящем текстовом запросе.|Нет измерений|
|SpeechSessionDuration|Длительность речи|Секунды|Итого|Общая длительность речи в секундах.|Нет измерений|
|TotalTransactions|Всего транзакций|Количество|Итого|Общее число транзакций|Нет измерений|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Загрузка ЦП|Процент|Средние|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|Нет измерений|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Итого|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|Нет измерений|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Итого|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|Нет измерений|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Итого|Общее число байтов, считанных с диска за период мониторинга.|Нет измерений|
|Disk Write Bytes|Скорость записи на диск|Байты|Итого|Общее число байтов, записанных на диск за период мониторинга.|Нет измерений|
|Disk Read Operations/Sec|Чтение с дисков (операций/с)|Число/с|Средняя|Количество операций вода-вывода в секунду при чтении с диска.|Нет измерений|
|Disk Write Operations/Sec|Запись на диски (операций/с)|Число/с|Средние|Количество операций вода-вывода в секунду при записи на диск.|Нет измерений|
|Оставшиеся кредиты ЦП|Оставшиеся кредиты ЦП|Количество|Средние|Общее число доступных для увеличения кредитов|Нет измерений|
|Использованные кредиты ЦП|Использованные кредиты ЦП|Количество|Средняя|Общее количество кредитов, использованных виртуальной машиной|Нет измерений|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets;

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Загрузка ЦП|Процент|Средняя|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|Нет измерений|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Итого|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|Нет измерений|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Итого|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|Нет измерений|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Итого|Общее число байтов, считанных с диска за период мониторинга.|Нет измерений|
|Disk Write Bytes|Скорость записи на диск|Байты|Итого|Общее число байтов, записанных на диск за период мониторинга.|Нет измерений|
|Disk Read Operations/Sec|Чтение с дисков (операций/с)|Число/с|Средние|Количество операций вода-вывода в секунду при чтении с диска.|Нет измерений|
|Disk Write Operations/Sec|Запись на диски (операций/с)|Число/с|Средние|Количество операций вода-вывода в секунду при записи на диск.|Нет измерений|
|Оставшиеся кредиты ЦП|Оставшиеся кредиты ЦП|Количество|Средние|Общее число доступных для увеличения кредитов|Нет измерений|
|Использованные кредиты ЦП|Использованные кредиты ЦП|Количество|Средние|Общее количество кредитов, использованных виртуальной машиной|Нет измерений|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Загрузка ЦП|Процент|Средняя|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|Нет измерений|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Итого|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|Нет измерений|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Итого|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|Нет измерений|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Итого|Общее число байтов, считанных с диска за период мониторинга.|Нет измерений|
|Disk Write Bytes|Скорость записи на диск|Байты|Итого|Общее число байтов, записанных на диск за период мониторинга.|Нет измерений|
|Disk Read Operations/Sec|Чтение с дисков (операций/с)|Число/с|Средние|Количество операций вода-вывода в секунду при чтении с диска.|Нет измерений|
|Disk Write Operations/Sec|Запись на диски (операций/с)|Число/с|Средние|Количество операций вода-вывода в секунду при записи на диск.|Нет измерений|
|Оставшиеся кредиты ЦП|Оставшиеся кредиты ЦП|Количество|Средняя|Общее число доступных для увеличения кредитов|Нет измерений|
|Использованные кредиты ЦП|Использованные кредиты ЦП|Количество|Средние|Общее количество кредитов, использованных виртуальной машиной|Нет измерений|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API Calls|Количество|Итого||Нет измерений|
|DCIMappingImportOperationSuccessfulLines|Mapping Import Operation Successful Lines|Количество|Итого||Нет измерений|
|DCIMappingImportOperationFailedLines|Mapping Import Operation Failed Lines|Количество|Итого||Нет измерений|
|DCIMappingImportOperationTotalLines|Mapping Import Operation Total Lines|Количество|Итого||Нет измерений|
|DCIMappingImportOperationRuntimeInSeconds|Mapping Import Operation Runtime In Seconds|Секунды|Итого||Нет измерений|
|DCIOutboundProfileExportSucceeded|Outbound Profile Export Succeeded|Количество|Итого||Нет измерений|
|DCIOutboundProfileExportFailed|Outbound Profile Export Failed|Количество|Итого||Нет измерений|
|DCIOutboundProfileExportDuration|Outbound Profile Export Duration|Секунды|Итого||Нет измерений|
|DCIOutboundKpiExportSucceeded|Outbound Kpi Export Succeeded|Количество|Итого||Нет измерений|
|DCIOutboundKpiExportFailed|Outbound Kpi Export Failed|Количество|Итого||Нет измерений|
|DCIOutboundKpiExportDuration|Outbound Kpi Export Duration|Секунды|Итого||Нет измерений|
|DCIOutboundKpiExportStarted|Outbound Kpi Export Started|Секунды|Итого||Нет измерений|
|DCIOutboundKpiRecordCount|Outbound Kpi Record Count|Секунды|Итого||Нет измерений|
|DCIOutboundProfileExportCount|Outbound Profile Export Count|Секунды|Итого||Нет измерений|
|DCIOutboundInitialProfileExportFailed|Outbound Initial Profile Export Failed|Секунды|Итого||Нет измерений|
|DCIOutboundInitialProfileExportSucceeded|Outbound Initial Profile Export Succeeded|Секунды|Итого||Нет измерений|
|DCIOutboundInitialKpiExportFailed|Outbound Initial Kpi Export Failed|Секунды|Итого||Нет измерений|
|DCIOutboundInitialKpiExportSucceeded|Outbound Initial Kpi Export Succeeded|Секунды|Итого||Нет измерений|
|DCIOutboundInitialProfileExportDurationInSeconds|Outbound Initial Profile Export Duration In Seconds|Секунды|Итого||Нет измерений|
|AdlaJobForStandardKpiFailed|Adla Job For Standard Kpi Failed In Seconds|Секунды|Итого||Нет измерений|
|AdlaJobForStandardKpiTimeOut|Adla Job For Standard Kpi TimeOut In Seconds|Секунды|Итого||Нет измерений|
|AdlaJobForStandardKpiCompleted|Adla Job For Standard Kpi Completed In Seconds|Секунды|Итого||Нет измерений|
|ImportASAValuesFailed|Import ASA Values Failed Count|Количество|Итого||Нет измерений|
|ImportASAValuesSucceeded|Import ASA Values Succeeded Count|Количество|Итого||Нет измерений|
|DCIProfilesCount|Количество экземпляров профиля|Количество|Последний||Нет измерений|
|DCIInteractionsPerMonthCount|Количество взаимодействий в месяц|Количество|Последний||Нет измерений|
|DCIKpisCount|Количество ключевых показателей эффективности|Количество|Последний||Нет измерений|
|DCISegmentsCount|Количество сегментов|Количество|Последний||Нет измерений|
|DCIPredictiveMatchPoliciesCount|Количество операций прогнозного сопоставления|Количество|Последний||Нет измерений|
|DCIPredictionsCount|Количество прогнозов|Количество|Последний||Нет измерений|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|SuccessfulRuns.|успешные циклы выполнения.|Количество|Итого|Число успешных запусков.|Нет измерений|
|FailedRuns и|циклы выполнения со сбоем;|Количество|Итого|Число неудачных запусков.|Нет измерений|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories;

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|PipelineFailedRuns|Метрики неудачных выполнений конвейеров.|Количество|Итого||Нет измерений|
|PipelineSucceededRuns|Метрики успешных выполнений конвейеров.|Количество|Итого||Нет измерений|
|ActivityFailedRuns|Метрики неудачных выполнений действий.|Количество|Итого||Нет измерений|
|ActivitySucceededRuns|Метрики успешных выполнений действий.|Количество|Итого||Нет измерений|
|TriggerFailedRuns|Метрики неудачных запусков триггеров.|Количество|Итого||Нет измерений|
|TriggerSucceededRuns|Метрики успешных запусков триггеров.|Количество|Итого||Нет измерений|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|JobEndedSuccess|Successful Jobs|Количество|Итого|Количество успешно выполненных заданий.|Нет измерений|
|JobEndedFailure|Failed Jobs|Количество|Итого|Количество невыполненных заданий.|Нет измерений|
|JobEndedCancelled|Cancelled Jobs|Количество|Итого|Количество отмененных заданий.|Нет измерений|
|JobAUEndedSuccess|Successful AU Time|Секунды|Итого|Общее время AU успешно выполненных заданий.|Нет измерений|
|JobAUEndedFailure|Failed AU Time|Секунды|Итого|Общее время AU невыполненных заданий.|Нет измерений|
|JobAUEndedCancelled|Cancelled AU Time|Секунды|Итого|Общее время AU отмененных заданий.|Нет измерений|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|TotalStorage|Общий объем хранилища|Байты|Максимальная|Общий объем данных, хранимых в учетной записи.|Нет измерений|
|DataWritten|Записанные данные|Байты|Итого|Общий объем данных, записанных в учетной записи.|Нет измерений|
|DataRead|Данных прочитано|Байты|Итого|Общий объем данных, прочитанных в учетной записи.|Нет измерений|
|WriteRequests|Запросы на запись|Количество|Итого|Количество запросов на запись данных в учетную запись.|Нет измерений|
|ReadRequests|Запросы на чтение|Количество|Итого|Количество запросов на чтение данных для учетной записи.|Нет измерений|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Средние|Нагрузка ЦП|Нет измерений|
|compute_limit|Ограничение на единицы вычислений|Количество|Средняя|Ограничение на единицы вычислений|Нет измерений|
|compute_consumption_percent|Процент использования единиц вычислений|Процент|Средние|Процент использования единиц вычислений|Нет измерений|
|memory_percent|Процент памяти|Процент|Средние|Процент памяти|Нет измерений|
|io_consumption_percent|Процент ввода-вывода данных|Процент|Средняя|Процент ввода-вывода данных|Нет измерений|
|storage_percent|Storage percentage|Процент|Средняя|Storage percentage|Нет измерений|
|storage_used|Storage used|Байты|Средняя|Storage used|Нет измерений|
|storage_limit|Storage limit|Байты|Средние|Storage limit|Нет измерений|
|active_connections|Общее количество активных подключений|Количество|Средние|Общее количество активных подключений|Нет измерений|
|connections_failed|Общее количество неудачных подключений|Количество|Средняя|Общее количество неудачных подключений|Нет измерений|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Средние|Нагрузка ЦП|Нет измерений|
|compute_limit|Ограничение на единицы вычислений|Количество|Средние|Ограничение на единицы вычислений|Нет измерений|
|compute_consumption_percent|Процент использования единиц вычислений|Процент|Средние|Процент использования единиц вычислений|Нет измерений|
|memory_percent|Процент памяти|Процент|Средние|Процент памяти|Нет измерений|
|io_consumption_percent|Процент ввода-вывода данных|Процент|Средние|Процент ввода-вывода данных|Нет измерений|
|storage_percent|Storage percentage|Процент|Средние|Storage percentage|Нет измерений|
|storage_used|Storage used|Байты|Средние|Storage used|Нет измерений|
|storage_limit|Storage limit|Байты|Средние|Storage limit|Нет измерений|
|active_connections|Общее количество активных подключений|Количество|Средняя|Общее количество активных подключений|Нет измерений|
|connections_failed|Общее количество неудачных подключений|Количество|Средние|Общее количество неудачных подключений|Нет измерений|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Число предпринятых попыток отправки сообщений телеметрии.|Количество|Итого|Число предпринятых попыток отправки в Центр Интернета вещей сообщений телеметрии из устройства в облако.|Нет измерений|
|d2c.telemetry.ingress.success|Число отправленных сообщений телеметрии.|Количество|Итого|Число успешно отправленных в Центр Интернета вещей сообщений телеметрии из устройства в облако.|Нет измерений|
|c2d.commands.egress.complete.success|Завершенные команды.|Количество|Итого|Число успешно завершенных устройством команд, отправленных из облака.|Нет измерений|
|c2d.commands.egress.abandon.success|Прерванные команды.|Количество|Итого|Число прерванных устройством команд, отправленных из облака.|Нет измерений|
|c2d.commands.egress.reject.success|Отклоненные команды.|Количество|Итого|Число отмененных устройством команд, отправленных из облака.|Нет измерений|
|devices.totalDevices|Общее число устройств.|Количество|Итого|Число устройств, зарегистрированных в Центре Интернета вещей.|Нет измерений|
|devices.connectedDevices.allProtocol|Подключенные устройства|Количество|Итого|Число устройств, подключенных к Центру Интернета вещей.|Нет измерений|
|d2c.telemetry.egress.success|Telemetry messages delivered|Количество|Итого|Общее число сообщений, успешно записанных в конечные точки.|Нет измерений|
|d2c.telemetry.egress.dropped|Удаленные сообщения.|Количество|Итого|Число сообщений, отброшенных из-за неработоспособности конечной точки доставки|Нет измерений|
|d2c.telemetry.egress.orphaned|Потерянные сообщения.|Количество|Итого|Число сообщений, не соответствующих какому-либо маршруту, включая резервный.|Нет измерений|
|d2c.telemetry.egress.invalid|Недопустимые сообщения.|Количество|Итого|Число сообщений, которые не были доставлены из-за несовместимости с конечной точкой.|Нет измерений|
|d2c.telemetry.egress.fallback|Сообщения, соответствующие условиям для резервной конечной точки.|Количество|Итого|Число сообщений, записанных в резервную конечную точку.|Нет измерений|
|d2c.endpoints.egress.eventHubs|Сообщения, доставленные в конечные точки концентратора событий.|Количество|Итого|Число успешных записей сообщений в конечные точки концентратора событий.|Нет измерений|
|d2c.endpoints.latency.eventHubs|Message latency for Event Hub endpoints|Миллисекунды|Средние|Средняя задержка в миллисекундах между поступлением сообщения в Центр Интернета вещей и его поступлением на конечную точку концентратора событий.|Нет измерений|
|d2c.endpoints.egress.serviceBusQueues|Сообщения, доставленные в конечные точки очереди служебной шины.|Количество|Итого|Число успешных записей сообщений в конечные точки очереди служебной шины.|Нет измерений|
|d2c.endpoints.latency.serviceBusQueues|Message latency for Service Bus Queue endpoints|Миллисекунды|Средняя|Средняя задержка в миллисекундах между поступлением сообщения в Центр Интернета вещей и его поступлением на конечную точку очереди служебной шины.|Нет измерений|
|d2c.endpoints.egress.serviceBusTopics|Сообщения, доставленные в конечные точки раздела служебной шины.|Количество|Итого|Число успешных записей сообщений в конечные точки раздела служебной шины.|Нет измерений|
|d2c.endpoints.latency.serviceBusTopics|Message latency for Service Bus Topic endpoints|Миллисекунды|Средние|Средняя задержка в миллисекундах между поступлением сообщения в Центр Интернета вещей и его поступлением на конечную точку раздела служебной шины.|Нет измерений|
|d2c.endpoints.egress.builtIn.events|Сообщения, доставленные во встроенную конечную точку (сообщения и события).|Количество|Итого|Число успешных записей сообщений во встроенную конечную точку (сообщения и события).|Нет измерений|
|d2c.endpoints.latency.builtIn.events|Задержка сообщения для встроенной конечной точки (сообщения и события).|Миллисекунды|Средние|Средняя задержка в миллисекундах между поступлением сообщения в Центр Интернета вещей и его поступлением на встроенную конечную точку (сообщения и события). |Нет измерений|
|d2c.Endpoints.egress.Storage|Сообщения, доставленные в конечные точки хранилища|Количество|Итого|Общее число сообщений, успешно записанных в конечные точки хранилища|Нет измерений|
|d2c.Endpoints.latency.Storage|Задержка сообщений для конечных точек хранилища|Миллисекунды|Средняя|Средняя задержка между поступлением сообщения в Центр Интернета вещей и в конечную точку хранилища в миллисекундах|Нет измерений|
|d2c.endpoints.egress.storage.bytes|Данные, записанные в хранилище|Байты|Итого|Объем данных, записанных в конечные точки хранилища (в байтах)|Нет измерений|
|d2c.Endpoints.egress.Storage.BLOBs|Большие двоичные объекты, записанные в хранилище|Количество|Итого|Количество больших двоичных объектов, записанных в конечные точки хранилища|Нет измерений|
|d2c.twin.read.success|Успешные операции чтения с двойников, инициированные устройством.|Количество|Итого|Число всех успешных операций чтения с двойников, инициированных устройством.|Нет измерений|
|d2c.twin.read.failure|Неудачные операции чтения с двойников, инициированные устройством.|Количество|Итого|Число всех неудачных операций чтения с двойников, инициированных устройством.|Нет измерений|
|d2c.twin.read.size|Размер ответа операций чтения с двойников, инициированных устройством.|Байты|Средняя|Среднее, минимальное и максимальное значение всех успешных операций чтения, инициированных устройством.|Нет измерений|
|d2c.twin.update.success|Успешные обновления двойников, инициированные устройством.|Количество|Итого|Число всех успешных обновлений двойников, инициированных устройством.|Нет измерений|
|d2c.twin.update.failure|Неудачные обновления двойников, инициированные устройством.|Количество|Итого|Число всех неудачных обновлений двойников, инициированных устройством.|Нет измерений|
|d2c.twin.update.size|Размер обновлений двойников, инициированных устройством.|Байты|Средние|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных устройством.|Нет измерений|
|c2d.methods.success|Успешные вызовы прямых методов.|Количество|Итого|Число всех успешных вызовов прямых методов.|Нет измерений|
|c2d.methods.failure|Неудачные вызовы прямых методов.|Количество|Итого|Число всех неудачных вызовов прямых методов.|Нет измерений|
|c2d.methods.requestSize|Размер запроса вызовов прямых методов.|Байты|Средняя|Среднее, минимальное и максимальное значение всех успешных запросов прямых методов.|Нет измерений|
|c2d.methods.responseSize|Размер ответа вызовов прямых методов.|Байты|Средние|Среднее, минимальное и максимальное значения всех успешных ответов прямых методов.|Нет измерений|
|c2d.twin.read.success|Успешные операции чтения с двойников, инициированные из серверной части.|Количество|Итого|Число всех успешных операций чтения с двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.read.failure|Неудачные операции чтения с двойников, инициированные из серверной части.|Количество|Итого|Число всех неудачных операций чтения с двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.read.size|Размер ответа операций чтения с двойников, инициированных из серверной части.|Байты|Средние|Среднее, минимальное и максимальное значения всех успешных операций чтения с двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.update.success|Успешные обновления двойников, инициированные из серверной части.|Количество|Итого|Число всех успешных обновлений двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.update.failure|Неудачные обновления двойников, инициированные из серверной части.|Количество|Итого|Число всех неудачных обновлений двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.update.size|Размер обновлений двойников, инициированных из серверной части.|Байты|Средние|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных из серверной части.|Нет измерений|
|twinQueries.success|Успешные запросы двойников.|Количество|Итого|Число всех успешных запросов двойников.|Нет измерений|
|twinQueries.failure|Неудачные запросы двойников.|Количество|Итого|Количество всех неудачных запросов двойников.|Нет измерений|
|twinQueries.resultSize|Размер результатов запросов двойников.|Байты|Средние|Среднее, минимальное и максимальное значения размера результатов всех успешных запросов двойников.|Нет измерений|
|jobs.createTwinUpdateJob.success|Успешные операции создания заданий обновления двойников.|Количество|Итого|Количество всех успешных созданий заданий обновления двойников.|Нет измерений|
|jobs.createTwinUpdateJob.failure|Неудачные операции создания заданий обновления двойников.|Количество|Итого|Количество всех неудачных операций создания заданий обновления двойников.|Нет измерений|
|jobs.createDirectMethodJob.success|Успешные операции создания заданий вызова методов.|Количество|Итого|Количество всех успешных операций создания заданий вызова прямых методов.|Нет измерений|
|jobs.createDirectMethodJob.failure|Неудачные операции создания заданий вызова методов.|Количество|Итого|Количество всех неудачных операций создания заданий вызова прямых методов.|Нет измерений|
|jobs.listJobs.success|Успешные вызовы получения списка заданий.|Количество|Итого|Количество всех успешных вызовов для получения списка заданий.|Нет измерений|
|jobs.listJobs.failure|Неудачные вызовы получения списка заданий.|Количество|Итого|Количество всех неудачных вызовов для получения списка заданий.|Нет измерений|
|jobs.cancelJob.success|Успешные отмены заданий.|Количество|Итого|Количество всех успешных вызовов для отмены заданий.|Нет измерений|
|jobs.cancelJob.failure|Неудачные отмены заданий.|Количество|Итого|Количество всех неудачных вызовов для отмены заданий.|Нет измерений|
|jobs.queryJobs.success|Успешные запросы заданий.|Количество|Итого|Количество всех успешных вызовов для запроса заданий.|Нет измерений|
|jobs.queryJobs.failure|Неудачные запросы заданий.|Количество|Итого|Количество всех неудачных вызовов для запроса заданий.|Нет измерений|
|jobs.completed|Завершенные задания|Количество|Итого|Количество всех выполненных заданий.|Нет измерений|
|jobs.failed|Неудачные задания.|Количество|Итого|Количество всех неудачных заданий.|Нет измерений|
|d2c.telemetry.ingress.sendThrottle|Количество ошибок регулирования|Количество|Итого|Количество ошибок регулирования из-за регулирования пропускной способности устройства|Нет измерений|
|dailyMessageQuotaUsed|Общее количество используемых сообщений|Количество|Средняя|Количество сообщений, использованных сегодня. Это совокупное значение, которое сбрасывается до нуля в 00:00 UTC каждый день.|Нет измерений|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|RegistrationAttempts|Попытки регистрации|Количество|Итого|Количество попыток регистрации устройств|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Назначенные устройства|Количество|Итого|Количество устройств, назначенных Центру Интернета вещей|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Попытки аттестации|Количество|Итого|Количество попыток аттестации устройств|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdeviceselasticpools"></a>Microsoft.Devices/ElasticPools

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|elasticPool.requestedUsageRate|частота использования|Процент|Средние|частота использования|Нет измерений|

## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|tenantHub.requestedUsageRate|частота использования|Процент|Средняя|частота использования|Нет измерений|
|deviceDataUsage|Использование общего devicedata|Количество|Итого|Байтов, переданных на и с любого устройства, подключенные к центру IOT|Нет измерений|
|d2c.telemetry.ingress.allProtocol|Число предпринятых попыток отправки сообщений телеметрии.|Количество|Итого|Число предпринятых попыток отправки в Центр Интернета вещей сообщений телеметрии из устройства в облако.|Нет измерений|
|d2c.telemetry.ingress.success|Число отправленных сообщений телеметрии.|Количество|Итого|Число успешно отправленных в Центр Интернета вещей сообщений телеметрии из устройства в облако.|Нет измерений|
|c2d.commands.egress.complete.success|Завершенные команды.|Количество|Итого|Число успешно завершенных устройством команд, отправленных из облака.|Нет измерений|
|c2d.commands.egress.abandon.success|Прерванные команды.|Количество|Итого|Число прерванных устройством команд, отправленных из облака.|Нет измерений|
|c2d.commands.egress.reject.success|Отклоненные команды.|Количество|Итого|Число отмененных устройством команд, отправленных из облака.|Нет измерений|
|devices.totalDevices|Общее число устройств.|Количество|Итого|Число устройств, зарегистрированных в Центре Интернета вещей.|Нет измерений|
|devices.connectedDevices.allProtocol|Подключенные устройства|Количество|Итого|Число устройств, подключенных к Центру Интернета вещей.|Нет измерений|
|d2c.telemetry.egress.success|Telemetry messages delivered|Количество|Итого|Общее число сообщений, успешно записанных в конечные точки.|Нет измерений|
|d2c.telemetry.egress.dropped|Удаленные сообщения.|Количество|Итого|Число сообщений, отброшенных из-за неработоспособности конечной точки доставки|Нет измерений|
|d2c.telemetry.egress.orphaned|Потерянные сообщения.|Количество|Итого|Число сообщений, не соответствующих какому-либо маршруту, включая резервный.|Нет измерений|
|d2c.telemetry.egress.invalid|Недопустимые сообщения.|Количество|Итого|Число сообщений, которые не были доставлены из-за несовместимости с конечной точкой.|Нет измерений|
|d2c.telemetry.egress.fallback|Сообщения, соответствующие условиям для резервной конечной точки.|Количество|Итого|Число сообщений, записанных в резервную конечную точку.|Нет измерений|
|d2c.endpoints.egress.eventHubs|Сообщения, доставленные в конечные точки концентратора событий.|Количество|Итого|Число успешных записей сообщений в конечные точки концентратора событий.|Нет измерений|
|d2c.endpoints.latency.eventHubs|Message latency for Event Hub endpoints|Миллисекунды|Средние|Средняя задержка в миллисекундах между поступлением сообщения в Центр Интернета вещей и его поступлением на конечную точку концентратора событий.|Нет измерений|
|d2c.endpoints.egress.serviceBusQueues|Сообщения, доставленные в конечные точки очереди служебной шины.|Количество|Итого|Число успешных записей сообщений в конечные точки очереди служебной шины.|Нет измерений|
|d2c.endpoints.latency.serviceBusQueues|Message latency for Service Bus Queue endpoints|Миллисекунды|Средние|Средняя задержка в миллисекундах между поступлением сообщения в Центр Интернета вещей и его поступлением на конечную точку очереди служебной шины.|Нет измерений|
|d2c.endpoints.egress.serviceBusTopics|Сообщения, доставленные в конечные точки раздела служебной шины.|Количество|Итого|Число успешных записей сообщений в конечные точки раздела служебной шины.|Нет измерений|
|d2c.endpoints.latency.serviceBusTopics|Message latency for Service Bus Topic endpoints|Миллисекунды|Средние|Средняя задержка в миллисекундах между поступлением сообщения в Центр Интернета вещей и его поступлением на конечную точку раздела служебной шины.|Нет измерений|
|d2c.endpoints.egress.builtIn.events|Сообщения, доставленные во встроенную конечную точку (сообщения и события).|Количество|Итого|Число успешных записей сообщений во встроенную конечную точку (сообщения и события).|Нет измерений|
|d2c.endpoints.latency.builtIn.events|Задержка сообщения для встроенной конечной точки (сообщения и события).|Миллисекунды|Средняя|Средняя задержка в миллисекундах между поступлением сообщения в Центр Интернета вещей и его поступлением на встроенную конечную точку (сообщения и события). |Нет измерений|
|d2c.Endpoints.egress.Storage|Сообщения, доставленные в конечные точки хранилища|Количество|Итого|Общее число сообщений, успешно записанных в конечные точки хранилища|Нет измерений|
|d2c.Endpoints.latency.Storage|Задержка сообщений для конечных точек хранилища|Миллисекунды|Средние|Средняя задержка между поступлением сообщения в Центр Интернета вещей и в конечную точку хранилища в миллисекундах|Нет измерений|
|d2c.endpoints.egress.storage.bytes|Данные, записанные в хранилище|Байты|Итого|Объем данных, записанных в конечные точки хранилища (в байтах)|Нет измерений|
|d2c.Endpoints.egress.Storage.BLOBs|Большие двоичные объекты, записанные в хранилище|Количество|Итого|Количество больших двоичных объектов, записанных в конечные точки хранилища|Нет измерений|
|d2c.twin.read.success|Успешные операции чтения с двойников, инициированные устройством.|Количество|Итого|Число всех успешных операций чтения с двойников, инициированных устройством.|Нет измерений|
|d2c.twin.read.failure|Неудачные операции чтения с двойников, инициированные устройством.|Количество|Итого|Число всех неудачных операций чтения с двойников, инициированных устройством.|Нет измерений|
|d2c.twin.read.size|Размер ответа операций чтения с двойников, инициированных устройством.|Байты|Средние|Среднее, минимальное и максимальное значение всех успешных операций чтения, инициированных устройством.|Нет измерений|
|d2c.twin.update.success|Успешные обновления двойников, инициированные устройством.|Количество|Итого|Число всех успешных обновлений двойников, инициированных устройством.|Нет измерений|
|d2c.twin.update.failure|Неудачные обновления двойников, инициированные устройством.|Количество|Итого|Число всех неудачных обновлений двойников, инициированных устройством.|Нет измерений|
|d2c.twin.update.size|Размер обновлений двойников, инициированных устройством.|Байты|Средняя|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных устройством.|Нет измерений|
|c2d.methods.success|Успешные вызовы прямых методов.|Количество|Итого|Число всех успешных вызовов прямых методов.|Нет измерений|
|c2d.methods.failure|Неудачные вызовы прямых методов.|Количество|Итого|Число всех неудачных вызовов прямых методов.|Нет измерений|
|c2d.methods.requestSize|Размер запроса вызовов прямых методов.|Байты|Средние|Среднее, минимальное и максимальное значение всех успешных запросов прямых методов.|Нет измерений|
|c2d.methods.responseSize|Размер ответа вызовов прямых методов.|Байты|Средние|Среднее, минимальное и максимальное значения всех успешных ответов прямых методов.|Нет измерений|
|c2d.twin.read.success|Успешные операции чтения с двойников, инициированные из серверной части.|Количество|Итого|Число всех успешных операций чтения с двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.read.failure|Неудачные операции чтения с двойников, инициированные из серверной части.|Количество|Итого|Число всех неудачных операций чтения с двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.read.size|Размер ответа операций чтения с двойников, инициированных из серверной части.|Байты|Средняя|Среднее, минимальное и максимальное значения всех успешных операций чтения с двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.update.success|Успешные обновления двойников, инициированные из серверной части.|Количество|Итого|Число всех успешных обновлений двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.update.failure|Неудачные обновления двойников, инициированные из серверной части.|Количество|Итого|Число всех неудачных обновлений двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.update.size|Размер обновлений двойников, инициированных из серверной части.|Байты|Средние|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных из серверной части.|Нет измерений|
|twinQueries.success|Успешные запросы двойников.|Количество|Итого|Число всех успешных запросов двойников.|Нет измерений|
|twinQueries.failure|Неудачные запросы двойников.|Количество|Итого|Количество всех неудачных запросов двойников.|Нет измерений|
|twinQueries.resultSize|Размер результатов запросов двойников.|Байты|Средние|Среднее, минимальное и максимальное значения размера результатов всех успешных запросов двойников.|Нет измерений|
|jobs.createTwinUpdateJob.success|Успешные операции создания заданий обновления двойников.|Количество|Итого|Количество всех успешных созданий заданий обновления двойников.|Нет измерений|
|jobs.createTwinUpdateJob.failure|Неудачные операции создания заданий обновления двойников.|Количество|Итого|Количество всех неудачных операций создания заданий обновления двойников.|Нет измерений|
|jobs.createDirectMethodJob.success|Успешные операции создания заданий вызова методов.|Количество|Итого|Количество всех успешных операций создания заданий вызова прямых методов.|Нет измерений|
|jobs.createDirectMethodJob.failure|Неудачные операции создания заданий вызова методов.|Количество|Итого|Количество всех неудачных операций создания заданий вызова прямых методов.|Нет измерений|
|jobs.listJobs.success|Успешные вызовы получения списка заданий.|Количество|Итого|Количество всех успешных вызовов для получения списка заданий.|Нет измерений|
|jobs.listJobs.failure|Неудачные вызовы получения списка заданий.|Количество|Итого|Количество всех неудачных вызовов для получения списка заданий.|Нет измерений|
|jobs.cancelJob.success|Успешные отмены заданий.|Количество|Итого|Количество всех успешных вызовов для отмены заданий.|Нет измерений|
|jobs.cancelJob.failure|Неудачные отмены заданий.|Количество|Итого|Количество всех неудачных вызовов для отмены заданий.|Нет измерений|
|jobs.queryJobs.success|Успешные запросы заданий.|Количество|Итого|Количество всех успешных вызовов для запроса заданий.|Нет измерений|
|jobs.queryJobs.failure|Неудачные запросы заданий.|Количество|Итого|Количество всех неудачных вызовов для запроса заданий.|Нет измерений|
|jobs.completed|Завершенные задания|Количество|Итого|Количество всех выполненных заданий.|Нет измерений|
|jobs.failed|Неудачные задания.|Количество|Итого|Количество всех неудачных заданий.|Нет измерений|
|d2c.telemetry.ingress.sendThrottle|Количество ошибок регулирования|Количество|Итого|Количество ошибок регулирования из-за регулирования пропускной способности устройства|Нет измерений|
|dailyMessageQuotaUsed|Общее количество используемых сообщений|Количество|Средняя|Количество сообщений, использованных сегодня. Это совокупное значение, которое сбрасывается до нуля в 00:00 UTC каждый день.|Нет измерений|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|MetadataRequests|Запросы метаданных|Количество|Количество|Количество запросов метаданных. Cosmos DB поддерживает коллекцию метаданных системы для каждой учетной записи, позволяет перечислять коллекций, баз данных, и т.д., и их конфигурации бесплатно.|GlobalDatabaseAccountName, имя базы данных, CollectionName, регион, StatusCode|
|MongoRequestCharge|Плата Mongo запроса|Количество|Итого|Используются единицы запрос Mongo|GlobalDatabaseAccountName, имя базы данных, CollectionName, регион, CommandName, код ошибки|
|MongoRequests|Запросы Mongo|Количество|Количество|Число выполненных запросов Mongo|GlobalDatabaseAccountName, имя базы данных, CollectionName, регион, CommandName, код ошибки|
|TotalRequestUnits|Единицы общий запрос|Количество|Итого|Запроса используются единицы|GlobalDatabaseAccountName, имя базы данных, CollectionName, регион, StatusCode|
|TotalRequests|Общее количество запросов|Количество|Количество|Количество выполненных запросов|GlobalDatabaseAccountName, имя базы данных, CollectionName, регион, StatusCode|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|SuccessfulRequests|Количество успешных запросов (Предварительная версия)|Количество|Итого|Выполненные запросы для Microsoft.EventHub. (предварительная версия)|EntityName|
|ServerErrors|Ошибки на сервере. (предварительная версия)|Количество|Итого|Ошибки на сервере для Microsoft.EventHub. (предварительная версия)|EntityName|
|UserErrors|Ошибки пользователей. (предварительная версия)|Количество|Итого|Ошибки пользователей для Microsoft.EventHub. (предварительная версия)|EntityName|
|QuotaExceededErrors|Ошибки превышения квоты. (предварительная версия)|Количество|Итого|Ошибки превышения квоты для Microsoft.EventHub. (предварительная версия)|EntityName|
|ThrottledRequests|Регулируемые запросы. (предварительная версия)|Количество|Итого|Регулируемые запросы для Microsoft.EventHub. (предварительная версия)|EntityName|
|IncomingRequests|Входящие запросы (Предварительная версия)|Количество|Итого|Входящие запросы для Microsoft.EventHub. (предварительная версия)|EntityName|
|IncomingMessages|Входящие сообщения (Предварительная версия)|Количество|Итого|Входящие сообщения для Microsoft.EventHub. (предварительная версия)|EntityName|
|OutgoingMessages|Исходящих сообщений (Предварительная версия)|Количество|Итого|Исходящие сообщения для Microsoft.EventHub. (предварительная версия)|EntityName|
|IncomingBytes|Входящие байты. (предварительная версия)|Байты|Итого|Входящие байты для Microsoft.EventHub. (предварительная версия)|EntityName|
|OutgoingBytes|Исходящие байты. (предварительная версия)|Байты|Итого|Исходящие байты для Microsoft.EventHub. (предварительная версия)|EntityName|
|ActiveConnections|ActiveConnections (Предварительная версия)|Количество|Итого|Число активных подключений для Microsoft.EventHub. (предварительная версия)|EntityName|
|ConnectionsOpened|Открытые подключения. (предварительная версия)|Количество|Итого|Открытые подключения для Microsoft.EventHub. (предварительная версия)|EntityName|
|ConnectionsClosed|Закрытые подключения. (предварительная версия)|Количество|Итого|Закрытые подключения для Microsoft.EventHub. (предварительная версия)|EntityName|
|CaptureBacklog|Невыполненная работа записи. (предварительная версия)|Количество|Итого|Невыполненная работа записи для Microsoft.EventHub. (предварительная версия)|EntityName|
|CapturedMessages|Записанные сообщения. (предварительная версия)|Количество|Итого|Записанные сообщения для Microsoft.EventHub. (предварительная версия)|EntityName|
|CapturedBytes|Записанные байты. (предварительная версия)|Байты|Итого|Записанные байты для Microsoft.EventHub. (предварительная версия)|EntityName|
|INREQS|Входящих запросов|Количество|Итого|Общее количество входящих запросов отправки для пространства имен|Нет измерений|
|SUCCREQ|Выполненные запросы|Количество|Итого|Общее количество успешных запросов для пространства имен|Нет измерений|
|FAILREQ|Невыполненные запросы|Количество|Итого|Общее количество неудачных запросов для пространства имен|Нет измерений|
|SVRBSY|Ошибки из-за занятости сервера|Количество|Итого|Общее количество ошибок из-за занятости сервера для пространства имен|Нет измерений|
|INTERR|Внутренние ошибки сервера|Количество|Итого|Общее количество внутренних ошибок сервера для пространства имен|Нет измерений|
|MISCERR|Другие ошибки|Количество|Итого|Общее количество неудачных запросов для пространства имен|Нет измерений|
|INMSGS|Входящие сообщения (не рекомендуется)|Количество|Итого|Общее количество входящих сообщений для пространства имен. Использовать эту метрику не рекомендуется. Вместо этого используйте метрику "Входящие сообщения".|Нет измерений|
|EHINMSGS|Входящие сообщения|Количество|Итого|Общее количество входящих сообщений для пространства имен|Нет измерений|
|OUTMSGS|Исходящие сообщения (не рекомендуется)|Количество|Итого|Общее количество исходящих сообщений для пространства имен. Использовать эту метрику не рекомендуется. Вместо этого используйте метрику "Исходящие сообщения"|Нет измерений|
|EHOUTMSGS|Исходящие сообщения|Количество|Итого|Общее количество исходящих сообщений для пространства имен|Нет измерений|
|EHINMBS|Входящие байты (не рекомендуется)|Байты|Итого|Пропускная способность входящих сообщений концентратора событий для пространства имен. Использовать эту метрику не рекомендуется. Вместо этого используйте метрику "Входящие байты".|Нет измерений|
|EHINBYTES|Входящие байты|Байты|Итого|Пропускная способность входящих сообщений концентратора событий для пространства имен|Нет измерений|
|EHOUTMBS|Исходящие байты (не рекомендуется)|Байты|Итого|Пропускная способность исходящих сообщений концентратора событий для пространства имен. Использовать эту метрику не рекомендуется. Вместо этого используйте метрику "Исходящие байты".|Нет измерений|
|EHOUTBYTES|Исходящие байты|Байты|Итого|Пропускная способность исходящих сообщений концентратора событий для пространства имен|Нет измерений|
|EHABL|Archive backlog messages|Количество|Итого|Архивные сообщения концентратора событий в списке невыполненных работ для пространства имен|Нет измерений|
|EHAMSGS|Archive messages|Количество|Итого|Архивные сообщения концентратора событий в пространстве имен|Нет измерений|
|EHAMBS|Archive message throughput|Байты|Итого|Пропускная способность архивных сообщений концентратора событий в пространстве имен|Нет измерений|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|ObservedMetricValue|Наблюдаемое значение метрики|Количество|Средние|Значение, вычисляемое функцией автомасштабирования при выполнении|MetricTriggerSource|
|MetricThreshold|Пороговое значение метрики|Количество|Средняя|Настроенное пороговое значение автомасштабирования при выполнении автомасштабирования.|MetricTriggerRule|
|ObservedCapacity|Наблюдаемая емкость|Количество|Средние|Емкость, передаваемая для автомасштабирования при выполнении.|Нет измерений|
|ScaleActionsInitiated|Инициированные действия масштабирования|Количество|Итого|Направление операции масштабирования.|ScaleDirection|

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|Latency|Latency|Миллисекунды|Средние|Длительность вызовов API|Имя_операции OperationResult|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Количество|Итого|Число запущенных выполнений рабочего процесса.|Нет измерений|
|RunsCompleted|Runs Completed|Количество|Итого|Число завершенных выполнений рабочего процесса.|Нет измерений|
|RunsSucceeded|Runs Succeeded|Количество|Итого|Число успешно завершенных выполнений рабочего процесса.|Нет измерений|
|RunsFailed|Runs Failed|Количество|Итого|Число выполнений рабочего процесса, завершившихся сбоем.|Нет измерений|
|RunsCancelled|Runs Cancelled|Количество|Итого|Число отмененных выполнений рабочего процесса.|Нет измерений|
|RunLatency|Run Latency|Секунды|Средние|Задержка завершенных выполнений рабочего процесса.|Нет измерений|
|RunSuccessLatency|Run Success Latency|Секунды|Средние|Задержка успешно завершенных выполнений рабочего процесса.|Нет измерений|
|RunThrottledEvents|Run Throttled Events|Количество|Итого|Число событий регулирования действия или триггера рабочего процесса.|Нет измерений|
|RunFailurePercentage|Run Failure Percentage|Процент|Итого|Процент выполнений рабочего процесса, завершившихся сбоем.|Нет измерений|
|ActionsStarted|Actions Started |Количество|Итого|Число запущенных действий рабочего процесса.|Нет измерений|
|ActionsCompleted|Actions Completed |Количество|Итого|Число завершенных действий рабочего процесса.|Нет измерений|
|ActionsSucceeded|Actions Succeeded |Количество|Итого|Число успешно выполненных действий рабочего процесса.|Нет измерений|
|ActionsFailed|Actions Failed|Количество|Итого|Число действий рабочего процесса, завершившихся сбоем.|Нет измерений|
|ActionsSkipped|Actions Skipped |Количество|Итого|Число пропущенных действий рабочего процесса.|Нет измерений|
|ActionLatency|Action Latency |Секунды|Средние|Задержка завершенных действий рабочего процесса.|Нет измерений|
|ActionSuccessLatency|Action Success Latency |Секунды|Средние|Задержка успешно выполненных действий рабочего процесса.|Нет измерений|
|ActionThrottledEvents|Action Throttled Events|Количество|Итого|Число событий регулирования действия рабочего процесса.|Нет измерений|
|TriggersStarted|Triggers Started |Количество|Итого|Число запущенных триггеров рабочего процесса.|Нет измерений|
|TriggersCompleted|Triggers Completed |Количество|Итого|Число завершенных триггеров рабочего процесса.|Нет измерений|
|TriggersSucceeded|Triggers Succeeded |Количество|Итого|Число успешно выполненных триггеров рабочего процесса.|Нет измерений|
|TriggersFailed|Triggers Failed |Количество|Итого|Число триггеров рабочего процесса, завершившихся со сбоем.|Нет измерений|
|TriggersSkipped|Triggers Skipped|Количество|Итого|Число пропущенных триггеров рабочего процесса.|Нет измерений|
|TriggersFired|Triggers Fired |Количество|Итого|Число активированных триггеров рабочего процесса.|Нет измерений|
|TriggerLatency|Trigger Latency |Секунды|Средние|Задержка завершенных триггеров рабочего процесса.|Нет измерений|
|TriggerFireLatency|Trigger Fire Latency |Секунды|Средняя|Задержка активированных триггеров рабочего процесса.|Нет измерений|
|TriggerSuccessLatency|Trigger Success Latency |Секунды|Средняя|Задержка успешно выполненных триггеров рабочего процесса.|Нет измерений|
|TriggerThrottledEvents|Trigger Throttled Events|Количество|Итого|Число событий регулирования триггера рабочего процесса.|Нет измерений|
|BillableActionExecutions|Billable Action Executions|Количество|Итого|Число выполненных действий рабочего процесса, за которые выставляется счет.|Нет измерений|
|BillableTriggerExecutions|Billable Trigger Executions|Количество|Итого|Число выполненных триггеров рабочего процесса, за которые выставляется счет.|Нет измерений|
|TotalBillableExecutions|Total Billable Executions|Количество|Итого|Число выполнений рабочего процесса, за которые выставляется счет.|Нет измерений|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|BytesSentRate|Отправлено байт|Количество|Итого|Число байтов, отправленных сетевого интерфейса|Нет измерений|
|BytesReceivedRate|Получено байт|Количество|Итого|Число байтов, полученных сетевого интерфейса|Нет измерений|
|PacketsSentRate|Пакеты, отправленные|Количество|Итого|Число пакетов, отправленных сетевого интерфейса|Нет измерений|
|PacketsReceivedRate|Получено пакетов|Количество|Итого|Число пакетов, полученных сетевого интерфейса|Нет измерений|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|VipAvailability|Доступность виртуального IP-адреса|Количество|Средняя|Доступность конечных точек виртуального IP-адреса на основе результатов проверки|VipAddress, VipPort|
|DipAvailability|Доступность выделенного IP-адреса (DIP)|Количество|Средние|Доступность конечных точек выделенного IP-адреса на основе результатов проверки|ProtocolType, DipPort, VipAddress, VipPort, DipAddress|
|ByteCount|Количество байтов|Количество|Итого|Общее количество байтов, переданных за период времени|VipAddress, VipPort, Direction|
|PacketCount|Количество пакетов|Количество|Итого|Общее количество пакетов, переданных за период времени|VipAddress, VipPort, Direction|
|SYNCount|Количество пакетов SYN|Количество|Итого|Общее количество пакетов SYN, переданных за период времени|VipAddress, VipPort, Direction|
|SnatConnectionCount|Количество подключений SNAT|Количество|Итого|Общее количество подключений SNAT, созданных за период времени|VipAddress, DipAddress, ConnectionState|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|PacketsInDDoS|Входящие пакеты (атака DDoS)|Число/с|Максимальная|Входящие пакеты (атака DDoS)|Нет измерений|
|PacketsDroppedDDoS|Удаленные входящие пакеты (атака DDoS)|Число/с|Максимальная|Удаленные входящие пакеты (атака DDoS)|Нет измерений|
|PacketsForwardedDDoS|Перенаправленные входящие пакеты (атака DDoS)|Число/с|Максимальная|Перенаправленные входящие пакеты (атака DDoS)|Нет измерений|
|TCPPacketsInDDoS|Входящие пакеты TCP (атака DDoS)|Число/с|Максимальная|Входящие пакеты TCP (атака DDoS)|Нет измерений|
|TCPPacketsDroppedDDoS|Удаленные входящие пакеты TCP (атака DDoS)|Число/с|Максимальная|Удаленные входящие пакеты TCP (атака DDoS)|Нет измерений|
|TCPPacketsForwardedDDoS|Перенаправленные входящие пакеты TCP (атака DDoS)|Число/с|Максимальная|Перенаправленные входящие пакеты TCP (атака DDoS)|Нет измерений|
|UDPPacketsInDDoS|Входящие пакеты UDP (атака DDoS)|Число/с|Максимальная|Входящие пакеты UDP (атака DDoS)|Нет измерений|
|UDPPacketsDroppedDDoS|Удаленные входящие пакеты UDP (атака DDoS)|Число/с|Максимальная|Удаленные входящие пакеты UDP (атака DDoS)|Нет измерений|
|UDPPacketsForwardedDDoS|Перенаправленные входящие пакеты UDP (атака DDoS)|Число/с|Максимальная|Перенаправленные входящие пакеты UDP (атака DDoS)|Нет измерений|
|BytesInDDoS|Входящие байты (атака DDoS)|Байт/с|Максимальная|Входящие байты (атака DDoS)|Нет измерений|
|BytesDroppedDDoS|Удаленные входящие байты (атака DDoS)|Байт/с|Максимальная|Удаленные входящие байты (атака DDoS)|Нет измерений|
|BytesForwardedDDoS|Перенаправленные входящие байты (атака DDoS)|Байт/с|Максимальная|Перенаправленные входящие байты (атака DDoS)|Нет измерений|
|TCPBytesInDDoS|Входящие байты TCP (атака DDoS)|Байт/с|Максимальная|Входящие байты TCP (атака DDoS)|Нет измерений|
|TCPBytesDroppedDDoS|Удаленные входящие байты TCP (атака DDoS)|Байт/с|Максимальная|Удаленные входящие байты TCP (атака DDoS)|Нет измерений|
|TCPBytesForwardedDDoS|Перенаправленные входящие байты TCP (атака DDoS)|Байт/с|Максимальная|Перенаправленные входящие байты TCP (атака DDoS)|Нет измерений|
|UDPBytesInDDoS|Входящие байты UDP (атака DDoS)|Байт/с|Максимальная|Входящие байты UDP (атака DDoS)|Нет измерений|
|UDPBytesDroppedDDoS|Удаленные входящие байты UDP (атака DDoS)|Байт/с|Максимальная|Удаленные входящие байты UDP (атака DDoS)|Нет измерений|
|UDPBytesForwardedDDoS|Перенаправленные входящие байты UDP (атака DDoS)|Байт/с|Максимальная|Перенаправленные входящие байты UDP (атака DDoS)|Нет измерений|
|IfUnderDDoSAttack|Выполняется ли атака DDoS|Количество|Максимальная|Выполняется ли атака DDoS|Нет измерений|
|DDoSTriggerTCPPackets|Входящие пакеты TCP для активации защиты от атаки DDoS|Число/с|Максимальная|Входящие пакеты TCP для активации защиты от атаки DDoS|Нет измерений|
|DDoSTriggerUDPPackets|Входящие пакеты UDP для активации защиты от атаки DDoS|Число/с|Максимальная|Входящие пакеты UDP для активации защиты от атаки DDoS|Нет измерений|
|DDoSTriggerSYNPackets|Количество входящих пакетов SYN активировать DDoS устранение рисков|Число/с|Максимальная|Количество входящих пакетов SYN активировать DDoS устранение рисков|Нет измерений|
|VipAvailability|Доступность|Количество|Средняя|Средняя доступность IP-адреса за период времени|Порт|
|ByteCount|Количество байтов|Количество|Итого|Общее количество байтов, переданных за период времени|Port, Direction|
|PacketCount|Количество пакетов|Количество|Итого|Общее количество пакетов, переданных за период времени|Port, Direction|
|SynCount|Количество пакетов SYN|Количество|Итого|Общее количество пакетов SYN, переданных за период времени|Port, Direction|

## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|PacketsInDroppedVMProtection|Отброшенных входящих пакетов для защиты виртуальных Машин|Число/с|Средняя|Отброшенных входящих пакетов для защиты виртуальных Машин|Нет измерений|
|PacketsOutDroppedVMProtection|Отброшенных исходящих пакетов для защиты виртуальных Машин|Число/с|Средние|Отброшенных исходящих пакетов для защиты виртуальных Машин|Нет измерений|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|Throughput|Throughput|Байт/с|Итого|Количество байтов в секунду, обрабатываемых шлюзом приложений|Нет измерений|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|TunnelAverageBandwidth|Пропускная способность туннеля|Байт/с|Средняя|Средняя пропускная способность туннеля в байтах в секунду|ConnectionName, RemoteIP|
|TunnelEgressBytes|Исходящие байты туннеля|Байты|Итого|Исходящих байт туннель|ConnectionName, RemoteIP|
|TunnelIngressBytes|Входящие байты туннеля|Байты|Итого|Входящих байт туннель|ConnectionName, RemoteIP|
|TunnelEgressPackets|Исходящие пакеты туннеля|Количество|Итого|Количество исходящих пакетов в туннель|ConnectionName, RemoteIP|
|TunnelIngressPackets|Входящие пакеты туннеля|Количество|Итого|Количество входящих пакетов в туннель|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Удаленные входящие пакеты туннеля (несоответствие селектора трафика)|Количество|Итого|Исходящих drop число пакетов от трафика селектор несоответствие туннелей|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Удаленные исходящие пакеты туннеля (несоответствие селектора трафика)|Количество|Итого|Входящее количество drop пакетов из несоответствие селектор трафика туннелей|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Число/с|Средние|Входящий трафик Azure в секунду (в битах)|Нет измерений|
|BitsOutPerSecond|BitsOutPerSecond|Число/с|Средняя|Исходящий трафик Azure в секунду (в битах)|Нет измерений|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|QpsByEndpoint|Запросы, выполняемые возвращаемой конечной точкой|Количество|Итого|Сколько раз конечная точка диспетчера трафика возвращалась в заданный период времени|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Проверка состояния конечной точки с помощью конечной точки|Количество|Максимальная|1, если состояние проверки конечной точки Enabled, в противном случае используется значение 0.|EndpointName|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|registration.all|Операции регистрации|Количество|Итого|Число всех успешных операций регистрации (создание, запрос, обновление и удаление). |Нет измерений|
|registration.create|Операции создания регистрации|Количество|Итого|Число всех успешных операций создания регистраций.|Нет измерений|
|registration.update|Операции обновления регистрации|Количество|Итого|Число всех успешных операций обновления регистраций.|Нет измерений|
|registration.get|Операции чтения регистрации|Количество|Итого|Число всех успешных запросов регистрации.|Нет измерений|
|registration.delete|Операции удаления регистрации|Количество|Итого|Число всех успешных операций удаления регистраций.|Нет измерений|
|incoming|Входящие сообщения|Количество|Итого|Число всех успешно отправленных вызовов API. |Нет измерений|
|incoming.scheduled|Отправлено запланированных push-уведомлений|Количество|Итого|Отменено запланированных push-уведомлений|Нет измерений|
|incoming.scheduled.cancel|Отменено запланированных push-уведомлений|Количество|Итого|Отменено запланированных push-уведомлений|Нет измерений|
|scheduled.pending|Запланированных уведомлений в состоянии ожидания|Количество|Итого|Запланированных уведомлений в состоянии ожидания|Нет измерений|
|installation.all|Операции управления установкой|Количество|Итого|Операции управления установкой|Нет измерений|
|installation.get|Операции получения установки|Количество|Итого|Операции получения установки|Нет измерений|
|installation.upsert|Операции создания или обновления установки|Количество|Итого|Операции создания или обновления установки|Нет измерений|
|installation.patch|Операции исправления установки|Количество|Итого|Операции исправления установки|Нет измерений|
|installation.delete|Операции удаления установки|Количество|Итого|Операции удаления установки|Нет измерений|
|outgoing.allpns.success|Успешные уведомления|Количество|Итого|Общее число успешных уведомлений.|Нет измерений|
|outgoing.allpns.invalidpayload|Ошибки полезных данных|Количество|Итого|Количество неудачных отправлений из-за того, что PNS вернула ошибку полезных данных.|Нет измерений|
|outgoing.allpns.pnserror|Ошибки внешней системы уведомлений|Количество|Итого|Количество неудачных отправлений из-за ошибки связи с PNS (за исключением проблем с проверкой подлинности).|Нет измерений|
|outgoing.allpns.channelerror|Ошибки канала|Количество|Итого|Количество неудачных отправлений из-за недопустимого канала, не связанного с соответствующим регулируемым или просроченным приложением.|Нет измерений|
|outgoing.allpns.badorexpiredchannel|Ошибки из-за поврежденного или просроченного канала|Количество|Итого|Количество неудачных отправлений из-за недопустимого или просроченного канала, маркера или идентификатора регистрации в регистрации.|Нет измерений|
|outgoing.wns.success|Успешные уведомления WNS|Количество|Итого|Общее число успешных уведомлений.|Нет измерений|
|outgoing.wns.invalidcredentials|Ошибки авторизации WNS (недопустимые учетные данные)|Количество|Итого|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных. (Windows Live не распознает учетные данные.)|Нет измерений|
|outgoing.wns.badchannel|Ошибка из-за поврежденного канала WNS|Количество|Итого|Количество неудачных отправлений из-за ошибки распознавания универсального кода ресурса (URI) канала в регистрации (состояние WNS: 404 — не найдено).|Нет измерений|
|outgoing.wns.expiredchannel|Ошибка из-за просроченного канала WNS|Количество|Итого|Количество неудачных отправлений из-за просроченного универсального кода ресурса (URI) канала (состояние WNS: 410 — потеряно).|Нет измерений|
|outgoing.wns.throttled|Регулируемые уведомления WNS|Количество|Итого|Количество неудачных отправлений из-за регулирования этого приложения с помощью WNS (состояние WNS: 406 — неприемлемо).|Нет измерений|
|outgoing.wns.tokenproviderunreachable|Ошибки авторизации WNS (нет доступа)|Количество|Итого|Windows Live недоступна.|Нет измерений|
|outgoing.wns.invalidtoken|Ошибки авторизации WNS (недопустимый маркер)|Количество|Итого|WNS предоставлен недопустимый маркер (состояние WNS: 401 — не санкционировано).|Нет измерений|
|outgoing.wns.wrongtoken|Ошибки авторизации WNS (неправильный маркер)|Количество|Итого|Маркер, предоставленный для WNS, допустимый, но он предназначен для другого приложения (состояние WNS: 403 — запрещено). Это возможно, если универсальный код ресурса (URI) канала в регистрации связан с другим приложением. Убедитесь, что клиентское приложение связано с приложением, учетные данные которого находятся в центре уведомлений.|Нет измерений|
|outgoing.wns.invalidnotificationformat|Недопустимый формат уведомления WNS|Количество|Итого|Недопустимый формат уведомления (состояние WNS: 400). Обратите внимание, что WNS не отклоняет все недопустимые полезные данные.|Нет измерений|
|outgoing.wns.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления WNS|Количество|Итого|Полезные данные уведомления слишком большие (состояние WNS: 413).|Нет измерений|
|outgoing.wns.channelthrottled|Канал WNS регулируется|Количество|Итого|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (заголовок ответа WNS: состояние уведомления X-WNS: канал регулируется).|Нет измерений|
|outgoing.wns.channeldisconnected|Канал WNS отсоединен|Количество|Итого|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (заголовок ответа WNS: состояние подключения устройства X-WNS: отключено).|Нет измерений|
|outgoing.wns.dropped|Пропущенные уведомления WNS|Количество|Итого|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (состояние уведомления X-WNS: пропущено. Состояние подключения устройства не X-WNS: отключено).|Нет измерений|
|outgoing.wns.pnserror|Ошибки WNS|Количество|Итого|Уведомление не доставлено из-за ошибок связи с WNS.|Нет измерений|
|outgoing.wns.authenticationerror|Ошибки проверки подлинности WNS|Количество|Итого|Уведомление не доставлено из-за ошибок связи Windows Live с использованием недопустимых учетных данных или неправильного маркера.|Нет измерений|
|outgoing.apns.success|Успешные уведомления APNS|Количество|Итого|Общее число успешных уведомлений.|Нет измерений|
|outgoing.apns.invalidcredentials|Ошибки авторизации APNS|Количество|Итого|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Нет измерений|
|outgoing.apns.badchannel|Ошибка из-за поврежденного канала APNS|Количество|Итого|Количество неудачных отправлений из-за недопустимого маркера (код состояния APNS: 8).|Нет измерений|
|outgoing.apns.expiredchannel|Ошибка из-за просроченного канала APNS|Количество|Итого|Количество маркеров, которые были аннулированы каналом обратной связи APNS.|Нет измерений|
|outgoing.apns.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления APNS|Количество|Итого|Количество неудачных отправлений из-за большого объема полезных данных (код состояния APNS: 7).|Нет измерений|
|outgoing.apns.pnserror|Ошибки APNS|Количество|Итого|Количество неудачных отправлений из-за ошибок связи с APNS.|Нет измерений|
|outgoing.gcm.success|Успешные уведомления GCM|Количество|Итого|Общее число успешных уведомлений.|Нет измерений|
|outgoing.gcm.invalidcredentials|Ошибки авторизации GCM (недопустимые учетные данные)|Количество|Итого|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Нет измерений|
|outgoing.gcm.badchannel|Ошибка из-за поврежденного канала GCM|Количество|Итого|Количество неудачных отправлений из-за ошибки распознавания идентификатора регистрации в регистрации (результат GCM: недействительная регистрация).|Нет измерений|
|outgoing.gcm.expiredchannel|Ошибка из-за просроченного канала GCM|Количество|Итого|Количество неудачных отправлений из-за просроченного идентификатора регистрации в регистрации (результат GCM: не зарегистрировано).|Нет измерений|
|outgoing.gcm.throttled|Регулируемые уведомления GCM|Количество|Итого|Количество неудачных отправлений из-за регулирования этого приложения GCM (код состояния GCM: 501–599; либо результат: недоступен).|Нет измерений|
|outgoing.gcm.invalidnotificationformat|Недопустимый формат уведомления GCM|Количество|Итого|Количество неудачных отправлений из-за неправильного формата полезных данных (результат GCM: недопустимый ключ данных или недопустимый TTL).|Нет измерений|
|outgoing.gcm.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления GCM|Количество|Итого|Количество неудачных отправлений из-за большого объема полезных данных (результат GCM: слишком большое сообщение).|Нет измерений|
|outgoing.gcm.wrongchannel|Ошибка из-за неправильного канала GCM|Количество|Итого|Количество неудачных отправлений из-за того, что идентификатор регистрации в регистрации не связан с текущим приложением (результат GCM: недопустимое имя пакета).|Нет измерений|
|outgoing.gcm.pnserror|Ошибки GCM|Количество|Итого|Количество неудачных отправлений из-за ошибок связи с GCM.|Нет измерений|
|outgoing.gcm.authenticationerror|Ошибки проверки подлинности GCM|Количество|Итого|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных по причине использования блокированных учетных данных или неправильно настроенного идентификатора отправителя в приложении (результат GCM: несовпадающий код отправителя).|Нет измерений|
|outgoing.mpns.success|Успешные уведомления MPNS|Количество|Итого|Общее число успешных уведомлений.|Нет измерений|
|outgoing.mpns.invalidcredentials|Недопустимые учетные данные MPNS|Количество|Итого|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Нет измерений|
|outgoing.mpns.badchannel|Ошибка из-за поврежденного канала MPNS|Количество|Итого|Количество неудачных отправлений из-за ошибки распознавания универсального кода ресурса (URI) канала в регистрации (состояние MPNS: 404 — не найдено).|Нет измерений|
|outgoing.mpns.throttled|Регулируемые уведомления MPNS|Количество|Итого|Количество неудачных отправлений из-за регулирования этого приложения MPNS (WNS MPNS: 406 — неприемлемо).|Нет измерений|
|outgoing.mpns.invalidnotificationformat|Недопустимый формат уведомления MPNS|Количество|Итого|Количество неудачных отправлений из-за большого объема полезных данных уведомления.|Нет измерений|
|outgoing.mpns.channeldisconnected|Канал MPNS отсоединен|Количество|Итого|Количество неудачных отправлений из-за отсоединения универсального кода ресурса (URI) канала в регистрации (состояние MPNS: 412 — не найдено).|Нет измерений|
|outgoing.mpns.dropped|Пропущенные уведомления MPNS|Количество|Итого|Количество отправлений, пропущенных MPNS (заголовок ответа MPNS: состояние уведомления X — очередь переполнена или подавлено).|Нет измерений|
|outgoing.mpns.pnserror|Ошибки MPNS|Количество|Итого|Количество неудачных отправлений из-за ошибок связи с MPNS.|Нет измерений|
|outgoing.mpns.authenticationerror|Ошибки проверки подлинности MPNS|Количество|Итого|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Нет измерений|
|notificationhub.pushes|Все исходящие уведомления|Количество|Итого|Все исходящие уведомления из центра уведомлений.|Нет измерений|
|incoming.all.requests|Все входящие запросы|Количество|Итого|Общее количество входящих запросов для концентратора уведомлений|Нет измерений|
|Incoming.all.failedrequests|Все неудачные входящие запросы|Количество|Итого|Общее количество неудачных входящих запросов для концентратора уведомлений|Нет измерений|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|ListenerConnections — выполненные|ListenerConnections — выполненные|Количество|Итого|Выполненные ListenerConnections для Microsoft.Relay.|EntityName|
|ListenerConnections — ClientError|ListenerConnections — ClientError|Количество|Итого|ClientError в ListenerConnections для Microsoft.Relay.|EntityName|
|ListenerConnections — ServerError|ListenerConnections — ServerError|Количество|Итого|ServerError в ListenerConnections для Microsoft.Relay.|EntityName|
|SenderConnections — выполненные|SenderConnections — выполненные|Количество|Итого|Выполненные SenderConnections для Microsoft.Relay.|EntityName|
|SenderConnections — ClientError|SenderConnections — ClientError|Количество|Итого|ClientError в SenderConnections для Microsoft.Relay.|EntityName|
|SenderConnections — ServerError|SenderConnections — ServerError|Количество|Итого|ServerError в SenderConnections для Microsoft.Relay.|EntityName|
|ListenerConnections — TotalRequests|ListenerConnections — TotalRequests|Количество|Итого|Всего ListenerConnections для Microsoft.Relay.|EntityName|
|SenderConnections — TotalRequests|SenderConnections — TotalRequests|Количество|Итого|Всего запросов SenderConnections для Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Количество|Итого|Всего ActiveConnections для Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Количество|Итого|Всего ActiveListeners для Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Количество|Итого|Всего BytesTransferred для Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Количество|Итого|Всего ListenerDisconnects для Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Количество|Итого|Всего SenderDisconnects для Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|SearchLatency|Search Latency|Секунды|Средние|Среднее время задержки поиска для службы поиска.|Нет измерений|
|SearchQueriesPerSecond|Search queries per second|Число/с|Средние|Число поисковых запросов в секунду для службы поиска.|Нет измерений|
|ThrottledSearchQueriesPercentage|Throttled search queries percentage|Процент|Средние|Процент отрегулированных поисковых запросов для службы поиска.|Нет измерений|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|SuccessfulRequests|Количество успешных запросов (Предварительная версия)|Количество|Итого|Общее количество успешных запросов в пространстве имен (Предварительная версия)|EntityName|
|ServerErrors|Ошибки на сервере. (предварительная версия)|Количество|Итого|Ошибки на сервере для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|UserErrors|Ошибки пользователей. (предварительная версия)|Количество|Итого|Ошибки пользователей для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|ThrottledRequests|Регулируемые запросы. (предварительная версия)|Количество|Итого|Регулируемые запросы для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|IncomingRequests|Входящие запросы (Предварительная версия)|Количество|Итого|Входящие запросы для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|IncomingMessages|Входящие сообщения (Предварительная версия)|Количество|Итого|Входящие сообщения для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|OutgoingMessages|Исходящих сообщений (Предварительная версия)|Количество|Итого|Исходящие сообщения для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|ActiveConnections|ActiveConnections (Предварительная версия)|Количество|Итого|Число активных подключений для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|ConnectionsOpened|Открытые подключения. (предварительная версия)|Количество|Итого|Открытые подключения для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|ConnectionsClosed|Закрытые подключения. (предварительная версия)|Количество|Итого|Закрытые подключения для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|CPUXNS|CPU usage per namespace|Процент|Максимальная|Метрика использования ЦП пространства имен служебной шины для премиум-обслуживания|Нет измерений|
|WSXNS|Memory size usage per namespace|Процент|Максимальная|Метрика использования памяти пространства имен служебной шины для премиум-обслуживания|Нет измерений|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Процент использования ЦП|Процент|Средние|Процент использования ЦП|Нет измерений|
|physical_data_read_percent|Процент операций ввода/вывода данных|Процент|Средние|Процент операций ввода/вывода данных|Нет измерений|
|log_write_percent|Log IO percentage|Процент|Средние|Log IO percentage|Нет измерений|
|dtu_consumption_percent|Процент использования DTU|Процент|Средние|Процент использования DTU|Нет измерений|
|storage|Total database size|Байты|Максимальная|Total database size|Нет измерений|
|connection_successful|Успешные подключения|Количество|Итого|Успешные подключения|Нет измерений|
|connection_failed|Неудачные подключения|Количество|Итого|Неудачные подключения|Нет измерений|
|blocked_by_firewall|Заблокировано брандмауэром|Количество|Итого|Заблокировано брандмауэром|Нет измерений|
|deadlock|Взаимоблокировки|Количество|Итого|Взаимоблокировки|Нет измерений|
|storage_percent|Размер базы данных в процентах|Процент|Максимальная|Размер базы данных в процентах|Нет измерений|
|xtp_storage_percent|Процент хранилища выполняющейся в памяти OLTP|Процент|Средние|Процент хранилища выполняющейся в памяти OLTP|Нет измерений|
|workers_percent|Workers percentage|Процент|Средние|Workers percentage|Нет измерений|
|sessions_percent|Процент использования сеансов|Процент|Средние|Процент использования сеансов|Нет измерений|
|dtu_limit|Лимит DTU|Количество|Средние|Лимит DTU|Нет измерений|
|dtu_used|DTU used|Количество|Средние|DTU used|Нет измерений|
|dwu_limit|Лимит DWU|Количество|Максимальная|Лимит DWU|Нет измерений|
|dwu_consumption_percent|DWU percentage|Процент|Максимальная|DWU percentage|Нет измерений|
|dwu_used|DWU used|Количество|Максимальная|DWU used|Нет измерений|
|dw_cpu_percent|Процент использования ЦП на уровне узла DW|Процент|Средние|Процент использования ЦП на уровне узла DW|dw_logical_node_id|
|dw_physical_data_read_percent|Процент операций ввода-вывода данных на уровне узла DW|Процент|Средние|Процент операций ввода-вывода данных на уровне узла DW|dw_logical_node_id|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Процент использования ЦП|Процент|Средние|Процент использования ЦП|Нет измерений|
|database_cpu_percent|Процент использования ЦП|Процент|Средние|Процент использования ЦП|DatabaseResourceId|
|physical_data_read_percent|Процент операций ввода/вывода данных|Процент|Средняя|Процент операций ввода/вывода данных|Нет измерений|
|database_physical_data_read_percent|Процент операций ввода/вывода данных|Процент|Средние|Процент операций ввода/вывода данных|DatabaseResourceId|
|log_write_percent|Log IO percentage|Процент|Средние|Log IO percentage|Нет измерений|
|database_log_write_percent|Log IO percentage|Процент|Средние|Log IO percentage|DatabaseResourceId|
|dtu_consumption_percent|Процент использования DTU|Процент|Средние|Процент использования DTU|Нет измерений|
|database_dtu_consumption_percent|Процент использования DTU|Процент|Средняя|Процент использования DTU|DatabaseResourceId|
|storage_percent|Storage percentage|Процент|Средняя|Storage percentage|Нет измерений|
|workers_percent|Workers percentage|Процент|Средние|Workers percentage|Нет измерений|
|database_workers_percent|Workers percentage|Процент|Средняя|Workers percentage|DatabaseResourceId|
|sessions_percent|Процент использования сеансов|Процент|Средние|Процент использования сеансов|Нет измерений|
|database_sessions_percent|Процент использования сеансов|Процент|Средние|Процент использования сеансов|DatabaseResourceId|
|eDTU_limit|eDTU limit|Количество|Средние|eDTU limit|Нет измерений|
|storage_limit|Storage limit|Байты|Средняя|Storage limit|Нет измерений|
|eDTU_used|eDTU used|Количество|Средние|eDTU used|Нет измерений|
|storage_used|Storage used|Байты|Средняя|Storage used|Нет измерений|
|database_storage_used|Storage used|Байты|Средняя|Storage used|DatabaseResourceId|
|xtp_storage_percent|Процент хранилища выполняющейся в памяти OLTP|Процент|Средние|Процент хранилища выполняющейся в памяти OLTP|Нет измерений|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|dtu_consumption_percent|Процент использования DTU|Процент|Средние|Процент использования DTU|ElasticPoolResourceId|
|database_dtu_consumption_percent|Процент использования DTU|Процент|Средняя|Процент использования DTU|DatabaseResourceId, ElasticPoolResourceId|
|storage_used|Storage used|Байты|Средние|Storage used|ElasticPoolResourceId|
|database_storage_used|Storage used|Байты|Средняя|Storage used|DatabaseResourceId, ElasticPoolResourceId|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|UsedCapacity|Используемая емкость|Байты|Средняя|Используемая емкость учетной записи|Нет измерений|
|Транзакции|Транзакции|Количество|Итого|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName|
|Входящие|Входящие|Байты|Итого|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName|
|Исходящие|Исходящие|Байты|Итого|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Средние|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Средняя|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName|
|Доступность|Доступность|Процент|Средние|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|BlobCapacity|Емкость больших двоичных объектов|Байты|Средние|Объем хранилища, используемый службой BLOB-объектов учетной записи хранения, в байтах.|BlobType|
|BlobCount|Количество больших двоичных объектов|Количество|Средняя|Количество больших двоичных объектов в службе BLOB-объектов учетной записи хранения.|BlobType|
|ContainerCount|Количество контейнеров больших двоичных объектов|Количество|Средние|Количество контейнеров в службе BLOB-объектов учетной записи хранения.|Нет измерений|
|Транзакции|Транзакции|Количество|Итого|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName|
|Входящие|Входящие|Байты|Итого|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName|
|Исходящие|Исходящие|Байты|Итого|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Средние|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Средние|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName|
|Доступность|Доступность|Процент|Средние|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|TableCapacity|Емкость таблицы|Байты|Средние|Объем хранилища, используемый службой таблиц учетной записи хранения, в байтах.|Нет измерений|
|TableCount|Количество таблиц|Количество|Средняя|Количество таблиц в службе таблиц учетной записи хранения.|Нет измерений|
|TableEntityCount|Количество сущностей таблиц|Количество|Средние|Количество сущностей таблиц в службе таблиц учетной записи хранения.|Нет измерений|
|Транзакции|Транзакции|Количество|Итого|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName|
|Входящие|Входящие|Байты|Итого|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName|
|Исходящие|Исходящие|Байты|Итого|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Средняя|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Средние|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName|
|Доступность|Доступность|Процент|Средние|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|QueueCapacity|Емкость очереди|Байты|Средние|Объем хранилища, используемый службой очередей учетной записи хранения, в байтах.|Нет измерений|
|QueueCount|Количество очередей|Количество|Средние|Количество очередей в службе очередей учетной записи хранения.|Нет измерений|
|QueueMessageCount|Количество сообщений очереди|Количество|Средняя|Приблизительное количество сообщений очередей в службе очередей учетной записи хранения.|Нет измерений|
|Транзакции|Транзакции|Количество|Итого|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName|
|Входящие|Входящие|Байты|Итого|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName|
|Исходящие|Исходящие|Байты|Итого|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Средняя|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Средние|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName|
|Доступность|Доступность|Процент|Средняя|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|FileCapacity|Емкость файла|Байты|Средние|Объем хранилища, используемый службой файлов учетной записи хранения, в байтах.|Нет измерений|
|FileCount|Количество файлов|Количество|Средняя|Количество файлов в службе файлов учетной записи хранения.|Нет измерений|
|FileShareCount|Количество общих файловых ресурсов|Количество|Средние|Количество общих файловых ресурсов в службе файлов учетной записи хранения.|Нет измерений|
|Транзакции|Транзакции|Количество|Итого|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName|
|Входящие|Входящие|Байты|Итого|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName|
|Исходящие|Исходящие|Байты|Итого|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Средняя|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Средние|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName|
|Доступность|Доступность|Процент|Средние|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|ResourceUtilization|Использование единиц потоковой передачи (%)|Процент|Максимальная|Использование единиц потоковой передачи (%)|Нет измерений|
|InputEvents|Входные события|Количество|Итого|Входные события|Нет измерений|
|InputEventBytes|Байты входного события|Байты|Итого|Байты входного события|Нет измерений|
|LateInputEvents|События позднего ввода|Количество|Итого|События позднего ввода|Нет измерений|
|OutputEvents|Выходные события|Количество|Итого|Выходные события|Нет измерений|
|ConversionErrors|Ошибки преобразования данных|Количество|Итого|Ошибки преобразования данных|Нет измерений|
|Errors|Ошибки среды выполнения|Количество|Итого|Ошибки среды выполнения|Нет измерений|
|DroppedOrAdjustedEvents|Неупорядоченные события|Количество|Итого|Неупорядоченные события|Нет измерений|
|AMLCalloutRequests|Запросы функций|Количество|Итого|Запросы функций|Нет измерений|
|AMLCalloutFailedRequests|Неудачные запросы функций|Количество|Итого|Неудачные запросы функций|Нет измерений|
|AMLCalloutInputEvents|События функций|Количество|Итого|События функций|Нет измерений|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|CpuPercentage|Процент использования ЦП|Процент|Средняя|Процент использования ЦП|Экземпляр|
|MemoryPercentage|Процент использования памяти|Процент|Средние|Процент использования памяти|Экземпляр|
|DiskQueueLength|Длина дисковой очереди|Количество|Средние|Длина дисковой очереди|Экземпляр|
|HttpQueueLength|Длина очереди HTTP:|Количество|Средняя|Длина очереди HTTP:|Экземпляр|
|BytesReceived|Входящие данные:|Байты|Итого|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Итого|Выходные данные|Экземпляр|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (за исключением функций)

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|CpuTime|Время ЦП:|Секунды|Итого|Время ЦП:|Экземпляр|
|Requests|Requests|Количество|Итого|Requests|Экземпляр|
|BytesReceived|Входящие данные:|Байты|Итого|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Итого|Выходные данные|Экземпляр|
|Http101|Http 101|Количество|Итого|Http 101|Экземпляр|
|Http2xx|HTTP 2xx|Количество|Итого|HTTP 2xx|Экземпляр|
|Http3xx|HTTP 3xx:|Количество|Итого|HTTP 3xx:|Экземпляр|
|Http401|HTTP 401:|Количество|Итого|HTTP 401:|Экземпляр|
|Http403|HTTP 403:|Количество|Итого|HTTP 403:|Экземпляр|
|Http404|HTTP 404:|Количество|Итого|HTTP 404:|Экземпляр|
|Http406|HTTP 406:|Количество|Итого|HTTP 406:|Экземпляр|
|Http4xx|HTTP 4xx:|Количество|Итого|HTTP 4xx:|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Количество|Итого|Ошибки HTTP-сервера:|Экземпляр|
|MemoryWorkingSet|рабочий набор памяти;|Байты|Средние|рабочий набор памяти;|Экземпляр|
|AverageMemoryWorkingSet|средний размер рабочего набора памяти;|Байты|Средние|средний размер рабочего набора памяти;|Экземпляр|
|AverageResponseTime|Среднее время ответа:|Секунды|Средние|Среднее время ответа:|Экземпляр|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (функции)

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|BytesReceived|Входящие данные:|Байты|Итого|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Итого|Выходные данные|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Количество|Итого|Ошибки HTTP-сервера:|Экземпляр|
|MemoryWorkingSet|рабочий набор памяти;|Байты|Средние|рабочий набор памяти;|Экземпляр|
|AverageMemoryWorkingSet|средний размер рабочего набора памяти;|Байты|Средние|средний размер рабочего набора памяти;|Экземпляр|
|FunctionExecutionUnits|Function Execution Units|Количество|Итого|Единицы выполнения функции.|Экземпляр|
|FunctionExecutionCount|Function Execution Count|Количество|Итого|Function Execution Count|Экземпляр|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|CpuTime|Время ЦП:|Секунды|Итого|Время ЦП:|Экземпляр|
|Requests|Requests|Количество|Итого|Requests|Экземпляр|
|BytesReceived|Входящие данные:|Байты|Итого|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Итого|Выходные данные|Экземпляр|
|Http101|Http 101|Количество|Итого|Http 101|Экземпляр|
|Http2xx|HTTP 2xx|Количество|Итого|HTTP 2xx|Экземпляр|
|Http3xx|HTTP 3xx:|Количество|Итого|HTTP 3xx:|Экземпляр|
|Http401|HTTP 401:|Количество|Итого|HTTP 401:|Экземпляр|
|Http403|HTTP 403:|Количество|Итого|HTTP 403:|Экземпляр|
|Http404|HTTP 404:|Количество|Итого|HTTP 404:|Экземпляр|
|Http406|HTTP 406:|Количество|Итого|HTTP 406:|Экземпляр|
|Http4xx|HTTP 4xx:|Количество|Итого|HTTP 4xx:|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Количество|Итого|Ошибки HTTP-сервера:|Экземпляр|
|MemoryWorkingSet|рабочий набор памяти;|Байты|Средние|рабочий набор памяти;|Экземпляр|
|AverageMemoryWorkingSet|средний размер рабочего набора памяти;|Байты|Средняя|средний размер рабочего набора памяти;|Экземпляр|
|AverageResponseTime|Среднее время ответа:|Секунды|Средние|Среднее время ответа:|Экземпляр|
|FunctionExecutionUnits|Function Execution Units|Количество|Средние|Единицы выполнения функции.|Экземпляр|
|FunctionExecutionCount|Function Execution Count|Количество|Средние|Function Execution Count|Экземпляр|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|Requests|Requests|Количество|Итого|Requests|Экземпляр|
|BytesReceived|Входящие данные:|Байты|Итого|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Итого|Выходные данные|Экземпляр|
|Http101|Http 101|Количество|Итого|Http 101|Экземпляр|
|Http2xx|HTTP 2xx|Количество|Итого|HTTP 2xx|Экземпляр|
|Http3xx|HTTP 3xx:|Количество|Итого|HTTP 3xx:|Экземпляр|
|Http401|HTTP 401:|Количество|Итого|HTTP 401:|Экземпляр|
|Http403|HTTP 403:|Количество|Итого|HTTP 403:|Экземпляр|
|Http404|HTTP 404:|Количество|Итого|HTTP 404:|Экземпляр|
|Http406|HTTP 406:|Количество|Итого|HTTP 406:|Экземпляр|
|Http4xx|HTTP 4xx:|Количество|Итого|HTTP 4xx:|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Количество|Итого|Ошибки HTTP-сервера:|Экземпляр|
|AverageResponseTime|Среднее время ответа:|Секунды|Средняя|Среднее время ответа:|Экземпляр|
|CpuPercentage|Процент использования ЦП|Процент|Средние|Процент использования ЦП|Экземпляр|
|MemoryPercentage|Процент использования памяти|Процент|Средние|Процент использования памяти|Экземпляр|
|DiskQueueLength|Длина дисковой очереди|Количество|Средние|Длина дисковой очереди|Экземпляр|
|HttpQueueLength|Длина очереди HTTP:|Количество|Средняя|Длина очереди HTTP:|Экземпляр|
|ActiveRequests|Активные запросы|Количество|Итого|Активные запросы|Экземпляр|
|TotalFrontEnds|Общее число внешних интерфейсов|Количество|Средние|Общее число внешних интерфейсов|Экземпляр|
|SmallAppServicePlanInstances|Рабочие роли плана службы приложений уровня "Малый"|Количество|Средние|Рабочие роли плана службы приложений уровня "Малый"|Экземпляр|
|MediumAppServicePlanInstances|Рабочие процессы плана службы приложений уровня "Средний"|Количество|Средняя|Рабочие процессы плана службы приложений уровня "Средний"|Экземпляр|
|LargeAppServicePlanInstances|Рабочие процессы плана службы приложений уровня "Крупный"|Количество|Средние|Рабочие процессы плана службы приложений уровня "Крупный"|Экземпляр|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|WorkersTotal|Общее количество рабочих процессов|Количество|Средние|Общее количество рабочих процессов|Экземпляр|
|WorkersAvailable|Доступные рабочие процессы|Количество|Средние|Доступные рабочие процессы|Экземпляр|
|WorkersUsed|Использованные рабочие процессы|Количество|Средние|Использованные рабочие процессы|Экземпляр|
|CpuPercentage|Процент использования ЦП|Процент|Средние|Процент использования ЦП|Экземпляр|
|MemoryPercentage|Процент использования памяти|Процент|Средняя|Процент использования памяти|Экземпляр|

## <a name="next-steps"></a>Дальнейшие действия
* [Прочитайте о метриках в Azure Monitor](monitoring-overview-metrics.md)
* [Создайте оповещения на основе метрик](insights-receive-alert-notifications.md)
* [Экспортируйте метрики в хранилище, концентратор событий или Log Analytics](monitoring-overview-of-diagnostic-logs.md)
