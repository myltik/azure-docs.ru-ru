---
title: 'Azure Monitor: поддерживаемые метрики для каждого типа ресурсов | Документация Майкрософт'
description: Список метрик, доступных для каждого типа ресурса в Azure Monitor.
author: anirudhcavale
manager: ashwink
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 63d4ac65-1688-40d1-85c8-7cd408285b0f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: ancav
ms.openlocfilehash: c9dab276b39b1ceb55851e865f9166e3e9e7fee8
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="supported-metrics-with-azure-monitor"></a>Метрики, поддерживаемые Azure Monitor
Azure Monitor обеспечивает несколько способов взаимодействия с метриками, включая создание диаграмм метрик на портале, доступ к метрикам через REST API или запрос метрик с помощью PowerShell или интерфейса командной строки. Ниже приведен полный список метрик, доступных в настоящее время в конвейере метрик Azure Monitor. Другие метрики могут быть доступны на портале или при использовании интерфейсов API прежних версий. Этот список содержит только метрики, которые доступны при использовании объединенного конвейера метрик Azure Monitor. Чтобы запросить метрики с измерениями и получить к ним доступ, воспользуйтесь версией [2018-01-01 api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions).

> [!NOTE]
> Отправка многомерных метрик с помощью параметров диагностики сейчас не поддерживается. Метрики с измерениями экспортируются как преобразованные в плоскую структуру одномерные метрики, агрегированные по значениям измерений.
>
> *Пример.* Метрику "Входящие сообщения" в концентраторе событий можно изучить и вывести в виде диаграммы на уровне очереди. Но при экспорте с помощью параметров диагностики метрика будет представлена в виде всех входящих сообщений для всех очередей концентратора событий.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Средняя|QPU. Диапазоны: 0–100 для S1, 0–200 для S2 и 0–400 для S4.|ServerResourceType|
|memory_metric|Память|Байты|Средняя|Память. Диапазоны: 0–25 ГБ для S1, 0–50 ГБ для S2 и 0–100 ГБ для S4.|ServerResourceType|
|TotalConnectionRequests|Общее число запросов на подключение|Count|Среднее|Общее число запросов на подключение. Поступившие запросы.|ServerResourceType|
|SuccessfullConnectionsPerSec|Число успешных подключений в секунду|Число/с|Средняя|Частота успешных установок подключений.|ServerResourceType|
|TotalConnectionFailures|Общее число неудачных подключений|Count|Среднее|Общее число неудачных попыток подключения.|ServerResourceType|
|CurrentUserSessions|Текущие пользовательские сеансы|Count|Средняя|Текущее число установленных пользовательских сеансов.|ServerResourceType|
|QueryPoolBusyThreads|Занятые потоки в пуле запросов|Count|Среднее|Число занятых потоков в пуле потоков запросов.|ServerResourceType|
|CommandPoolJobQueueLength|Длина очереди заданий пула команд|Count|Средняя|Число заданий в очереди пула потоков команд.|ServerResourceType|
|ProcessingPoolJobQueueLength|Длина очереди заданий пула обработки|Count|Среднее|Количество заданий, не связанных с вводом-выводом, в очереди пула потоков обработки.|ServerResourceType|
|CurrentConnections|Подключение: текущие подключения|Count|Средняя|Текущее число установленных клиентских подключений.|ServerResourceType|
|CleanerCurrentPrice|Память: текущая цена очистки|Count|Средняя|Текущая цена памяти, $/байт/время, нормализованная до 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Память: сжимаемая память очистки|Байты|Средняя|Объем памяти (в байтах), доступной для очистки с помощью средства фоновой очистки.|ServerResourceType|
|CleanerMemoryNonshrinkable|Память: несжимаемая память очистки|Байты|Среднее|Объем памяти (в байтах), недоступной для очистки с помощью средства фоновой очистки.|ServerResourceType|
|MemoryUsage|Память: использование памяти|Байты|Средняя|Использование памяти процессом сервера согласно вычислениям стоимости памяти очистки. Равно сумме значений счетчика Process\PrivateBytes и объема сопоставленных в памяти данных за вычетом памяти, сопоставленной или выделенной xVelocity в подсистеме, выполняющейся в памяти аналитики (VertiPaq) сверх предела памяти подсистемы VertiPaq.|ServerResourceType|
|MemoryLimitHard|Память: твердый лимит памяти|Байты|Средняя|Твердый лимит памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitHigh|Память: верхний предел памяти|Байты|Среднее|Верхняя граница объема памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitLow|Память: нижний предел памяти|Байты|Средняя|Нижняя граница объема памяти, из файла конфигурации.|ServerResourceType|
|MemoryLimitVertiPaq|Память: ограничение памяти VertiPaq|Байты|Средняя|Ограничение памяти, из файла конфигурации.|ServerResourceType|
|Quota|Память: квота|Байты|Среднее|Текущая квота памяти, в байтах. Квота памяти также называется выделением или резервированием памяти.|ServerResourceType|
|QuotaBlocked|Память: заблокировано квот|Count|Средняя|Текущее число запросов на квоту, заблокированных до высвобождения других квот памяти.|ServerResourceType|
|VertiPaqNonpaged|Память: размер нестраничных данных VertiPaq|Байты|Средняя|Размер памяти (в байтах), заблокированной в рабочем множестве для использования в подсистеме памяти.|ServerResourceType|
|VertiPaqPaged|Память: размер страничных данных VertiPaq|Байты|Средняя|Размер памяти (в байтах), занятой страничными данными в памяти.|ServerResourceType|
|RowsReadPerSec|Обработка: считано строк в секунду|Число/с|Среднее|Интенсивность считывания строк из всех реляционных баз данных.|ServerResourceType|
|RowsConvertedPerSec|Обработка: преобразовано строк в секунду|Число/с|Средняя|Интенсивность преобразованных в ходе обработки строк.|ServerResourceType|
|RowsWrittenPerSec|Обработка: записано строк в секунду|Число/с|Средняя|Интенсивность записанных в ходе обработки строк.|ServerResourceType|
|CommandPoolBusyThreads|Потоки: занятые потоки в пуле команд|Count|Среднее|Число занятых потоков в пуле потоков команд.|ServerResourceType|
|CommandPoolIdleThreads|Потоки: бездействующие потоки в пуле команд|Count|Средняя|Число бездействующих потоков в пуле потоков команд.|ServerResourceType|
|LongParsingBusyThreads|Потоки: занятые потоки продолжительного анализа|Count|Средняя|Число занятых потоков в пуле потоков продолжительного анализа.|ServerResourceType|
|LongParsingIdleThreads|Потоки: бездействующие потоки продолжительного анализа|Count|Средняя|Число бездействующих потоков в пуле потоков продолжительного анализа.|ServerResourceType|
|LongParsingJobQueueLength|Потоки: длина очереди заданий продолжительного анализа|Count|Среднее|Число заданий в очереди пула потоков продолжительного анализа.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Потоки: занятые потоки ввода-вывода в пуле обработки заданий|Count|Средняя|Количество потоков, выполняющих задания ввода-вывода, в пуле потоков обработки.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Потоки: занятые потоки в пуле обработки, не связанные с вводом-выводом|Count|Среднее|Количество потоков, выполняющих задания, не связанные с вводом-выводом в пуле потоков обработки.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Потоки: длина очереди заданий ввода-вывода пула обработки|Count|Среднее|Количество заданий ввода-вывода в очереди пула потоков обработки.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Потоки: бездействующие потоки ввода-вывода в пуле обработки заданий|Count|Средняя|Количество бездействующих потоков, выполняющих задания ввода-вывода, в пуле потоков обработки.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Потоки: бездействующие потоки в пуле обработки, не связанные с вводом-выводом|Count|Среднее|Количество бездействующих потоков в пуле потоков обработки, выделенных для заданий, не связанных с вводом-выводом.|ServerResourceType|
|QueryPoolIdleThreads|Потоки: бездействующие потоки в пуле запросов|Count|Среднее|Количество бездействующих потоков, выполняющих задания ввода-вывода, в пуле потоков обработки.|ServerResourceType|
|QueryPoolJobQueueLength|Потоки: длина очереди заданий пула запросов|Count|Средняя|Число заданий в очереди пула потоков запросов.|ServerResourceType|
|ShortParsingBusyThreads|Потоки: занятые потоки быстрого анализа|Count|Средняя|Число занятых потоков в пуле потоков быстрого анализа.|ServerResourceType|
|ShortParsingIdleThreads|Потоки: бездействующие потоки быстрого анализа|Count|Среднее|Число бездействующих потоков в пуле потоков быстрого анализа.|ServerResourceType|
|ShortParsingJobQueueLength|Потоки: длина очереди заданий быстрого анализа|Count|Средняя|Число заданий в очереди пула потоков быстрого анализа.|ServerResourceType|
|memory_thrashing_metric|Пробуксовка памяти|Процент|Среднее|Среднее значение пробуксовки памяти.|ServerResourceType|
|mashup_engine_qpu_metric|QPU подсистемы M|Count|Среднее|Использование QPU процессами подсистемы гибридных веб-приложений|ServerResourceType|
|mashup_engine_memory_metric|Память подсистемы M|Байты|Средняя|Использование памяти процессами подсистемы гибридных веб-приложений|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|TotalRequests|Total Gateway Requests|Count|Всего|Число запросов к шлюзу|Location, Hostname|
|SuccessfulRequests|Successful Gateway Requests|Count|Всего|Число успешных запросов к шлюзу|Location, Hostname|
|UnauthorizedRequests|Неавторизованные запросы к шлюзу|Count|Всего|Число неавторизованных запросов к шлюзу|Location, Hostname|
|FailedRequests|Failed Gateway Requests|Count|Всего|Количество сбоев при обработке запросов к шлюзу.|Location, Hostname|
|OtherRequests|Другие запросы к шлюзу|Count|Всего|Количество других запросов к шлюзу|Location, Hostname|
|Duration|Общая длительность запросов шлюза|Миллисекунды|Средняя|Общая длительность запросов шлюза в миллисекундах|Location, Hostname|
|Capacity|Емкость (предварительная версия)|Процент|Максимальная|Метрика использования для службы ApiManagement|Расположение|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|TotalJob|Всего заданий|Count|Всего|Общее количество заданий|RunbookName, Status|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|CoreCount|Dedicated Core Count|Count|Всего|Общее число выделенных ядер в учетной записи пакетной службы.|Нет измерений|
|TotalNodeCount|Dedicated Node Count|Count|Всего|Общее число выделенных узлов в учетной записи пакетной службы.|Нет измерений|
|LowPriorityCoreCount|LowPriority Core Count|Count|Всего|Общее число низкоприоритетных ядер в учетной записи пакетной службы.|Нет измерений|
|TotalLowPriorityNodeCount|Low-Priority Node Count|Count|Всего|Общее число низкоприоритетных узлов в учетной записи пакетной службы.|Нет измерений|
|CreatingNodeCount|Creating Node Count|Count|Всего|Количество создаваемых узлов.|Нет измерений|
|StartingNodeCount|Starting Node Count|Count|Всего|Число узлов, которые запускаются.|Нет измерений|
|WaitingForStartTaskNodeCount|Waiting For Start Task Node Count|Count|Всего|Число узлов, ожидающих завершения задачи запуска.|Нет измерений|
|StartTaskFailedNodeCount|Start Task Failed Node Count|Count|Всего|Число узлов, на которых произошел сбой задачи запуска.|Нет измерений|
|IdleNodeCount|Idle Node Count|Count|Всего|Количество узлов в неактивном состоянии.|Нет измерений|
|OfflineNodeCount|Offline Node Count|Count|Всего|Количество узлов не в сети.|Нет измерений|
|RebootingNodeCount|Rebooting Node Count|Count|Всего|Количество перезапускаемых узлов.|Нет измерений|
|ReimagingNodeCount|Reimaging Node Count|Count|Всего|Число узлов, для которых пересоздается образ.|Нет измерений|
|RunningNodeCount|Running Node Count|Count|Всего|Число работающих узлов.|Нет измерений|
|LeavingPoolNodeCount|Leaving Pool Node Count|Count|Всего|Количество узлов, покидающих пул.|Нет измерений|
|UnusableNodeCount|Unusable Node Count|Count|Всего|Число узлов, непригодных для использования.|Нет измерений|
|PreemptedNodeCount|Preempted Node Count|Count|Всего|Количество замещенных узлов.|Нет измерений|
|TaskStartEvent|Task Start Events|Count|Всего|Общее число запущенных задач.|Нет измерений|
|TaskCompleteEvent|Task Complete Events|Count|Всего|Общее число завершенных задач.|Нет измерений|
|TaskFailEvent|Task Fail Events|Count|Всего|Общее число задач, завершившихся сбоем.|Нет измерений|
|PoolCreateEvent|Pool Create Events|Count|Всего|Общее число созданных пулов.|Нет измерений|
|PoolResizeStartEvent|Pool Resize Start Events|Count|Всего|Общее число запущенных задач изменения размера пула.|Нет измерений|
|PoolResizeCompleteEvent|Pool Resize Complete Events|Count|Всего|Общее число завершенных задач изменения размера пула.|Нет измерений|
|PoolDeleteStartEvent|Pool Delete Start Events|Count|Всего|Общее число запущенных задач удаления пула.|Нет измерений|
|PoolDeleteCompleteEvent|Pool Delete Complete Events|Count|Всего|Общее число завершенных задач удаления пула.|Нет измерений|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|connectedclients|Подключенные клиенты|Count|Максимальная||Нет измерений|
|totalcommandsprocessed|Всего операций|Count|Всего||Нет измерений|
|cachehits|Попаданий в кэш|Count|Всего||Нет измерений|
|cachemisses|Промахов в кэше|Count|Всего||Нет измерений|
|getcommands|Операций считывания|Count|Всего||Нет измерений|
|setcommands|Операций записи|Count|Всего||Нет измерений|
|operationsPerSecond|Количество операций в секунду|Count|Всего||Нет измерений|
|evictedkeys|Исключенные ключи|Count|Всего||Нет измерений|
|totalkeys|Всего ключей|Count|Максимальная||Нет измерений|
|expiredkeys|Ключи с истекшим сроком действия|Count|Всего||Нет измерений|
|usedmemory|Используемая память|Байты|Максимальная||Нет измерений|
|usedmemoryRss|RSS используемой памяти|Байты|Максимальная||Нет измерений|
|serverLoad|Загрузка сервера|Процент|Максимальная||Нет измерений|
|cacheWrite|Запись в кэш|Байт/с|Максимальная||Нет измерений|
|cacheRead|Чтение из кэша|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime|ЦП|Процент|Максимальная||Нет измерений|
|connectedclients0|Connected Clients (Shard 0)|Count|Максимальная||Нет измерений|
|totalcommandsprocessed0|Total Operations (Shard 0)|Count|Всего||Нет измерений|
|cachehits0|Cache Hits (Shard 0)|Count|Всего||Нет измерений|
|cachemisses0|Cache Misses (Shard 0)|Count|Всего||Нет измерений|
|getcommands0|Gets (Shard 0)|Count|Всего||Нет измерений|
|setcommands0|Sets (Shard 0)|Count|Всего||Нет измерений|
|operationsPerSecond0|Количество операций в секунду (сегмент 0).|Count|Всего||Нет измерений|
|evictedkeys0|Evicted Keys (Shard 0)|Count|Всего||Нет измерений|
|totalkeys0|Total Keys (Shard 0)|Count|Максимальная||Нет измерений|
|expiredkeys0|Expired Keys (Shard 0)|Count|Всего||Нет измерений|
|usedmemory0|Used Memory (Shard 0)|Байты|Максимальная||Нет измерений|
|usedmemoryRss0|Used Memory RSS (Shard 0)|Байты|Максимальная||Нет измерений|
|serverLoad0|Server Load (Shard 0)|Процент|Максимальная||Нет измерений|
|cacheWrite0|Cache Write (Shard 0)|Байт/с|Максимальная||Нет измерений|
|cacheRead0|Cache Read (Shard 0)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime0|CPU (Shard 0)|Процент|Максимальная||Нет измерений|
|connectedclients1|Connected Clients (Shard 1)|Count|Максимальная||Нет измерений|
|totalcommandsprocessed1|Total Operations (Shard 1)|Count|Всего||Нет измерений|
|cachehits1|Cache Hits (Shard 1)|Count|Всего||Нет измерений|
|cachemisses1|Cache Misses (Shard 1)|Count|Всего||Нет измерений|
|getcommands1|Gets (Shard 1)|Count|Всего||Нет измерений|
|getcommands1|Sets (Shard 1)|Count|Всего||Нет измерений|
|operationsPerSecond1|Количество операций в секунду (сегмент 1).|Count|Всего||Нет измерений|
|evictedkeys1|Evicted Keys (Shard 1)|Count|Всего||Нет измерений|
|totalkeys1|Total Keys (Shard 1)|Count|Максимальная||Нет измерений|
|expiredkeys1|Expired Keys (Shard 1)|Count|Всего||Нет измерений|
|usedmemory1|Used Memory (Shard 1)|Байты|Максимальная||Нет измерений|
|usedmemoryRss1|Used Memory RSS (Shard 1)|Байты|Максимальная||Нет измерений|
|serverLoad1|Server Load (Shard 1)|Процент|Максимальная||Нет измерений|
|cacheWrite1|Cache Write (Shard 1)|Байт/с|Максимальная||Нет измерений|
|cacheRead1|Cache Read (Shard 1)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime1|CPU (Shard 1)|Процент|Максимальная||Нет измерений|
|connectedclients2|Connected Clients (Shard 2)|Count|Максимальная||Нет измерений|
|totalcommandsprocessed2|Total Operations (Shard 2)|Count|Всего||Нет измерений|
|cachehits2|Cache Hits (Shard 2)|Count|Всего||Нет измерений|
|cachemisses2|Cache Misses (Shard 2)|Count|Всего||Нет измерений|
|getcommands2|Gets (Shard 2)|Count|Всего||Нет измерений|
|getcommands2|Sets (Shard 2)|Count|Всего||Нет измерений|
|operationsPerSecond2|Количество операций в секунду (сегмент 2).|Count|Всего||Нет измерений|
|evictedkeys2|Evicted Keys (Shard 2)|Count|Всего||Нет измерений|
|totalkeys2|Total Keys (Shard 2)|Count|Максимальная||Нет измерений|
|expiredkeys2|Expired Keys (Shard 2)|Count|Всего||Нет измерений|
|usedmemory2|Used Memory (Shard 2)|Байты|Максимальная||Нет измерений|
|usedmemoryRss2|Used Memory RSS (Shard 2)|Байты|Максимальная||Нет измерений|
|serverLoad2|Server Load (Shard 2)|Процент|Максимальная||Нет измерений|
|cacheWrite2|Cache Write (Shard 2)|Байт/с|Максимальная||Нет измерений|
|cacheRead2|Cache Read (Shard 2)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime2|CPU (Shard 2)|Процент|Максимальная||Нет измерений|
|connectedclients3|Connected Clients (Shard 3)|Count|Максимальная||Нет измерений|
|totalcommandsprocessed3|Total Operations (Shard 3)|Count|Всего||Нет измерений|
|cachehits3|Cache Hits (Shard 3)|Count|Всего||Нет измерений|
|cachemisses3|Cache Misses (Shard 3)|Count|Всего||Нет измерений|
|getcommands3|Gets (Shard 3)|Count|Всего||Нет измерений|
|setcommands3|Sets (Shard 3)|Count|Всего||Нет измерений|
|operationsPerSecond3|Количество операций в секунду (сегмент 3).|Count|Всего||Нет измерений|
|evictedkeys3|Evicted Keys (Shard 3)|Count|Всего||Нет измерений|
|totalkeys3|Total Keys (Shard 3)|Count|Максимальная||Нет измерений|
|expiredkeys3|Expired Keys (Shard 3)|Count|Всего||Нет измерений|
|usedmemory3|Used Memory (Shard 3)|Байты|Максимальная||Нет измерений|
|usedmemoryRss3|Used Memory RSS (Shard 3)|Байты|Максимальная||Нет измерений|
|serverLoad3|Server Load (Shard 3)|Процент|Максимальная||Нет измерений|
|cacheWrite3|Cache Write (Shard 3)|Байт/с|Максимальная||Нет измерений|
|cacheRead3|Cache Read (Shard 3)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime3|CPU (Shard 3)|Процент|Максимальная||Нет измерений|
|connectedclients4|Connected Clients (Shard 4)|Count|Максимальная||Нет измерений|
|totalcommandsprocessed4|Total Operations (Shard 4)|Count|Всего||Нет измерений|
|cachehits4|Cache Hits (Shard 4)|Count|Всего||Нет измерений|
|cachemisses4|Cache Misses (Shard 4)|Count|Всего||Нет измерений|
|getcommands4|Gets (Shard 4)|Count|Всего||Нет измерений|
|setcommands4|Sets (Shard 4)|Count|Всего||Нет измерений|
|operationsPerSecond4|Количество операций в секунду (сегмент 4).|Count|Всего||Нет измерений|
|evictedkeys4|Evicted Keys (Shard 4)|Count|Всего||Нет измерений|
|totalkeys4|Total Keys (Shard 4)|Count|Максимальная||Нет измерений|
|expiredkeys4|Expired Keys (Shard 4)|Count|Всего||Нет измерений|
|usedmemory4|Used Memory (Shard 4)|Байты|Максимальная||Нет измерений|
|usedmemoryRss4|Used Memory RSS (Shard 4)|Байты|Максимальная||Нет измерений|
|serverLoad4|Server Load (Shard 4)|Процент|Максимальная||Нет измерений|
|cacheWrite4|Cache Write (Shard 4)|Байт/с|Максимальная||Нет измерений|
|cacheRead4|Cache Read (Shard 4)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime4|CPU (Shard 4)|Процент|Максимальная||Нет измерений|
|connectedclients5|Connected Clients (Shard 5)|Count|Максимальная||Нет измерений|
|totalcommandsprocessed5|Total Operations (Shard 5)|Count|Всего||Нет измерений|
|cachehits5|Cache Hits (Shard 5)|Count|Всего||Нет измерений|
|cachemisses5|Cache Misses (Shard 5)|Count|Всего||Нет измерений|
|getcommands5|Gets (Shard 5)|Count|Всего||Нет измерений|
|getcommands5|Sets (Shard 5)|Count|Всего||Нет измерений|
|operationsPerSecond5|Количество операций в секунду (сегмент 5).|Count|Всего||Нет измерений|
|evictedkeys5|Evicted Keys (Shard 5)|Count|Всего||Нет измерений|
|totalkeys5|Total Keys (Shard 5)|Count|Максимальная||Нет измерений|
|expiredkeys5|Expired Keys (Shard 5)|Count|Всего||Нет измерений|
|usedmemory5|Used Memory (Shard 5)|Байты|Максимальная||Нет измерений|
|usedmemoryRss5|Used Memory RSS (Shard 5)|Байты|Максимальная||Нет измерений|
|serverLoad5|Server Load (Shard 5)|Процент|Максимальная||Нет измерений|
|cacheWrite5|Cache Write (Shard 5)|Байт/с|Максимальная||Нет измерений|
|cacheRead5|Cache Read (Shard 5)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime5|CPU (Shard 5)|Процент|Максимальная||Нет измерений|
|connectedclients6|Connected Clients (Shard 6)|Count|Максимальная||Нет измерений|
|totalcommandsprocessed6|Total Operations (Shard 6)|Count|Всего||Нет измерений|
|cachehits6|Cache Hits (Shard 6)|Count|Всего||Нет измерений|
|cachemisses6|Cache Misses (Shard 6)|Count|Всего||Нет измерений|
|getcommands6|Gets (Shard 6)|Count|Всего||Нет измерений|
|setcommands6|Sets (Shard 6)|Count|Всего||Нет измерений|
|operationsPerSecond6|Количество операций в секунду (сегмент 6).|Count|Всего||Нет измерений|
|evictedkeys6|Evicted Keys (Shard 6)|Count|Всего||Нет измерений|
|totalkeys6|Total Keys (Shard 6)|Count|Максимальная||Нет измерений|
|expiredkeys6|Expired Keys (Shard 6)|Count|Всего||Нет измерений|
|usedmemory6|Used Memory (Shard 6)|Байты|Максимальная||Нет измерений|
|usedmemoryRss6|Used Memory RSS (Shard 6)|Байты|Максимальная||Нет измерений|
|serverLoad6|Server Load (Shard 6)|Процент|Максимальная||Нет измерений|
|cacheWrite6|Cache Write (Shard 6)|Байт/с|Максимальная||Нет измерений|
|cacheRead6|Cache Read (Shard 6)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime6|CPU (Shard 6)|Процент|Максимальная||Нет измерений|
|connectedclients7|Connected Clients (Shard 7)|Count|Максимальная||Нет измерений|
|totalcommandsprocessed7|Total Operations (Shard 7)|Count|Всего||Нет измерений|
|cachehits7|Cache Hits (Shard 7)|Count|Всего||Нет измерений|
|cachemisses7|Cache Misses (Shard 7)|Count|Всего||Нет измерений|
|getcommands7|Gets (Shard 7)|Count|Всего||Нет измерений|
|setcommands7|Sets (Shard 7)|Count|Всего||Нет измерений|
|operationsPerSecond7|Количество операций в секунду (сегмент 7).|Count|Всего||Нет измерений|
|evictedkeys7|Evicted Keys (Shard 7)|Count|Всего||Нет измерений|
|totalkeys7|Total Keys (Shard 7)|Count|Максимальная||Нет измерений|
|expiredkeys7|Expired Keys (Shard 7)|Count|Всего||Нет измерений|
|usedmemory7|Used Memory (Shard 7)|Байты|Максимальная||Нет измерений|
|usedmemoryRss7|Used Memory RSS (Shard 7)|Байты|Максимальная||Нет измерений|
|serverLoad7|Server Load (Shard 7)|Процент|Максимальная||Нет измерений|
|cacheWrite7|Cache Write (Shard 7)|Байт/с|Максимальная||Нет измерений|
|cacheRead7|Cache Read (Shard 7)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime7|CPU (Shard 7)|Процент|Максимальная||Нет измерений|
|connectedclients8|Connected Clients (Shard 8)|Count|Максимальная||Нет измерений|
|totalcommandsprocessed8|Total Operations (Shard 8)|Count|Всего||Нет измерений|
|cachehits8|Cache Hits (Shard 8)|Count|Всего||Нет измерений|
|cachemisses8|Cache Misses (Shard 8)|Count|Всего||Нет измерений|
|getcommands8|Gets (Shard 8)|Count|Всего||Нет измерений|
|setcommands8|Sets (Shard 8)|Count|Всего||Нет измерений|
|operationsPerSecond8|Количество операций в секунду (сегмент 8).|Count|Всего||Нет измерений|
|evictedkeys8|Evicted Keys (Shard 8)|Count|Всего||Нет измерений|
|totalkeys8|Total Keys (Shard 8)|Count|Максимальная||Нет измерений|
|expiredkeys8|Expired Keys (Shard 8)|Count|Всего||Нет измерений|
|usedmemory8|Used Memory (Shard 8)|Байты|Максимальная||Нет измерений|
|usedmemoryRss8|Used Memory RSS (Shard 8)|Байты|Максимальная||Нет измерений|
|serverLoad8|Server Load (Shard 8)|Процент|Максимальная||Нет измерений|
|cacheWrite8|Cache Write (Shard 8)|Байт/с|Максимальная||Нет измерений|
|cacheRead8|Cache Read (Shard 8)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime8|CPU (Shard 8)|Процент|Максимальная||Нет измерений|
|connectedclients9|Connected Clients (Shard 9)|Count|Максимальная||Нет измерений|
|totalcommandsprocessed9|Total Operations (Shard 9)|Count|Всего||Нет измерений|
|cachehits9|Cache Hits (Shard 9)|Count|Всего||Нет измерений|
|cachemisses9|Cache Misses (Shard 9)|Count|Всего||Нет измерений|
|getcommands9|Gets (Shard 9)|Count|Всего||Нет измерений|
|setcommands9|Sets (Shard 9)|Count|Всего||Нет измерений|
|operationsPerSecond9|Количество операций в секунду (сегмент 9).|Count|Всего||Нет измерений|
|evictedkeys9|Evicted Keys (Shard 9)|Count|Всего||Нет измерений|
|totalkeys9|Total Keys (Shard 9)|Count|Максимальная||Нет измерений|
|expiredkeys9|Expired Keys (Shard 9)|Count|Всего||Нет измерений|
|usedmemory9|Used Memory (Shard 9)|Байты|Максимальная||Нет измерений|
|usedmemoryRss9|Used Memory RSS (Shard 9)|Байты|Максимальная||Нет измерений|
|serverLoad9|Server Load (Shard 9)|Процент|Максимальная||Нет измерений|
|cacheWrite9|Cache Write (Shard 9)|Байт/с|Максимальная||Нет измерений|
|cacheRead9|Cache Read (Shard 9)|Байт/с|Максимальная||Нет измерений|
|percentProcessorTime9|CPU (Shard 9)|Процент|Максимальная||Нет измерений|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classicСompute/virtualMachines

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Загрузка ЦП|Процент|Средняя|Процент выделенных вычислительных единиц, используемых в настоящее время виртуальными машинами.|Нет измерений|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Всего|Количество байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|Нет измерений|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Всего|Количество байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|Нет измерений|
|Disk Read Bytes/Sec|Скорость чтения с диска|Байт/с|Средняя|Среднее количество байтов, считанных с диска за период мониторинга.|Нет измерений|
|Disk Write Bytes/Sec|Запись на диск|Байт/с|Средняя|Среднее количество байтов, записанных на диск за период мониторинга.|Нет измерений|
|Disk Read Operations/Sec|Чтение с дисков (операций/с)|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|Нет измерений|
|Disk Write Operations/Sec|Запись на диски (операций/с)|Число/с|Средняя|Количество операций вода-вывода в секунду при записи на диск.|Нет измерений|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Загрузка ЦП|Процент|Средняя|Процент выделенных вычислительных единиц, используемых в настоящее время виртуальными машинами.|Нет измерений|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Всего|Количество байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|Нет измерений|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Всего|Количество байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|Нет измерений|
|Disk Read Bytes/Sec|Скорость чтения с диска|Байт/с|Средняя|Среднее количество байтов, считанных с диска за период мониторинга.|Нет измерений|
|Disk Write Bytes/Sec|Запись на диск|Байт/с|Средняя|Среднее количество байтов, записанных на диск за период мониторинга.|Нет измерений|
|Disk Read Operations/Sec|Чтение с дисков (операций/с)|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|Нет измерений|
|Disk Write Operations/Sec|Запись на диски (операций/с)|Число/с|Средняя|Количество операций вода-вывода в секунду при записи на диск.|Нет измерений|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|TotalCalls|Total Calls|Count|Всего|Общее число вызовов.|Нет измерений|
|SuccessfulCalls|Successful Calls|Count|Всего|Число успешных вызовов.|Нет измерений|
|TotalErrors|Total Errors|Count|Всего|Общее число вызовов, вернувших сообщение об ошибке (код ответа HTTP 4xx или 5xx).|Нет измерений|
|BlockedCalls|Blocked Calls|Count|Всего|Число вызовов, превысивших ограничение скорости или квоты.|Нет измерений|
|ServerErrors|Server Errors|Count|Всего|Число вызовов, приведших к внутренней ошибке сервера (код ответа HTTP 5xx).|Нет измерений|
|ClientErrors|Client Errors|Count|Всего|Число вызовов, приведших к ошибке на стороне клиента (код ответа HTTP 4xx).|Нет измерений|
|DataIn|Входящие данные:|Байты|Всего|Размер входящих данных в байтах.|Нет измерений|
|DataOut|Выходные данные|Байты|Всего|Размер исходящих данных в байтах.|Нет измерений|
|Latency|Latency|MilliSeconds|Средняя|Время задержки в миллисекундах.|Нет измерений|
|CharactersTranslated|Преобразованные символы|Count|Всего|Общее количество символов во входящем текстовом запросе.|Нет измерений|
|SpeechSessionDuration|Длительность речи|Секунды|Всего|Общая длительность речи в секундах.|Нет измерений|
|TotalTransactions|Всего транзакций|Count|Всего|Общее количество транзакций|Нет измерений|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Загрузка ЦП|Процент|Среднее|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|Нет измерений|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Всего|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|Нет измерений|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Всего|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|Нет измерений|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Всего|Общее число байтов, считанных с диска за период мониторинга.|Нет измерений|
|Disk Write Bytes|Скорость записи на диск|Байты|Всего|Общее число байтов, записанных на диск за период мониторинга.|Нет измерений|
|Disk Read Operations/Sec|Чтение с дисков (операций/с)|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|Нет измерений|
|Disk Write Operations/Sec|Запись на диски (операций/с)|Число/с|Среднее|Количество операций вода-вывода в секунду при записи на диск.|Нет измерений|
|Оставшиеся кредиты ЦП|Оставшиеся кредиты ЦП|Count|Средняя|Общее число доступных для увеличения кредитов|Нет измерений|
|Использованные кредиты ЦП|Использованные кредиты ЦП|Count|Средняя|Общее количество кредитов, использованных виртуальной машиной|Нет измерений|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets;

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Загрузка ЦП|Процент|Средняя|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|Нет измерений|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Всего|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|Нет измерений|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Всего|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|Нет измерений|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Всего|Общее число байтов, считанных с диска за период мониторинга.|Нет измерений|
|Disk Write Bytes|Скорость записи на диск|Байты|Всего|Общее число байтов, записанных на диск за период мониторинга.|Нет измерений|
|Disk Read Operations/Sec|Чтение с дисков (операций/с)|Число/с|Средняя|Количество операций вода-вывода в секунду при чтении с диска.|Нет измерений|
|Disk Write Operations/Sec|Запись на диски (операций/с)|Число/с|Средняя|Количество операций вода-вывода в секунду при записи на диск.|Нет измерений|
|Оставшиеся кредиты ЦП|Оставшиеся кредиты ЦП|Count|Среднее|Общее число доступных для увеличения кредитов|Нет измерений|
|Использованные кредиты ЦП|Использованные кредиты ЦП|Count|Среднее|Общее количество кредитов, использованных виртуальной машиной|Нет измерений|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|Percentage CPU|Загрузка ЦП|Процент|Средняя|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|Нет измерений|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Всего|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|Нет измерений|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Всего|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|Нет измерений|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Всего|Общее число байтов, считанных с диска за период мониторинга.|Нет измерений|
|Disk Write Bytes|Скорость записи на диск|Байты|Всего|Общее число байтов, записанных на диск за период мониторинга.|Нет измерений|
|Disk Read Operations/Sec|Чтение с дисков (операций/с)|Число/с|Среднее|Количество операций вода-вывода в секунду при чтении с диска.|Нет измерений|
|Disk Write Operations/Sec|Запись на диски (операций/с)|Число/с|Средняя|Количество операций вода-вывода в секунду при записи на диск.|Нет измерений|
|Оставшиеся кредиты ЦП|Оставшиеся кредиты ЦП|Count|Средняя|Общее число доступных для увеличения кредитов|Нет измерений|
|Использованные кредиты ЦП|Использованные кредиты ЦП|Count|Средняя|Общее количество кредитов, использованных виртуальной машиной|Нет измерений|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|CpuUsage|Загрузка ЦП|Count|Средняя|Использование ресурсов ЦП по всем ядрам, в миллиардах.|containerName|
|MemoryUsage|Использование памяти|Байты|Средняя|Общее использование памяти в байтах.|containerName|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API Calls|Count|Всего||Нет измерений|
|DCIMappingImportOperationSuccessfulLines|Mapping Import Operation Successful Lines|Count|Всего||Нет измерений|
|DCIMappingImportOperationFailedLines|Mapping Import Operation Failed Lines|Count|Всего||Нет измерений|
|DCIMappingImportOperationTotalLines|Mapping Import Operation Total Lines|Count|Всего||Нет измерений|
|DCIMappingImportOperationRuntimeInSeconds|Mapping Import Operation Runtime In Seconds|Секунды|Всего||Нет измерений|
|DCIOutboundProfileExportSucceeded|Outbound Profile Export Succeeded|Count|Всего||Нет измерений|
|DCIOutboundProfileExportFailed|Outbound Profile Export Failed|Count|Всего||Нет измерений|
|DCIOutboundProfileExportDuration|Outbound Profile Export Duration|Секунды|Всего||Нет измерений|
|DCIOutboundKpiExportSucceeded|Outbound Kpi Export Succeeded|Count|Всего||Нет измерений|
|DCIOutboundKpiExportFailed|Outbound Kpi Export Failed|Count|Всего||Нет измерений|
|DCIOutboundKpiExportDuration|Outbound Kpi Export Duration|Секунды|Всего||Нет измерений|
|DCIOutboundKpiExportStarted|Outbound Kpi Export Started|Секунды|Всего||Нет измерений|
|DCIOutboundKpiRecordCount|Outbound Kpi Record Count|Секунды|Всего||Нет измерений|
|DCIOutboundProfileExportCount|Outbound Profile Export Count|Секунды|Всего||Нет измерений|
|DCIOutboundInitialProfileExportFailed|Outbound Initial Profile Export Failed|Секунды|Всего||Нет измерений|
|DCIOutboundInitialProfileExportSucceeded|Outbound Initial Profile Export Succeeded|Секунды|Всего||Нет измерений|
|DCIOutboundInitialKpiExportFailed|Outbound Initial Kpi Export Failed|Секунды|Всего||Нет измерений|
|DCIOutboundInitialKpiExportSucceeded|Outbound Initial Kpi Export Succeeded|Секунды|Всего||Нет измерений|
|DCIOutboundInitialProfileExportDurationInSeconds|Outbound Initial Profile Export Duration In Seconds|Секунды|Всего||Нет измерений|
|AdlaJobForStandardKpiFailed|Adla Job For Standard Kpi Failed In Seconds|Секунды|Всего||Нет измерений|
|AdlaJobForStandardKpiTimeOut|Adla Job For Standard Kpi TimeOut In Seconds|Секунды|Всего||Нет измерений|
|AdlaJobForStandardKpiCompleted|Adla Job For Standard Kpi Completed In Seconds|Секунды|Всего||Нет измерений|
|ImportASAValuesFailed|Import ASA Values Failed Count|Count|Всего||Нет измерений|
|ImportASAValuesSucceeded|Import ASA Values Succeeded Count|Count|Всего||Нет измерений|
|DCIProfilesCount|Количество экземпляров профиля|Count|Последний||Нет измерений|
|DCIInteractionsPerMonthCount|Количество взаимодействий в месяц|Count|Последний||Нет измерений|
|DCIKpisCount|Количество ключевых показателей эффективности|Count|Последний||Нет измерений|
|DCISegmentsCount|Количество сегментов|Count|Последний||Нет измерений|
|DCIPredictiveMatchPoliciesCount|Количество операций прогнозного сопоставления|Count|Последний||Нет измерений|
|DCIPredictionsCount|Количество прогнозов|Count|Последний||Нет измерений|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|FailedRuns и|циклы выполнения со сбоем;|Count|Всего||pipelineName, activityName, windowEnd, windowStart |
|SuccessfulRuns.|успешные циклы выполнения.|Count|Всего||pipelineName, activityName, windowEnd, windowStart |

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories;

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|PipelineFailedRuns|Метрики неудачных выполнений конвейеров.|Count|Всего||FailureType, Name|
|PipelineSucceededRuns|Метрики успешных выполнений конвейеров.|Count|Всего||FailureType, Name|
|ActivityFailedRuns|Метрики неудачных выполнений действий.|Count|Всего||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Метрики успешных выполнений действий.|Count|Всего||ActivityType, PipelineName, FailureType, Name|
|TriggerFailedRuns|Метрики неудачных запусков триггеров.|Count|Всего||Name, FailureType|
|TriggerSucceededRuns|Метрики успешных запусков триггеров.|Count|Всего||Name, FailureType|
|IntegrationRuntimeCpuPercentage|Использование ЦП средой выполнения интеграции|Процент|Средняя||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Доступная память для среды выполнения интеграции|Байты|Средняя||IntegrationRuntimeName, NodeName|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|JobEndedSuccess|Successful Jobs|Count|Всего|Количество успешно выполненных заданий.|Нет измерений|
|JobEndedFailure|Failed Jobs|Count|Всего|Количество невыполненных заданий.|Нет измерений|
|JobEndedCancelled|Cancelled Jobs|Count|Всего|Количество отмененных заданий.|Нет измерений|
|JobAUEndedSuccess|Successful AU Time|Секунды|Всего|Общее время AU успешно выполненных заданий.|Нет измерений|
|JobAUEndedFailure|Failed AU Time|Секунды|Всего|Общее время AU невыполненных заданий.|Нет измерений|
|JobAUEndedCancelled|Cancelled AU Time|Секунды|Всего|Общее время AU отмененных заданий.|Нет измерений|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|TotalStorage|Общий объем хранилища|Байты|Максимальная|Общий объем данных, хранимых в учетной записи.|Нет измерений|
|DataWritten|Записанные данные|Байты|Всего|Общий объем данных, записанных в учетной записи.|Нет измерений|
|DataRead|Данных прочитано|Байты|Всего|Общий объем данных, прочитанных в учетной записи.|Нет измерений|
|WriteRequests|Запросы на запись|Count|Всего|Количество запросов на запись данных в учетную запись.|Нет измерений|
|ReadRequests|Запросы на чтение|Count|Всего|Количество запросов на чтение данных для учетной записи.|Нет измерений|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Средняя|Нагрузка ЦП|Нет измерений|
|compute_limit|Ограничение на единицы вычислений|Count|Среднее|Ограничение на единицы вычислений|Нет измерений|
|compute_consumption_percent|Процент использования единиц вычислений|Процент|Средняя|Процент использования единиц вычислений|Нет измерений|
|memory_percent|Процент памяти|Процент|Средняя|Процент памяти|Нет измерений|
|io_consumption_percent|Процент ввода-вывода данных|Процент|Среднее|Процент ввода-вывода данных|Нет измерений|
|storage_percent|Storage percentage|Процент|Средняя|Storage percentage|Нет измерений|
|storage_used|Storage used|Байты|Средняя|Storage used|Нет измерений|
|storage_limit|Storage limit|Байты|Среднее|Storage limit|Нет измерений|
|active_connections|Общее количество активных подключений|Count|Средняя|Общее количество активных подключений|Нет измерений|
|connections_failed|Общее количество неудачных подключений|Count|Среднее|Общее количество неудачных подключений|Нет измерений|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Средняя|Нагрузка ЦП|Нет измерений|
|compute_limit|Ограничение на единицы вычислений|Count|Средняя|Ограничение на единицы вычислений|Нет измерений|
|compute_consumption_percent|Процент использования единиц вычислений|Процент|Средняя|Процент использования единиц вычислений|Нет измерений|
|memory_percent|Процент памяти|Процент|Среднее|Процент памяти|Нет измерений|
|io_consumption_percent|Процент ввода-вывода данных|Процент|Средняя|Процент ввода-вывода данных|Нет измерений|
|storage_percent|Storage percentage|Процент|Средняя|Storage percentage|Нет измерений|
|storage_used|Storage used|Байты|Средняя|Storage used|Нет измерений|
|storage_limit|Storage limit|Байты|Средняя|Storage limit|Нет измерений|
|active_connections|Общее количество активных подключений|Count|Среднее|Общее количество активных подключений|Нет измерений|
|connections_failed|Общее количество неудачных подключений|Count|Средняя|Общее количество неудачных подключений|Нет измерений|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Число предпринятых попыток отправки сообщений телеметрии.|Count|Всего|Число предпринятых попыток отправки в Центр Интернета вещей сообщений телеметрии из устройства в облако.|Нет измерений|
|d2c.telemetry.ingress.success|Число отправленных сообщений телеметрии.|Count|Всего|Число успешно отправленных в Центр Интернета вещей сообщений телеметрии из устройства в облако.|Нет измерений|
|c2d.commands.egress.complete.success|Завершенные команды.|Count|Всего|Число успешно завершенных устройством команд, отправленных из облака.|Нет измерений|
|c2d.commands.egress.abandon.success|Прерванные команды.|Count|Всего|Число прерванных устройством команд, отправленных из облака.|Нет измерений|
|c2d.commands.egress.reject.success|Отклоненные команды.|Count|Всего|Число отмененных устройством команд, отправленных из облака.|Нет измерений|
|devices.totalDevices|Общее число устройств.|Count|Всего|Число устройств, зарегистрированных в Центре Интернета вещей.|Нет измерений|
|devices.connectedDevices.allProtocol|Подключенные устройства|Count|Всего|Число устройств, подключенных к Центру Интернета вещей.|Нет измерений|
|d2c.telemetry.egress.success|Telemetry messages delivered|Count|Всего|Общее число сообщений, успешно записанных в конечные точки.|Нет измерений|
|d2c.telemetry.egress.dropped|Удаленные сообщения.|Count|Всего|Число сообщений, отброшенных из-за неработоспособности конечной точки доставки|Нет измерений|
|d2c.telemetry.egress.orphaned|Потерянные сообщения.|Count|Всего|Число сообщений, не соответствующих какому-либо маршруту, включая резервный.|Нет измерений|
|d2c.telemetry.egress.invalid|Недопустимые сообщения.|Count|Всего|Число сообщений, которые не были доставлены из-за несовместимости с конечной точкой.|Нет измерений|
|d2c.telemetry.egress.fallback|Сообщения, соответствующие условиям для резервной конечной точки.|Count|Всего|Число сообщений, записанных в резервную конечную точку.|Нет измерений|
|d2c.endpoints.egress.eventHubs|Сообщения, доставленные в конечные точки концентратора событий.|Count|Всего|Число успешных записей сообщений в конечные точки концентратора событий.|Нет измерений|
|d2c.endpoints.latency.eventHubs|Message latency for Event Hub endpoints|Миллисекунды|Средняя|Средняя задержка в миллисекундах между поступлением сообщения в Центр Интернета вещей и его поступлением на конечную точку концентратора событий.|Нет измерений|
|d2c.endpoints.egress.serviceBusQueues|Сообщения, доставленные в конечные точки очереди служебной шины.|Count|Всего|Число успешных записей сообщений в конечные точки очереди служебной шины.|Нет измерений|
|d2c.endpoints.latency.serviceBusQueues|Message latency for Service Bus Queue endpoints|Миллисекунды|Средняя|Средняя задержка в миллисекундах между поступлением сообщения в Центр Интернета вещей и его поступлением на конечную точку очереди служебной шины.|Нет измерений|
|d2c.endpoints.egress.serviceBusTopics|Сообщения, доставленные в конечные точки раздела служебной шины.|Count|Всего|Число успешных записей сообщений в конечные точки раздела служебной шины.|Нет измерений|
|d2c.endpoints.latency.serviceBusTopics|Message latency for Service Bus Topic endpoints|Миллисекунды|Средняя|Средняя задержка в миллисекундах между поступлением сообщения в Центр Интернета вещей и его поступлением на конечную точку раздела служебной шины.|Нет измерений|
|d2c.endpoints.egress.builtIn.events|Сообщения, доставленные во встроенную конечную точку (сообщения и события).|Count|Всего|Число успешных записей сообщений во встроенную конечную точку (сообщения и события).|Нет измерений|
|d2c.endpoints.latency.builtIn.events|Задержка сообщения для встроенной конечной точки (сообщения и события).|Миллисекунды|Среднее|Средняя задержка в миллисекундах между поступлением сообщения в Центр Интернета вещей и его поступлением на встроенную конечную точку (сообщения и события). |Нет измерений|
|d2c.Endpoints.egress.Storage|Сообщения, доставленные в конечные точки хранилища|Count|Всего|Общее число сообщений, успешно записанных в конечные точки хранилища|Нет измерений|
|d2c.Endpoints.latency.Storage|Задержка сообщений для конечных точек хранилища|Миллисекунды|Среднее|Средняя задержка между поступлением сообщения в Центр Интернета вещей и в конечную точку хранилища в миллисекундах|Нет измерений|
|d2c.endpoints.egress.storage.bytes|Данные, записанные в хранилище|Байты|Всего|Объем данных, записанных в конечные точки хранилища (в байтах)|Нет измерений|
|d2c.Endpoints.egress.Storage.BLOBs|Большие двоичные объекты, записанные в хранилище|Count|Всего|Количество больших двоичных объектов, записанных в конечные точки хранилища|Нет измерений|
|d2c.twin.read.success|Успешные операции чтения с двойников, инициированные устройством.|Count|Всего|Число всех успешных операций чтения с двойников, инициированных устройством.|Нет измерений|
|d2c.twin.read.failure|Неудачные операции чтения с двойников, инициированные устройством.|Count|Всего|Число всех неудачных операций чтения с двойников, инициированных устройством.|Нет измерений|
|d2c.twin.read.size|Размер ответа операций чтения с двойников, инициированных устройством.|Байты|Среднее|Среднее, минимальное и максимальное значение всех успешных операций чтения, инициированных устройством.|Нет измерений|
|d2c.twin.update.success|Успешные обновления двойников, инициированные устройством.|Count|Всего|Число всех успешных обновлений двойников, инициированных устройством.|Нет измерений|
|d2c.twin.update.failure|Неудачные обновления двойников, инициированные устройством.|Count|Всего|Число всех неудачных обновлений двойников, инициированных устройством.|Нет измерений|
|d2c.twin.update.size|Размер обновлений двойников, инициированных устройством.|Байты|Средняя|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных устройством.|Нет измерений|
|c2d.methods.success|Успешные вызовы прямых методов.|Count|Всего|Число всех успешных вызовов прямых методов.|Нет измерений|
|c2d.methods.failure|Неудачные вызовы прямых методов.|Count|Всего|Число всех неудачных вызовов прямых методов.|Нет измерений|
|c2d.methods.requestSize|Размер запроса вызовов прямых методов.|Байты|Среднее|Среднее, минимальное и максимальное значение всех успешных запросов прямых методов.|Нет измерений|
|c2d.methods.responseSize|Размер ответа вызовов прямых методов.|Байты|Средняя|Среднее, минимальное и максимальное значения всех успешных ответов прямых методов.|Нет измерений|
|c2d.twin.read.success|Успешные операции чтения с двойников, инициированные из серверной части.|Count|Всего|Число всех успешных операций чтения с двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.read.failure|Неудачные операции чтения с двойников, инициированные из серверной части.|Count|Всего|Число всех неудачных операций чтения с двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.read.size|Размер ответа операций чтения с двойников, инициированных из серверной части.|Байты|Средняя|Среднее, минимальное и максимальное значения всех успешных операций чтения с двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.update.success|Успешные обновления двойников, инициированные из серверной части.|Count|Всего|Число всех успешных обновлений двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.update.failure|Неудачные обновления двойников, инициированные из серверной части.|Count|Всего|Число всех неудачных обновлений двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.update.size|Размер обновлений двойников, инициированных из серверной части.|Байты|Средняя|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных из серверной части.|Нет измерений|
|twinQueries.success|Успешные запросы двойников.|Count|Всего|Число всех успешных запросов двойников.|Нет измерений|
|twinQueries.failure|Неудачные запросы двойников.|Count|Всего|Количество всех неудачных запросов двойников.|Нет измерений|
|twinQueries.resultSize|Размер результатов запросов двойников.|Байты|Средняя|Среднее, минимальное и максимальное значения размера результатов всех успешных запросов двойников.|Нет измерений|
|jobs.createTwinUpdateJob.success|Успешные операции создания заданий обновления двойников.|Count|Всего|Количество всех успешных созданий заданий обновления двойников.|Нет измерений|
|jobs.createTwinUpdateJob.failure|Неудачные операции создания заданий обновления двойников.|Count|Всего|Количество всех неудачных операций создания заданий обновления двойников.|Нет измерений|
|jobs.createDirectMethodJob.success|Успешные операции создания заданий вызова методов.|Count|Всего|Количество всех успешных операций создания заданий вызова прямых методов.|Нет измерений|
|jobs.createDirectMethodJob.failure|Неудачные операции создания заданий вызова методов.|Count|Всего|Количество всех неудачных операций создания заданий вызова прямых методов.|Нет измерений|
|jobs.listJobs.success|Успешные вызовы получения списка заданий.|Count|Всего|Количество всех успешных вызовов для получения списка заданий.|Нет измерений|
|jobs.listJobs.failure|Неудачные вызовы получения списка заданий.|Count|Всего|Количество всех неудачных вызовов для получения списка заданий.|Нет измерений|
|jobs.cancelJob.success|Успешные отмены заданий.|Count|Всего|Количество всех успешных вызовов для отмены заданий.|Нет измерений|
|jobs.cancelJob.failure|Неудачные отмены заданий.|Count|Всего|Количество всех неудачных вызовов для отмены заданий.|Нет измерений|
|jobs.queryJobs.success|Успешные запросы заданий.|Count|Всего|Количество всех успешных вызовов для запроса заданий.|Нет измерений|
|jobs.queryJobs.failure|Неудачные запросы заданий.|Count|Всего|Количество всех неудачных вызовов для запроса заданий.|Нет измерений|
|jobs.completed|Завершенные задания|Count|Всего|Количество всех выполненных заданий.|Нет измерений|
|jobs.failed|Неудачные задания.|Count|Всего|Количество всех неудачных заданий.|Нет измерений|
|d2c.telemetry.ingress.sendThrottle|Количество ошибок регулирования|Count|Всего|Количество ошибок регулирования из-за регулирования пропускной способности устройства|Нет измерений|
|dailyMessageQuotaUsed|Общее количество используемых сообщений|Count|Средняя|Количество сообщений, использованных сегодня. Это совокупное значение, которое сбрасывается до нуля в 00:00 UTC каждый день.|Нет измерений|
|deviceDataUsage|Общее количество использований devicedata|Count|Всего|Байты, переданные на любые устройства, подключенные к Центру Интернета вещей, и с них|Нет измерений|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|RegistrationAttempts|Попытки регистрации|Count|Всего|Количество попыток регистрации устройств|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Назначенные устройства|Count|Всего|Количество устройств, назначенных Центру Интернета вещей|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Попытки аттестации|Count|Всего|Количество попыток аттестации устройств|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdeviceselasticpools"></a>Microsoft.Devices/ElasticPools

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|elasticPool.requestedUsageRate|Запрошенная частота использования|Процент|Среднее|Запрошенная частота использования|Нет измерений|

## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|tenantHub.requestedUsageRate|Запрошенная частота использования|Процент|Средняя|Запрошенная частота использования|Нет измерений|
|deviceDataUsage|Общее количество использований devicedata|Count|Всего|Байты, переданные на любые устройства, подключенные к Центру Интернета вещей, и с них|Нет измерений|
|d2c.telemetry.ingress.allProtocol|Число предпринятых попыток отправки сообщений телеметрии.|Count|Всего|Число предпринятых попыток отправки в Центр Интернета вещей сообщений телеметрии из устройства в облако.|Нет измерений|
|d2c.telemetry.ingress.success|Число отправленных сообщений телеметрии.|Count|Всего|Число успешно отправленных в Центр Интернета вещей сообщений телеметрии из устройства в облако.|Нет измерений|
|c2d.commands.egress.complete.success|Завершенные команды.|Count|Всего|Число успешно завершенных устройством команд, отправленных из облака.|Нет измерений|
|c2d.commands.egress.abandon.success|Прерванные команды.|Count|Всего|Число прерванных устройством команд, отправленных из облака.|Нет измерений|
|c2d.commands.egress.reject.success|Отклоненные команды.|Count|Всего|Число отмененных устройством команд, отправленных из облака.|Нет измерений|
|devices.totalDevices|Общее число устройств.|Count|Всего|Число устройств, зарегистрированных в Центре Интернета вещей.|Нет измерений|
|devices.connectedDevices.allProtocol|Подключенные устройства|Count|Всего|Число устройств, подключенных к Центру Интернета вещей.|Нет измерений|
|d2c.telemetry.egress.success|Telemetry messages delivered|Count|Всего|Общее число сообщений, успешно записанных в конечные точки.|Нет измерений|
|d2c.telemetry.egress.dropped|Удаленные сообщения.|Count|Всего|Число сообщений, отброшенных из-за неработоспособности конечной точки доставки|Нет измерений|
|d2c.telemetry.egress.orphaned|Потерянные сообщения.|Count|Всего|Число сообщений, не соответствующих какому-либо маршруту, включая резервный.|Нет измерений|
|d2c.telemetry.egress.invalid|Недопустимые сообщения.|Count|Всего|Число сообщений, которые не были доставлены из-за несовместимости с конечной точкой.|Нет измерений|
|d2c.telemetry.egress.fallback|Сообщения, соответствующие условиям для резервной конечной точки.|Count|Всего|Число сообщений, записанных в резервную конечную точку.|Нет измерений|
|d2c.endpoints.egress.eventHubs|Сообщения, доставленные в конечные точки концентратора событий.|Count|Всего|Число успешных записей сообщений в конечные точки концентратора событий.|Нет измерений|
|d2c.endpoints.latency.eventHubs|Message latency for Event Hub endpoints|Миллисекунды|Средняя|Средняя задержка в миллисекундах между поступлением сообщения в Центр Интернета вещей и его поступлением на конечную точку концентратора событий.|Нет измерений|
|d2c.endpoints.egress.serviceBusQueues|Сообщения, доставленные в конечные точки очереди служебной шины.|Count|Всего|Число успешных записей сообщений в конечные точки очереди служебной шины.|Нет измерений|
|d2c.endpoints.latency.serviceBusQueues|Message latency for Service Bus Queue endpoints|Миллисекунды|Средняя|Средняя задержка в миллисекундах между поступлением сообщения в Центр Интернета вещей и его поступлением на конечную точку очереди служебной шины.|Нет измерений|
|d2c.endpoints.egress.serviceBusTopics|Сообщения, доставленные в конечные точки раздела служебной шины.|Count|Всего|Число успешных записей сообщений в конечные точки раздела служебной шины.|Нет измерений|
|d2c.endpoints.latency.serviceBusTopics|Message latency for Service Bus Topic endpoints|Миллисекунды|Среднее|Средняя задержка в миллисекундах между поступлением сообщения в Центр Интернета вещей и его поступлением на конечную точку раздела служебной шины.|Нет измерений|
|d2c.endpoints.egress.builtIn.events|Сообщения, доставленные во встроенную конечную точку (сообщения и события).|Count|Всего|Число успешных записей сообщений во встроенную конечную точку (сообщения и события).|Нет измерений|
|d2c.endpoints.latency.builtIn.events|Задержка сообщения для встроенной конечной точки (сообщения и события).|Миллисекунды|Среднее|Средняя задержка в миллисекундах между поступлением сообщения в Центр Интернета вещей и его поступлением на встроенную конечную точку (сообщения и события). |Нет измерений|
|d2c.Endpoints.egress.Storage|Сообщения, доставленные в конечные точки хранилища|Count|Всего|Общее число сообщений, успешно записанных в конечные точки хранилища|Нет измерений|
|d2c.Endpoints.latency.Storage|Задержка сообщений для конечных точек хранилища|Миллисекунды|Средняя|Средняя задержка между поступлением сообщения в Центр Интернета вещей и в конечную точку хранилища в миллисекундах|Нет измерений|
|d2c.endpoints.egress.storage.bytes|Данные, записанные в хранилище|Байты|Всего|Объем данных, записанных в конечные точки хранилища (в байтах)|Нет измерений|
|d2c.Endpoints.egress.Storage.BLOBs|Большие двоичные объекты, записанные в хранилище|Count|Всего|Количество больших двоичных объектов, записанных в конечные точки хранилища|Нет измерений|
|d2c.twin.read.success|Успешные операции чтения с двойников, инициированные устройством.|Count|Всего|Число всех успешных операций чтения с двойников, инициированных устройством.|Нет измерений|
|d2c.twin.read.failure|Неудачные операции чтения с двойников, инициированные устройством.|Count|Всего|Число всех неудачных операций чтения с двойников, инициированных устройством.|Нет измерений|
|d2c.twin.read.size|Размер ответа операций чтения с двойников, инициированных устройством.|Байты|Среднее|Среднее, минимальное и максимальное значение всех успешных операций чтения, инициированных устройством.|Нет измерений|
|d2c.twin.update.success|Успешные обновления двойников, инициированные устройством.|Count|Всего|Число всех успешных обновлений двойников, инициированных устройством.|Нет измерений|
|d2c.twin.update.failure|Неудачные обновления двойников, инициированные устройством.|Count|Всего|Число всех неудачных обновлений двойников, инициированных устройством.|Нет измерений|
|d2c.twin.update.size|Размер обновлений двойников, инициированных устройством.|Байты|Средняя|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных устройством.|Нет измерений|
|c2d.methods.success|Успешные вызовы прямых методов.|Count|Всего|Число всех успешных вызовов прямых методов.|Нет измерений|
|c2d.methods.failure|Неудачные вызовы прямых методов.|Count|Всего|Число всех неудачных вызовов прямых методов.|Нет измерений|
|c2d.methods.requestSize|Размер запроса вызовов прямых методов.|Байты|Средняя|Среднее, минимальное и максимальное значение всех успешных запросов прямых методов.|Нет измерений|
|c2d.methods.responseSize|Размер ответа вызовов прямых методов.|Байты|Средняя|Среднее, минимальное и максимальное значения всех успешных ответов прямых методов.|Нет измерений|
|c2d.twin.read.success|Успешные операции чтения с двойников, инициированные из серверной части.|Count|Всего|Число всех успешных операций чтения с двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.read.failure|Неудачные операции чтения с двойников, инициированные из серверной части.|Count|Всего|Число всех неудачных операций чтения с двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.read.size|Размер ответа операций чтения с двойников, инициированных из серверной части.|Байты|Среднее|Среднее, минимальное и максимальное значения всех успешных операций чтения с двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.update.success|Успешные обновления двойников, инициированные из серверной части.|Count|Всего|Число всех успешных обновлений двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.update.failure|Неудачные обновления двойников, инициированные из серверной части.|Count|Всего|Число всех неудачных обновлений двойников, инициированных из серверной части.|Нет измерений|
|c2d.twin.update.size|Размер обновлений двойников, инициированных из серверной части.|Байты|Средняя|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных из серверной части.|Нет измерений|
|twinQueries.success|Успешные запросы двойников.|Count|Всего|Число всех успешных запросов двойников.|Нет измерений|
|twinQueries.failure|Неудачные запросы двойников.|Count|Всего|Количество всех неудачных запросов двойников.|Нет измерений|
|twinQueries.resultSize|Размер результатов запросов двойников.|Байты|Среднее|Среднее, минимальное и максимальное значения размера результатов всех успешных запросов двойников.|Нет измерений|
|jobs.createTwinUpdateJob.success|Успешные операции создания заданий обновления двойников.|Count|Всего|Количество всех успешных созданий заданий обновления двойников.|Нет измерений|
|jobs.createTwinUpdateJob.failure|Неудачные операции создания заданий обновления двойников.|Count|Всего|Количество всех неудачных операций создания заданий обновления двойников.|Нет измерений|
|jobs.createDirectMethodJob.success|Успешные операции создания заданий вызова методов.|Count|Всего|Количество всех успешных операций создания заданий вызова прямых методов.|Нет измерений|
|jobs.createDirectMethodJob.failure|Неудачные операции создания заданий вызова методов.|Count|Всего|Количество всех неудачных операций создания заданий вызова прямых методов.|Нет измерений|
|jobs.listJobs.success|Успешные вызовы получения списка заданий.|Count|Всего|Количество всех успешных вызовов для получения списка заданий.|Нет измерений|
|jobs.listJobs.failure|Неудачные вызовы получения списка заданий.|Count|Всего|Количество всех неудачных вызовов для получения списка заданий.|Нет измерений|
|jobs.cancelJob.success|Успешные отмены заданий.|Count|Всего|Количество всех успешных вызовов для отмены заданий.|Нет измерений|
|jobs.cancelJob.failure|Неудачные отмены заданий.|Count|Всего|Количество всех неудачных вызовов для отмены заданий.|Нет измерений|
|jobs.queryJobs.success|Успешные запросы заданий.|Count|Всего|Количество всех успешных вызовов для запроса заданий.|Нет измерений|
|jobs.queryJobs.failure|Неудачные запросы заданий.|Count|Всего|Количество всех неудачных вызовов для запроса заданий.|Нет измерений|
|jobs.completed|Завершенные задания|Count|Всего|Количество всех выполненных заданий.|Нет измерений|
|jobs.failed|Неудачные задания.|Count|Всего|Количество всех неудачных заданий.|Нет измерений|
|d2c.telemetry.ingress.sendThrottle|Количество ошибок регулирования|Count|Всего|Количество ошибок регулирования из-за регулирования пропускной способности устройства|Нет измерений|
|dailyMessageQuotaUsed|Общее количество используемых сообщений|Count|Средняя|Количество сообщений, использованных сегодня. Это совокупное значение, которое сбрасывается до нуля в 00:00 UTC каждый день.|Нет измерений|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|MetadataRequests|Запросы метаданных|Count|Count|Количество запросов метаданных. База данных Cosmos DB поддерживает сбор метаданных системы для каждой учетной записи, который позволяет бесплатно перечислять коллекции, базы данных и т. д. и их конфигурации.|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, StatusCode|
|MongoRequestCharge|Запросить оплату Mongo|Count|Всего|Использованные единицы запросов Mongo|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Запросы Mongo|Count|Count|Количество выполненных запросов Mongo|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|TotalRequestUnits|Общее количество единиц запросов|Count|Всего|Использованные единицы запросов|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, StatusCode|
|TotalRequests|Общее количество запросов|Count|Count|Количество выполненных запросов|GlobalDatabaseAccountName, DatabaseName, CollectionName, Region, StatusCode|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|SuccessfulRequests|Выполненные запросы (предварительная версия)|Count|Всего|Выполненные запросы для Microsoft.EventHub. (предварительная версия)|EntityName|
|ServerErrors|Ошибки сервера. (предварительная версия)|Count|Всего|Ошибки на сервере для Microsoft.EventHub. (предварительная версия)|EntityName|
|UserErrors|Ошибки пользователей. (предварительная версия)|Count|Всего|Ошибки пользователей для Microsoft.EventHub. (предварительная версия)|EntityName|
|QuotaExceededErrors|Ошибки превышения квоты. (предварительная версия)|Count|Всего|Ошибки превышения квоты для Microsoft.EventHub. (предварительная версия)|EntityName|
|ThrottledRequests|Регулируемые запросы. (предварительная версия)|Count|Всего|Регулируемые запросы для Microsoft.EventHub. (предварительная версия)|EntityName|
|IncomingRequests|Входящие запросы (предварительная версия)|Count|Всего|Входящие запросы для Microsoft.EventHub. (предварительная версия)|EntityName|
|IncomingMessages|Входящие сообщения (предварительная версия)|Count|Всего|Входящие сообщения для Microsoft.EventHub. (предварительная версия)|EntityName|
|OutgoingMessages|Исходящие сообщения (предварительная версия)|Count|Всего|Исходящие сообщения для Microsoft.EventHub. (предварительная версия)|EntityName|
|IncomingBytes|Входящие байты. (предварительная версия)|Байты|Всего|Входящие байты для Microsoft.EventHub. (предварительная версия)|EntityName|
|OutgoingBytes|Исходящие байты. (предварительная версия)|Байты|Всего|Исходящие байты для Microsoft.EventHub. (предварительная версия)|EntityName|
|ActiveConnections|Активные подключения (предварительная версия)|Count|Средняя|Число активных подключений для Microsoft.EventHub. (предварительная версия)|Нет измерений|
|ConnectionsOpened|Открытые подключения. (предварительная версия)|Count|Средняя|Открытые подключения для Microsoft.EventHub. (предварительная версия)|EntityName|
|ConnectionsOpened|Закрытые подключения. (предварительная версия)|Count|Средняя|Закрытые подключения для Microsoft.EventHub. (предварительная версия)|EntityName|
|CaptureBacklog|Невыполненная работа записи. (предварительная версия)|Count|Всего|Невыполненная работа записи для Microsoft.EventHub. (предварительная версия)|EntityName|
|CapturedMessages|Записанные сообщения. (предварительная версия)|Count|Всего|Записанные сообщения для Microsoft.EventHub. (предварительная версия)|EntityName|
|CapturedBytes|Записанные байты. (предварительная версия)|Байты|Всего|Записанные байты для Microsoft.EventHub. (предварительная версия)|EntityName|
|Размер|Размер (предварительная версия)|Байты|Средняя|Размер EventHub в байтах. (предварительная версия)|EntityName|
|INREQS|Входящих запросов|Count|Всего|Общее количество входящих запросов отправки для пространства имен|Нет измерений|
|SUCCREQ|Выполненные запросы|Count|Всего|Общее количество успешных запросов для пространства имен|Нет измерений|
|FAILREQ|Невыполненные запросы|Count|Всего|Общее количество неудачных запросов для пространства имен|Нет измерений|
|SVRBSY|Ошибки из-за занятости сервера|Count|Всего|Общее количество ошибок из-за занятости сервера для пространства имен|Нет измерений|
|INTERR|Внутренние ошибки сервера|Count|Всего|Общее количество внутренних ошибок сервера для пространства имен|Нет измерений|
|MISCERR|Другие ошибки|Count|Всего|Общее количество неудачных запросов для пространства имен|Нет измерений|
|INMSGS|Входящие сообщения (не рекомендуется)|Count|Всего|Общее количество входящих сообщений для пространства имен. Использовать эту метрику не рекомендуется. Вместо этого используйте метрику "Входящие сообщения".|Нет измерений|
|EHINMSGS|Входящие сообщения|Count|Всего|Общее количество входящих сообщений для пространства имен|Нет измерений|
|OUTMSGS|Исходящие сообщения (не рекомендуется)|Count|Всего|Общее количество исходящих сообщений для пространства имен. Использовать эту метрику не рекомендуется. Вместо этого используйте метрику "Исходящие сообщения"|Нет измерений|
|EHOUTMSGS|Исходящие сообщения|Count|Всего|Общее количество исходящих сообщений для пространства имен|Нет измерений|
|EHINMBS|Входящие байты (не рекомендуется)|Байты|Всего|Пропускная способность входящих сообщений концентратора событий для пространства имен. Использовать эту метрику не рекомендуется. Вместо этого используйте метрику "Входящие байты".|Нет измерений|
|EHINBYTES|Входящие байты|Байты|Всего|Пропускная способность входящих сообщений концентратора событий для пространства имен|Нет измерений|
|EHOUTMBS|Исходящие байты (не рекомендуется)|Байты|Всего|Пропускная способность исходящих сообщений концентратора событий для пространства имен. Использовать эту метрику не рекомендуется. Вместо этого используйте метрику "Исходящие байты".|Нет измерений|
|EHOUTBYTES|Исходящие байты|Байты|Всего|Пропускная способность исходящих сообщений концентратора событий для пространства имен|Нет измерений|
|EHABL|Archive backlog messages|Count|Всего|Архивные сообщения концентратора событий в списке невыполненных работ для пространства имен|Нет измерений|
|EHAMSGS|Archive messages|Count|Всего|Архивные сообщения концентратора событий в пространстве имен|Нет измерений|
|EHAMBS|Archive message throughput|Байты|Всего|Пропускная способность архивных сообщений концентратора событий в пространстве имен|Нет измерений|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|GatewayRequests|Запросы к шлюзу|Count|Всего|Число запросов к шлюзу|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Запросы к шлюзу по категориям|Count|Всего|Число запросов к шлюзу по категориям (1xx, 2xx, 3xx, 4xx или 5xx)|ClusterDnsName, HttpStatus|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|ObservedMetricValue|Наблюдаемое значение метрики|Count|Среднее|Значение, вычисляемое функцией автомасштабирования при выполнении|MetricTriggerSource|
|MetricThreshold|Пороговое значение метрики|Count|Среднее|Настроенное пороговое значение автомасштабирования при выполнении автомасштабирования.|MetricTriggerRule|
|ObservedCapacity|Наблюдаемая емкость|Count|Средняя|Емкость, передаваемая для автомасштабирования при выполнении.|Нет измерений|
|ScaleActionsInitiated|Инициированные действия масштабирования|Count|Всего|Направление операции масштабирования.|ScaleDirection|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|ServiceApiHit|Всего попаданий в API службы|Count|Count|Общее число попаданий в API службы|ActivityType, ActivityName|
|ServiceApiLatency|Общая задержка API службы|Миллисекунды|Средняя|Общая задержка запросов к API службы|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Всего результатов для API службы|Count|Count|Общее число результатов для API службы|ActivityType, ActivityName, StatusCode|

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|Latency|Latency|Миллисекунды|Средняя|Длительность вызовов API|OperationName, OperationResult|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Count|Всего|Число запущенных выполнений рабочего процесса.|Нет измерений|
|RunsCompleted|Runs Completed|Count|Всего|Число завершенных выполнений рабочего процесса.|Нет измерений|
|RunsSucceeded|Runs Succeeded|Count|Всего|Число успешно завершенных выполнений рабочего процесса.|Нет измерений|
|RunsFailed|Runs Failed|Count|Всего|Число выполнений рабочего процесса, завершившихся сбоем.|Нет измерений|
|RunsCancelled|Runs Cancelled|Count|Всего|Число отмененных выполнений рабочего процесса.|Нет измерений|
|RunLatency|Run Latency|Секунды|Средняя|Задержка завершенных выполнений рабочего процесса.|Нет измерений|
|RunSuccessLatency|Run Success Latency|Секунды|Средняя|Задержка успешно завершенных выполнений рабочего процесса.|Нет измерений|
|RunThrottledEvents|Run Throttled Events|Count|Всего|Число событий регулирования действия или триггера рабочего процесса.|Нет измерений|
|RunFailurePercentage|Run Failure Percentage|Процент|Всего|Процент выполнений рабочего процесса, завершившихся сбоем.|Нет измерений|
|ActionsStarted|Actions Started |Count|Всего|Число запущенных действий рабочего процесса.|Нет измерений|
|ActionsCompleted|Actions Completed |Count|Всего|Число завершенных действий рабочего процесса.|Нет измерений|
|ActionsSucceeded|Actions Succeeded |Count|Всего|Число успешно выполненных действий рабочего процесса.|Нет измерений|
|ActionsFailed|Actions Failed|Count|Всего|Число действий рабочего процесса, завершившихся сбоем.|Нет измерений|
|ActionsSkipped|Actions Skipped |Count|Всего|Число пропущенных действий рабочего процесса.|Нет измерений|
|ActionLatency|Action Latency |Секунды|Средняя|Задержка завершенных действий рабочего процесса.|Нет измерений|
|ActionSuccessLatency|Action Success Latency |Секунды|Средняя|Задержка успешно выполненных действий рабочего процесса.|Нет измерений|
|ActionThrottledEvents|Action Throttled Events|Count|Всего|Число событий регулирования действия рабочего процесса.|Нет измерений|
|TriggersStarted|Triggers Started |Count|Всего|Число запущенных триггеров рабочего процесса.|Нет измерений|
|TriggersCompleted|Triggers Completed |Count|Всего|Число завершенных триггеров рабочего процесса.|Нет измерений|
|TriggersSucceeded|Triggers Succeeded |Count|Всего|Число успешно выполненных триггеров рабочего процесса.|Нет измерений|
|TriggersFailed|Triggers Failed |Count|Всего|Число триггеров рабочего процесса, завершившихся со сбоем.|Нет измерений|
|TriggersSkipped|Triggers Skipped|Count|Всего|Число пропущенных триггеров рабочего процесса.|Нет измерений|
|TriggersFired|Triggers Fired |Count|Всего|Число активированных триггеров рабочего процесса.|Нет измерений|
|TriggerLatency|Trigger Latency |Секунды|Средняя|Задержка завершенных триггеров рабочего процесса.|Нет измерений|
|TriggerFireLatency|Trigger Fire Latency |Секунды|Средняя|Задержка активированных триггеров рабочего процесса.|Нет измерений|
|TriggerSuccessLatency|Trigger Success Latency |Секунды|Среднее|Задержка успешно выполненных триггеров рабочего процесса.|Нет измерений|
|TriggerThrottledEvents|Trigger Throttled Events|Count|Всего|Число событий регулирования триггера рабочего процесса.|Нет измерений|
|BillableActionExecutions|Billable Action Executions|Count|Всего|Число выполненных действий рабочего процесса, за которые выставляется счет.|Нет измерений|
|BillableTriggerExecutions|Billable Trigger Executions|Count|Всего|Число выполненных триггеров рабочего процесса, за которые выставляется счет.|Нет измерений|
|TotalBillableExecutions|Total Billable Executions|Count|Всего|Число выполнений рабочего процесса, за которые выставляется счет.|Нет измерений|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|VipAvailability|Доступность виртуального IP-адреса|Count|Средняя|Доступность конечных точек виртуального IP-адреса на основе результатов проверки|VipAddress, VipPort|
|DipAvailability|Доступность выделенного IP-адреса (DIP)|Count|Среднее|Доступность конечных точек выделенного IP-адреса на основе результатов проверки|ProtocolType, DipPort, VipAddress, VipPort, DipAddress|
|ByteCount|Количество байтов|Count|Всего|Общее количество байтов, переданных за период времени|VipAddress, VipPort, Direction|
|PacketCount|Количество пакетов|Count|Всего|Общее количество пакетов, переданных за период времени|VipAddress, VipPort, Direction|
|SYNCount|Количество пакетов SYN|Count|Всего|Общее количество пакетов SYN, переданных за период времени|VipAddress, VipPort, Direction|
|SnatConnectionCount|Количество подключений SNAT|Count|Всего|Общее количество подключений SNAT, созданных за период времени|VipAddress, DipAddress, ConnectionState|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|QueryVolume|Объем запросов|Count|Всего|Количество запросов, выполненных для зоны DNS|Нет измерений|
|RecordSetCount|Количество наборов записей|Count|Максимальная|Количество наборов записей в зоне DNS|Нет измерений|
|RecordSetCapacityUtilization|Использование емкости, доступной для наборов записей|Процент|Максимальная|Доля от общей емкости для наборов записей, используемая зоной DNS, в процентах|Нет измерений|

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
|IfUnderDDoSAttack|Выполняется ли атака DDoS|Count|Максимальная|Выполняется ли атака DDoS|Нет измерений|
|DDoSTriggerTCPPackets|Входящие пакеты TCP для активации защиты от атаки DDoS|Число/с|Максимальная|Входящие пакеты TCP для активации защиты от атаки DDoS|Нет измерений|
|DDoSTriggerUDPPackets|Входящие пакеты UDP для активации защиты от атаки DDoS|Число/с|Максимальная|Входящие пакеты UDP для активации защиты от атаки DDoS|Нет измерений|
|DDoSTriggerSYNPackets|Входящие пакеты SYN для активации защиты от атаки DDoS|Число/с|Максимальная|Входящие пакеты SYN для активации защиты от атаки DDoS|Нет измерений|
|VipAvailability|Доступность|Count|Средняя|Средняя доступность IP-адреса за период времени|Порт|
|ByteCount|Количество байтов|Count|Всего|Общее количество байтов, переданных за период времени|Port, Direction|
|PacketCount|Количество пакетов|Count|Всего|Общее количество пакетов, переданных за период времени|Port, Direction|
|SynCount|Количество пакетов SYN|Count|Всего|Общее количество пакетов SYN, переданных за период времени|Port, Direction|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|Throughput|Throughput|Байт/с|Всего|Количество байтов в секунду, обрабатываемых шлюзом приложений|Нет измерений|
|UnhealthyHostCount|Количество неработоспособных узлов|Count|Средняя|Количество неработоспособных узлов серверной части. Можно выполнить фильтрацию по серверному пулу для отображения работоспособных и неработоспособных узлов в определенном серверном пуле.|BackendSettingsPool|
|HealthyHostCount|Количество работоспособных узлов.|Count|Средняя|Количество работоспособных узлов серверной части. Можно выполнить фильтрацию по серверному пулу для отображения работоспособных и неработоспособных узлов в определенном серверном пуле.|BackendSettingsPool. |
|TotalRequests|Общее количество запросов|Count|Всего|Число успешных запросов, обработанных шлюзом приложений|BackendSettingsPool|
|FailedRequests|Невыполненные запросы|Count|Всего|Число запросов со сбоем, обработанных шлюзом приложений|BackendSettingsPool|
|ResponseStatus|Состояние ответа.|Count|Всего|Состояние ответа HTTP, возвращенное шлюзом приложений. Распределение кодов состояния ответа можно дополнительно сгруппировать, чтобы отобразить ответы по категориям 2xx, 3xx, 4xx и 5xx.|HttpStatusGroup|
|CurrentConnections|Текущие подключения.|Count|Всего|Число текущих установленных подключений к шлюзу приложений|Нет измерений|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|TunnelAverageBandwidth|Пропускная способность туннеля|Байт/с|Среднее|Средняя пропускная способность туннеля в байтах в секунду|ConnectionName, RemoteIP|
|TunnelEgressBytes|Исходящие байты туннеля|Байты|Всего|Исходящие байты в туннеле|ConnectionName, RemoteIP|
|TunnelIngressBytes|Входящие байты туннеля|Байты|Всего|Входящие байты в туннеле|ConnectionName, RemoteIP|
|TunnelEgressPackets|Исходящие пакеты туннеля|Count|Всего|Количество исходящих пакетов в туннеле|ConnectionName, RemoteIP|
|TunnelIngressPackets|Входящие пакеты туннеля|Count|Всего|Количество входящих пакетов в туннеле|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Удаленные входящие пакеты туннеля (несоответствие селектора трафика)|Count|Всего|Количество удаленных исходящих пакетов из-за несоответствия селектора трафика в туннеле|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Удаленные исходящие пакеты туннеля (несоответствие селектора трафика)|Count|Всего|Количество удаленных входящих пакетов из-за несоответствия селектора трафика в туннеле|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|Число/с|Средняя|Входящий трафик Azure в секунду (в битах)|Нет измерений|
|BitsOutPerSecond|BitsOutPerSecond|Число/с|Средняя|Исходящий трафик Azure в секунду (в битах)|Нет измерений|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|QpsByEndpoint|Запросы, выполняемые возвращаемой конечной точкой|Count|Всего|Сколько раз конечная точка диспетчера трафика возвращалась в заданный период времени|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Проверка состояния конечной точки с помощью конечной точки|Count|Максимальная|1, если состояние проверки конечной точки Enabled, в противном случае используется значение 0.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|ProbesFailedPercent|Доля проб с ошибками, в процентах|Процент|Средняя|Доля проб мониторинга подключения с ошибками, в процентах|Нет измерений|
|AverageRoundtripMs|Среднее Время приема-передачи (мс)|MilliSeconds|Средняя|Среднее время приема-передачи (мс) в сети, вычисляемое для проб мониторинга подключения между источником и назначением|Нет измерений|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|registration.all|Операции регистрации|Count|Всего|Число всех успешных операций регистрации (создание, запрос, обновление и удаление). |Нет измерений|
|registration.create|Операции создания регистрации|Count|Всего|Число всех успешных операций создания регистраций.|Нет измерений|
|registration.update|Операции обновления регистрации|Count|Всего|Число всех успешных операций обновления регистраций.|Нет измерений|
|registration.get|Операции чтения регистрации|Count|Всего|Число всех успешных запросов регистрации.|Нет измерений|
|registration.delete|Операции удаления регистрации|Count|Всего|Число всех успешных операций удаления регистраций.|Нет измерений|
|incoming|Входящие сообщения|Count|Всего|Число всех успешно отправленных вызовов API. |Нет измерений|
|incoming.scheduled|Отправлено запланированных push-уведомлений|Count|Всего|Отменено запланированных push-уведомлений|Нет измерений|
|incoming.scheduled.cancel|Отменено запланированных push-уведомлений|Count|Всего|Отменено запланированных push-уведомлений|Нет измерений|
|scheduled.pending|Запланированных уведомлений в состоянии ожидания|Count|Всего|Запланированных уведомлений в состоянии ожидания|Нет измерений|
|installation.all|Операции управления установкой|Count|Всего|Операции управления установкой|Нет измерений|
|installation.get|Операции получения установки|Count|Всего|Операции получения установки|Нет измерений|
|installation.upsert|Операции создания или обновления установки|Count|Всего|Операции создания или обновления установки|Нет измерений|
|installation.patch|Операции исправления установки|Count|Всего|Операции исправления установки|Нет измерений|
|installation.delete|Операции удаления установки|Count|Всего|Операции удаления установки|Нет измерений|
|outgoing.allpns.success|Успешные уведомления|Count|Всего|Общее число успешных уведомлений.|Нет измерений|
|outgoing.allpns.invalidpayload|Ошибки полезных данных|Count|Всего|Количество неудачных отправлений из-за того, что PNS вернула ошибку полезных данных.|Нет измерений|
|outgoing.allpns.pnserror|Ошибки внешней системы уведомлений|Count|Всего|Количество неудачных отправлений из-за ошибки связи с PNS (за исключением проблем с проверкой подлинности).|Нет измерений|
|outgoing.allpns.channelerror|Ошибки канала|Count|Всего|Количество неудачных отправлений из-за недопустимого канала, не связанного с соответствующим регулируемым или просроченным приложением.|Нет измерений|
|outgoing.allpns.badorexpiredchannel|Ошибки из-за поврежденного или просроченного канала|Count|Всего|Количество неудачных отправлений из-за недопустимого или просроченного канала, маркера или идентификатора регистрации в регистрации.|Нет измерений|
|outgoing.wns.success|Успешные уведомления WNS|Count|Всего|Общее число успешных уведомлений.|Нет измерений|
|outgoing.wns.invalidcredentials|Ошибки авторизации WNS (недопустимые учетные данные)|Count|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных. (Windows Live не распознает учетные данные.)|Нет измерений|
|outgoing.wns.badchannel|Ошибка из-за поврежденного канала WNS|Count|Всего|Количество неудачных отправлений из-за ошибки распознавания универсального кода ресурса (URI) канала в регистрации (состояние WNS: 404 — не найдено).|Нет измерений|
|outgoing.wns.expiredchannel|Ошибка из-за просроченного канала WNS|Count|Всего|Количество неудачных отправлений из-за просроченного универсального кода ресурса (URI) канала (состояние WNS: 410 — потеряно).|Нет измерений|
|outgoing.wns.throttled|Регулируемые уведомления WNS|Count|Всего|Количество неудачных отправлений из-за регулирования этого приложения с помощью WNS (состояние WNS: 406 — неприемлемо).|Нет измерений|
|outgoing.wns.tokenproviderunreachable|Ошибки авторизации WNS (нет доступа)|Count|Всего|Windows Live недоступна.|Нет измерений|
|outgoing.wns.invalidtoken|Ошибки авторизации WNS (недопустимый маркер)|Count|Всего|WNS предоставлен недопустимый маркер (состояние WNS: 401 — не санкционировано).|Нет измерений|
|outgoing.wns.wrongtoken|Ошибки авторизации WNS (неправильный маркер)|Count|Всего|Маркер, предоставленный для WNS, допустимый, но он предназначен для другого приложения (состояние WNS: 403 — запрещено). Это возможно, если универсальный код ресурса (URI) канала в регистрации связан с другим приложением. Убедитесь, что клиентское приложение связано с приложением, учетные данные которого находятся в центре уведомлений.|Нет измерений|
|outgoing.wns.invalidnotificationformat|Недопустимый формат уведомления WNS|Count|Всего|Недопустимый формат уведомления (состояние WNS: 400). Обратите внимание, что WNS не отклоняет все недопустимые полезные данные.|Нет измерений|
|outgoing.wns.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления WNS|Count|Всего|Полезные данные уведомления слишком большие (состояние WNS: 413).|Нет измерений|
|outgoing.wns.channelthrottled|Канал WNS регулируется|Count|Всего|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (заголовок ответа WNS: состояние уведомления X-WNS: канал регулируется).|Нет измерений|
|outgoing.wns.channeldisconnected|Канал WNS отсоединен|Count|Всего|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (заголовок ответа WNS: состояние подключения устройства X-WNS: отключено).|Нет измерений|
|outgoing.wns.dropped|Пропущенные уведомления WNS|Count|Всего|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (состояние уведомления X-WNS: пропущено. Состояние подключения устройства не X-WNS: отключено).|Нет измерений|
|outgoing.wns.pnserror|Ошибки WNS|Count|Всего|Уведомление не доставлено из-за ошибок связи с WNS.|Нет измерений|
|outgoing.wns.authenticationerror|Ошибки проверки подлинности WNS|Count|Всего|Уведомление не доставлено из-за ошибок связи Windows Live с использованием недопустимых учетных данных или неправильного маркера.|Нет измерений|
|outgoing.apns.success|Успешные уведомления APNS|Count|Всего|Общее число успешных уведомлений.|Нет измерений|
|outgoing.apns.invalidcredentials|Ошибки авторизации APNS|Count|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Нет измерений|
|outgoing.apns.badchannel|Ошибка из-за поврежденного канала APNS|Count|Всего|Количество неудачных отправлений из-за недопустимого маркера (код состояния APNS: 8).|Нет измерений|
|outgoing.apns.expiredchannel|Ошибка из-за просроченного канала APNS|Count|Всего|Количество маркеров, которые были аннулированы каналом обратной связи APNS.|Нет измерений|
|outgoing.apns.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления APNS|Count|Всего|Количество неудачных отправлений из-за большого объема полезных данных (код состояния APNS: 7).|Нет измерений|
|outgoing.apns.pnserror|Ошибки APNS|Count|Всего|Количество неудачных отправлений из-за ошибок связи с APNS.|Нет измерений|
|outgoing.gcm.success|Успешные уведомления GCM|Count|Всего|Общее число успешных уведомлений.|Нет измерений|
|outgoing.gcm.invalidcredentials|Ошибки авторизации GCM (недопустимые учетные данные)|Count|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Нет измерений|
|outgoing.gcm.badchannel|Ошибка из-за поврежденного канала GCM|Count|Всего|Количество неудачных отправлений из-за ошибки распознавания идентификатора регистрации в регистрации (результат GCM: недействительная регистрация).|Нет измерений|
|outgoing.gcm.expiredchannel|Ошибка из-за просроченного канала GCM|Count|Всего|Количество неудачных отправлений из-за просроченного идентификатора регистрации в регистрации (результат GCM: не зарегистрировано).|Нет измерений|
|outgoing.gcm.throttled|Регулируемые уведомления GCM|Count|Всего|Количество неудачных отправлений из-за регулирования этого приложения GCM (код состояния GCM: 501–599; либо результат: недоступен).|Нет измерений|
|outgoing.gcm.invalidnotificationformat|Недопустимый формат уведомления GCM|Count|Всего|Количество неудачных отправлений из-за неправильного формата полезных данных (результат GCM: недопустимый ключ данных или недопустимый TTL).|Нет измерений|
|outgoing.gcm.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления GCM|Count|Всего|Количество неудачных отправлений из-за большого объема полезных данных (результат GCM: слишком большое сообщение).|Нет измерений|
|outgoing.gcm.wrongchannel|Ошибка из-за неправильного канала GCM|Count|Всего|Количество неудачных отправлений из-за того, что идентификатор регистрации в регистрации не связан с текущим приложением (результат GCM: недопустимое имя пакета).|Нет измерений|
|outgoing.gcm.pnserror|Ошибки GCM|Count|Всего|Количество неудачных отправлений из-за ошибок связи с GCM.|Нет измерений|
|outgoing.gcm.authenticationerror|Ошибки проверки подлинности GCM|Count|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных по причине использования блокированных учетных данных или неправильно настроенного идентификатора отправителя в приложении (результат GCM: несовпадающий код отправителя).|Нет измерений|
|outgoing.mpns.success|Успешные уведомления MPNS|Count|Всего|Общее число успешных уведомлений.|Нет измерений|
|outgoing.mpns.invalidcredentials|Недопустимые учетные данные MPNS|Count|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Нет измерений|
|outgoing.mpns.badchannel|Ошибка из-за поврежденного канала MPNS|Count|Всего|Количество неудачных отправлений из-за ошибки распознавания универсального кода ресурса (URI) канала в регистрации (состояние MPNS: 404 — не найдено).|Нет измерений|
|outgoing.mpns.throttled|Регулируемые уведомления MPNS|Count|Всего|Количество неудачных отправлений из-за регулирования этого приложения MPNS (WNS MPNS: 406 — неприемлемо).|Нет измерений|
|outgoing.mpns.invalidnotificationformat|Недопустимый формат уведомления MPNS|Count|Всего|Количество неудачных отправлений из-за большого объема полезных данных уведомления.|Нет измерений|
|outgoing.mpns.channeldisconnected|Канал MPNS отсоединен|Count|Всего|Количество неудачных отправлений из-за отсоединения универсального кода ресурса (URI) канала в регистрации (состояние MPNS: 412 — не найдено).|Нет измерений|
|outgoing.mpns.dropped|Пропущенные уведомления MPNS|Count|Всего|Количество отправлений, пропущенных MPNS (заголовок ответа MPNS: состояние уведомления X — очередь переполнена или подавлено).|Нет измерений|
|outgoing.mpns.pnserror|Ошибки MPNS|Count|Всего|Количество неудачных отправлений из-за ошибок связи с MPNS.|Нет измерений|
|outgoing.mpns.authenticationerror|Ошибки проверки подлинности MPNS|Count|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|Нет измерений|
|notificationhub.pushes|Все исходящие уведомления|Count|Всего|Все исходящие уведомления из центра уведомлений.|Нет измерений|
|incoming.all.requests|Все входящие запросы|Count|Всего|Общее количество входящих запросов для концентратора уведомлений|Нет измерений|
|Incoming.all.failedrequests|Все неудачные входящие запросы|Count|Всего|Общее количество неудачных входящих запросов для концентратора уведомлений|Нет измерений|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|QueryDuration|Длительность запросов|Миллисекунды|Средняя|Длительность запроса DAX в последнем интервале|Нет измерений|
|QueryPoolJobQueueLength|Потоки: длина очереди заданий пула запросов|Count|Средняя|Число заданий в очереди пула потоков запросов.|Нет измерений|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces
(Общедоступная предварительная версия)

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|Average_% Free Inodes|Average_% Free Inodes|Count|Средняя|Average_% Free Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|Average_% Free Space|Count|Среднее|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|Average_% Used Inodes|Count|Средняя|Average_% Used Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|Average_% Used Space|Count|Средняя|Average_% Used Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|Average_Disk Read Bytes/sec|Count|Среднее|Average_Disk Read Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Average_Disk Reads/sec|Count|Средняя|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Average_Disk Transfers/sec|Count|Средняя|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|Average_Disk Write Bytes/sec|Count|Средняя|Average_Disk Write Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Average_Disk Writes/sec|Count|Среднее|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Average_Free Megabytes|Count|Средняя|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|Average_Logical Disk Bytes/sec|Count|Средняя|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Memory|Average_% Available Memory|Count|Среднее|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|Average_% Available Swap Space|Count|Средняя|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|Average_% Used Memory|Count|Средняя|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|Average_% Used Swap Space|Count|Средняя|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|Average_Available MBytes Memory|Count|Среднее|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|Average_Available MBytes Swap|Count|Средняя|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|Average_Page Reads/sec|Count|Среднее|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|Average_Page Writes/sec|Count|Средняя|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Average_Pages/sec|Count|Средняя|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Average_Used MBytes Swap Space|Count|Среднее|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Average_Used Memory MBytes|Count|Среднее|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|Average_Total Bytes Transmitted|Count|Среднее|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|Average_Total Bytes Received|Count|Среднее|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|Average_Total Bytes|Count|Средняя|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|Average_Total Packets Transmitted|Count|Средняя|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|Average_Total Packets Received|Count|Средняя|Average_Total Packets Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|Average_Total Rx Errors|Count|Средняя|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|Average_Total Tx Errors|Count|Средняя|Average_Total Tx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|Average_Total Collisions|Count|Средняя|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. время чтения с диска (с)|Average_Avg. время чтения с диска (с)|Count|Средняя|Average_Avg. время чтения с диска (с)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. время обращения к диску (с)|Average_Avg. время обращения к диску (с)|Count|Средняя|Average_Avg. время обращения к диску (с)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. время записи на диск (с)|Average_Avg. время записи на диск (с)|Count|Средняя|Average_Avg. время записи на диск (с)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|Average_Physical Disk Bytes/sec|Count|Средняя|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Average_Pct Privileged Time|Count|Средняя|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|Average_Pct User Time|Count|Средняя|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|Average_Used Memory kBytes|Count|Средняя|Average_Used Memory kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|Average_Virtual Shared Memory|Count|Среднее|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|Average_% DPC Time|Count|Средняя|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|Average_% Idle Time|Count|Средняя|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|Average_% Interrupt Time|Count|Средняя|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|Average_% IO Wait Time|Count|Среднее|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|Average_% Nice Time|Count|Средняя|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|Average_% Privileged Time|Count|Средняя|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|Average_% Processor Time|Count|Средняя|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|Average_% User Time|Count|Средняя|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|Average_Free Physical Memory|Count|Средняя|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|Average_Free Space in Paging Files|Count|Средняя|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|Average_Free Virtual Memory|Count|Средняя|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Average_Processes|Count|Средняя|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Average_Size Stored In Paging Files|Count|Средняя|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Average_Uptime|Count|Средняя|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Average_Users|Count|Среднее|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. время чтения с диска (с)|Average_Avg. время чтения с диска (с)|Count|Средняя|Average_Avg. время чтения с диска (с)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. время записи на диск (с)|Average_Avg. время записи на диск (с)|Count|Среднее|Average_Avg. время записи на диск (с)|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Average_Current Disk Queue Length|Count|Средняя|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Average_Disk Reads/sec|Count|Среднее|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Average_Disk Transfers/sec|Count|Среднее|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Average_Disk Writes/sec|Count|Среднее|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Average_Free Megabytes|Count|Средняя|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|Average_% Free Space|Count|Средняя|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|Average_Available MBytes|Count|Средняя|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|Average_% Committed Bytes In Use|Count|Среднее|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Average_Bytes Received/sec|Count|Средняя|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Average_Bytes Sent/sec|Count|Среднее|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Average_Bytes Total/sec|Count|Среднее|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|Average_% Processor Time|Count|Среднее|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|Average_Processor Queue Length|Count|Среднее|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Пульс|Пульс|Count|Средняя|Пульс|Computer, OSType, Version, SourceComputerId|
|Блокировка изменений|Блокировка изменений|Count|Средняя|Блокировка изменений|Computer, Product, Classification, UpdateState, Optional, Approved|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|Всего|Выполненные ListenerConnections для Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|Всего|ClientError в ListenerConnections для Microsoft.Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Count|Всего|ServerError в ListenerConnections для Microsoft.Relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Count|Всего|Выполненные SenderConnections для Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|Всего|ClientError в SenderConnections для Microsoft.Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Count|Всего|ServerError в SenderConnections для Microsoft.Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|Всего|Всего ListenerConnections для Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|Всего|Всего запросов SenderConnections для Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Count|Всего|Всего ActiveConnections для Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Count|Всего|Всего ActiveListeners для Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Count|Всего|Всего BytesTransferred для Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|Всего|Всего ListenerDisconnects для Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Count|Всего|Всего SenderDisconnects для Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|SearchLatency|Search Latency|Секунды|Среднее|Среднее время задержки поиска для службы поиска.|Нет измерений|
|SearchQueriesPerSecond|Search queries per second|Число/с|Среднее|Число поисковых запросов в секунду для службы поиска.|Нет измерений|
|ThrottledSearchQueriesPercentage|Throttled search queries percentage|Процент|Средняя|Процент отрегулированных поисковых запросов для службы поиска.|Нет измерений|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|SuccessfulRequests|Выполненные запросы (предварительная версия)|Count|Всего|Общее количество выполненных запросов для пространства имен (предварительная версия)|EntityName, |
|ServerErrors|Ошибки сервера. (предварительная версия)|Count|Всего|Ошибки на сервере для Microsoft.ServiceBus. (предварительная версия)|EntityName, |
|UserErrors|Ошибки пользователей. (предварительная версия)|Count|Всего|Ошибки пользователей для Microsoft.ServiceBus. (предварительная версия)|EntityName, |
|ThrottledRequests|Регулируемые запросы. (предварительная версия)|Count|Всего|Регулируемые запросы для Microsoft.ServiceBus. (предварительная версия)|EntityName, |
|IncomingRequests|Входящие запросы (предварительная версия)|Count|Всего|Входящие запросы для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|IncomingMessages|Входящие сообщения (предварительная версия)|Count|Всего|Входящие сообщения для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|OutgoingMessages|Исходящие сообщения (предварительная версия)|Count|Всего|Исходящие сообщения для Microsoft.ServiceBus. (предварительная версия)|EntityName|
|ActiveConnections|Активные подключения (предварительная версия)|Count|Всего|Число активных подключений для Microsoft.ServiceBus. (предварительная версия)|Нет измерений|
|Размер|Размер (предварительная версия)|Байты|Среднее|Размер очереди или раздела в байтах. (предварительная версия)|EntityName|
|Сообщения|Число сообщений в очереди или разделе. (предварительная версия)|Count|Средняя|Число сообщений в очереди или разделе. (предварительная версия)|EntityName|
|ActiveMessages|Число активных сообщений в очереди или разделе. (предварительная версия)|Count|Средняя|Число активных сообщений в очереди или разделе. (предварительная версия)|EntityName|
|CPUXNS|CPU usage per namespace|Процент|Максимальная|Метрика использования ЦП пространства имен служебной шины для премиум-обслуживания|Нет измерений|
|WSXNS|Memory size usage per namespace|Процент|Максимальная|Метрика использования памяти пространства имен служебной шины для премиум-обслуживания|Нет измерений|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Процент использования ЦП|Процент|Средняя|Процент использования ЦП|Нет измерений|
|physical_data_read_percent|Процент операций ввода/вывода данных|Процент|Средняя|Процент операций ввода/вывода данных|Нет измерений|
|log_write_percent|Log IO percentage|Процент|Средняя|Log IO percentage|Нет измерений|
|dtu_consumption_percent|Процент использования DTU|Процент|Средняя|Процент использования DTU|Нет измерений|
|storage|Total database size|Байты|Максимальная|Total database size|Нет измерений|
|connection_successful|Успешные подключения|Count|Всего|Успешные подключения|Нет измерений|
|connection_failed|Неудачные подключения|Count|Всего|Неудачные подключения|Нет измерений|
|blocked_by_firewall|Заблокировано брандмауэром|Count|Всего|Заблокировано брандмауэром|Нет измерений|
|deadlock|Взаимоблокировки|Count|Всего|Взаимоблокировки|Нет измерений|
|storage_percent|Размер базы данных в процентах|Процент|Максимальная|Размер базы данных в процентах|Нет измерений|
|xtp_storage_percent|Процент хранилища выполняющейся в памяти OLTP|Процент|Средняя|Процент хранилища выполняющейся в памяти OLTP|Нет измерений|
|workers_percent|Workers percentage|Процент|Средняя|Workers percentage|Нет измерений|
|sessions_percent|Процент использования сеансов|Процент|Средняя|Процент использования сеансов|Нет измерений|
|dtu_limit|Лимит DTU|Count|Средняя|Лимит DTU|Нет измерений|
|dtu_used|DTU used|Count|Средняя|DTU used|Нет измерений|
|dwu_limit|Лимит DWU|Count|Максимальная|Лимит DWU|Нет измерений|
|dwu_consumption_percent|DWU percentage|Процент|Максимальная|DWU percentage|Нет измерений|
|dwu_used|DWU used|Count|Максимальная|DWU used|Нет измерений|
|dw_cpu_percent|Процент использования ЦП на уровне узла DW|Процент|Средняя|Процент использования ЦП на уровне узла DW|DwLogicalNodeId|
|dw_physical_data_read_percent|Процент операций ввода-вывода данных на уровне узла DW|Процент|Средняя|Процент операций ввода-вывода данных на уровне узла DW|DwLogicalNodeId|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|cpu_percent|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|Нет измерений|
|physical_data_read_percent|Процент операций ввода/вывода данных|Процент|Средняя|Процент операций ввода/вывода данных|Нет измерений|
|log_write_percent|Log IO percentage|Процент|Средняя|Log IO percentage|Нет измерений|
|dtu_consumption_percent|Процент использования DTU|Процент|Средняя|Процент использования DTU|Нет измерений|
|storage_percent|Storage percentage|Процент|Средняя|Storage percentage|Нет измерений|
|workers_percent|Workers percentage|Процент|Средняя|Workers percentage|Нет измерений|
|sessions_percent|Процент использования сеансов|Процент|Средняя|Процент использования сеансов|Нет измерений|
|eDTU_limit|eDTU limit|Count|Средняя|eDTU limit|Нет измерений|
|storage_limit|Storage limit|Байты|Средняя|Storage limit|Нет измерений|
|eDTU_used|eDTU used|Count|Средняя|eDTU used|Нет измерений|
|storage_used|Storage used|Байты|Средняя|Storage used|Нет измерений|
|xtp_storage_percent|Процент хранилища выполняющейся в памяти OLTP|Процент|Среднее|Процент хранилища выполняющейся в памяти OLTP|Нет измерений|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|dtu_consumption_percent|Процент использования DTU|Процент|Средняя|Процент использования DTU|ElasticPoolResourceId|
|storage_used|Storage used|Байты|Средняя|Storage used|ElasticPoolResourceId|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|UsedCapacity|Используемая емкость|Байты|Средняя|Используемая емкость учетной записи|Нет измерений|
|Транзакции|Транзакции|Count|Всего|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName|
|Входящие|Входящие|Байты|Всего|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName|
|Исходящие|Исходящие|Байты|Всего|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName|
|Доступность|Доступность|Процент|Средняя|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|BlobCapacity|Емкость больших двоичных объектов|Байты|Всего|Объем хранилища, используемый службой BLOB-объектов учетной записи хранения, в байтах.|BlobType|
|BlobCount|Количество больших двоичных объектов|Count|Всего|Количество больших двоичных объектов в службе BLOB-объектов учетной записи хранения.|BlobType|
|ContainerCount|Количество контейнеров больших двоичных объектов|Count|Среднее|Количество контейнеров в службе BLOB-объектов учетной записи хранения.|Нет измерений|
|Транзакции|Транзакции|Count|Всего|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName|
|Входящие|Входящие|Байты|Всего|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName|
|Исходящие|Исходящие|Байты|Всего|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Среднее|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Средняя|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName|
|Доступность|Доступность|Процент|Средняя|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|TableCapacity|Емкость таблицы|Байты|Средняя|Объем хранилища, используемый службой таблиц учетной записи хранения, в байтах.|Нет измерений|
|TableCount|Количество таблиц|Count|Средняя|Количество таблиц в службе таблиц учетной записи хранения.|Нет измерений|
|TableEntityCount|Количество сущностей таблиц|Count|Среднее|Количество сущностей таблиц в службе таблиц учетной записи хранения.|Нет измерений|
|Транзакции|Транзакции|Count|Всего|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName|
|Входящие|Входящие|Байты|Всего|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName|
|Исходящие|Исходящие|Байты|Всего|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Средняя|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName|
|Доступность|Доступность|Процент|Средняя|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|QueueCapacity|Емкость очереди|Байты|Среднее|Объем хранилища, используемый службой очередей учетной записи хранения, в байтах.|Нет измерений|
|QueueCount|Количество очередей|Count|Средняя|Количество очередей в службе очередей учетной записи хранения.|Нет измерений|
|QueueMessageCount|Количество сообщений очереди|Count|Среднее|Приблизительное количество сообщений очередей в службе очередей учетной записи хранения.|Нет измерений|
|Транзакции|Транзакции|Count|Всего|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName|
|Входящие|Входящие|Байты|Всего|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName|
|Исходящие|Исходящие|Байты|Всего|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Средняя|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Средняя|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName|
|Доступность|Доступность|Процент|Среднее|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|FileCapacity|Емкость файла|Байты|Среднее|Объем хранилища, используемый службой файлов учетной записи хранения, в байтах.|Нет измерений|
|FileCount|Количество файлов|Count|Средняя|Количество файлов в службе файлов учетной записи хранения.|Нет измерений|
|FileShareCount|Количество общих файловых ресурсов|Count|Средняя|Количество общих файловых ресурсов в службе файлов учетной записи хранения.|Нет измерений|
|Транзакции|Транзакции|Count|Всего|Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. Используйте измерение ResponseType для различных типов ответа.|ResponseType, GeoType, ApiName|
|Входящие|Входящие|Байты|Всего|Объем входящих данных в байтах. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure.|GeoType, ApiName|
|Исходящие|Исходящие|Байты|Всего|Объем входящих данных в байтах. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации.|GeoType, ApiName|
|SuccessServerLatency|Успешно выполненная задержка сервера|Миллисекунды|Средняя|Средняя задержка, используемая службой хранилища Azure для обработки успешного запроса, в миллисекундах. Это значение не включает сетевую задержку, указанную в метрике AverageE2ELatency.|GeoType, ApiName|
|SuccessE2ELatency|Успешно выполненная задержка E2E|Миллисекунды|Среднее|Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API в миллисекундах. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа.|GeoType, ApiName|
|Доступность|Доступность|Процент|Средняя|Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая те, которые вызвали непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API.|GeoType, ApiName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|ResourceUtilization|Использование единиц потоковой передачи (%)|Процент|Максимальная|Использование единиц потоковой передачи (%)|Нет измерений|
|InputEvents|Входные события|Count|Всего|Входные события|Нет измерений|
|InputEventBytes|Байты входного события|Байты|Всего|Байты входного события|Нет измерений|
|LateInputEvents|События позднего ввода|Count|Всего|События позднего ввода|Нет измерений|
|OutputEvents|Выходные события|Count|Всего|Выходные события|Нет измерений|
|ConversionErrors|Ошибки преобразования данных|Count|Всего|Ошибки преобразования данных|Нет измерений|
|Errors|Ошибки среды выполнения|Count|Всего|Ошибки среды выполнения|Нет измерений|
|DroppedOrAdjustedEvents|Неупорядоченные события|Count|Всего|Неупорядоченные события|Нет измерений|
|AMLCalloutRequests|Запросы функций|Count|Всего|Запросы функций|Нет измерений|
|AMLCalloutFailedRequests|Неудачные запросы функций|Count|Всего|Неудачные запросы функций|Нет измерений|
|AMLCalloutInputEvents|События функций|Count|Всего|События функций|Нет измерений|
|DeserializationError|Ошибки десериализации входа|Count|Всего|Ошибки десериализации входа|Нет измерений|
|EarlyInputEvents|События, у которых время применения предшествует времени прибытия.|Count|Всего|События, у которых время применения предшествует времени прибытия.|Нет измерений|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|CpuPercentage|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|Экземпляр|
|MemoryPercentage|Процент использования памяти|Процент|Средняя|Процент использования памяти|Экземпляр|
|DiskQueueLength|Длина дисковой очереди|Count|Средняя|Длина дисковой очереди|Экземпляр|
|HttpQueueLength|Длина очереди HTTP:|Count|Среднее|Длина очереди HTTP:|Экземпляр|
|BytesReceived|Входящие данные:|Байты|Всего|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Всего|Выходные данные|Экземпляр|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (за исключением функций)

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|CpuTime|Время ЦП:|Секунды|Всего|Время ЦП:|Экземпляр|
|Requests|Requests|Count|Всего|Requests|Экземпляр|
|BytesReceived|Входящие данные:|Байты|Всего|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Всего|Выходные данные|Экземпляр|
|Http101|Http 101|Count|Всего|Http 101|Экземпляр|
|Http2xx|HTTP 2xx|Count|Всего|HTTP 2xx|Экземпляр|
|Http3xx|HTTP 3xx:|Count|Всего|HTTP 3xx:|Экземпляр|
|Http401|HTTP 401:|Count|Всего|HTTP 401:|Экземпляр|
|Http403|HTTP 403:|Count|Всего|HTTP 403:|Экземпляр|
|Http404|HTTP 404:|Count|Всего|HTTP 404:|Экземпляр|
|Http406|HTTP 406:|Count|Всего|HTTP 406:|Экземпляр|
|Http4xx|HTTP 4xx:|Count|Всего|HTTP 4xx:|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Count|Всего|Ошибки HTTP-сервера:|Экземпляр|
|MemoryWorkingSet|рабочий набор памяти;|Байты|Средняя|рабочий набор памяти;|Экземпляр|
|AverageMemoryWorkingSet|средний размер рабочего набора памяти;|Байты|Средняя|средний размер рабочего набора памяти;|Экземпляр|
|AverageResponseTime|Среднее время ответа:|Секунды|Среднее|Среднее время ответа:|Экземпляр|
|AppConnections|Подключения|Count|Средняя|Подключения|Экземпляр|
|Handles|Счетчик дескрипторов|Count|Средняя|Счетчик дескрипторов|Экземпляр|
|Потоки|Счетчик потоков|Count|Средняя|Счетчик потоков|Экземпляр|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (функции)

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|BytesReceived|Входящие данные:|Байты|Всего|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Всего|Выходные данные|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Count|Всего|Ошибки HTTP-сервера:|Экземпляр|
|MemoryWorkingSet|рабочий набор памяти;|Байты|Среднее|рабочий набор памяти;|Экземпляр|
|AverageMemoryWorkingSet|средний размер рабочего набора памяти;|Байты|Среднее|средний размер рабочего набора памяти;|Экземпляр|
|FunctionExecutionUnits|Function Execution Units|Count|Всего|Единицы выполнения функции.|Экземпляр|
|FunctionExecutionCount|Function Execution Count|Count|Всего|Function Execution Count|Экземпляр|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|CpuTime|Время ЦП:|Секунды|Всего|Время ЦП:|Экземпляр|
|Requests|Requests|Count|Всего|Requests|Экземпляр|
|BytesReceived|Входящие данные:|Байты|Всего|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Всего|Выходные данные|Экземпляр|
|Http101|Http 101|Count|Всего|Http 101|Экземпляр|
|Http2xx|HTTP 2xx|Count|Всего|HTTP 2xx|Экземпляр|
|Http3xx|HTTP 3xx:|Count|Всего|HTTP 3xx:|Экземпляр|
|Http401|HTTP 401:|Count|Всего|HTTP 401:|Экземпляр|
|Http403|HTTP 403:|Count|Всего|HTTP 403:|Экземпляр|
|Http404|HTTP 404:|Count|Всего|HTTP 404:|Экземпляр|
|Http406|HTTP 406:|Count|Всего|HTTP 406:|Экземпляр|
|Http4xx|HTTP 4xx:|Count|Всего|HTTP 4xx:|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Count|Всего|Ошибки HTTP-сервера:|Экземпляр|
|MemoryWorkingSet|рабочий набор памяти;|Байты|Средняя|рабочий набор памяти;|Экземпляр|
|AverageMemoryWorkingSet|средний размер рабочего набора памяти;|Байты|Среднее|средний размер рабочего набора памяти;|Экземпляр|
|AverageResponseTime|Среднее время ответа:|Секунды|Средняя|Среднее время ответа:|Экземпляр|
|FunctionExecutionUnits|Function Execution Units|Count|Всего|Единицы выполнения функции.|Экземпляр|
|FunctionExecutionCount|Function Execution Count|Count|Всего|Function Execution Count|Экземпляр|
|AppConnections|Подключения|Count|Среднее|Подключения|Экземпляр|
|Handles|Счетчик дескрипторов|Count|Среднее|Счетчик дескрипторов|Экземпляр|
|Потоки|Счетчик потоков|Count|Средняя|Счетчик потоков|Экземпляр|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|Requests|Requests|Count|Всего|Requests|Экземпляр|
|BytesReceived|Входящие данные:|Байты|Всего|Входящие данные:|Экземпляр|
|BytesSent|Выходные данные|Байты|Всего|Выходные данные|Экземпляр|
|Http101|Http 101|Count|Всего|Http 101|Экземпляр|
|Http2xx|HTTP 2xx|Count|Всего|HTTP 2xx|Экземпляр|
|Http3xx|HTTP 3xx:|Count|Всего|HTTP 3xx:|Экземпляр|
|Http401|HTTP 401:|Count|Всего|HTTP 401:|Экземпляр|
|Http403|HTTP 403:|Count|Всего|HTTP 403:|Экземпляр|
|Http404|HTTP 404:|Count|Всего|HTTP 404:|Экземпляр|
|Http406|HTTP 406:|Count|Всего|HTTP 406:|Экземпляр|
|Http4xx|HTTP 4xx:|Count|Всего|HTTP 4xx:|Экземпляр|
|Http5xx|Ошибки HTTP-сервера:|Count|Всего|Ошибки HTTP-сервера:|Экземпляр|
|AverageResponseTime|Среднее время ответа:|Секунды|Средняя|Среднее время ответа:|Экземпляр|
|CpuPercentage|Процент использования ЦП|Процент|Среднее|Процент использования ЦП|Экземпляр|
|MemoryPercentage|Процент использования памяти|Процент|Средняя|Процент использования памяти|Экземпляр|
|DiskQueueLength|Длина дисковой очереди|Count|Средняя|Длина дисковой очереди|Экземпляр|
|HttpQueueLength|Длина очереди HTTP:|Count|Среднее|Длина очереди HTTP:|Экземпляр|
|ActiveRequests|Активные запросы|Count|Всего|Активные запросы|Экземпляр|
|TotalFrontEnds|Общее число внешних интерфейсов|Count|Средняя|Общее число внешних интерфейсов|Нет измерений|
|SmallAppServicePlanInstances|Рабочие роли плана службы приложений уровня "Малый"|Count|Средняя|Рабочие роли плана службы приложений уровня "Малый"|Нет измерений|
|MediumAppServicePlanInstances|Рабочие процессы плана службы приложений уровня "Средний"|Count|Средняя|Рабочие процессы плана службы приложений уровня "Средний"|Нет измерений|
|LargeAppServicePlanInstances|Рабочие процессы плана службы приложений уровня "Крупный"|Count|Средняя|Рабочие процессы плана службы приложений уровня "Крупный"|Нет измерений|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|ОПИСАНИЕ|Измерения|
|---|---|---|---|---|---|
|WorkersTotal|Общее количество рабочих процессов|Count|Средняя|Общее количество рабочих процессов|Нет измерений|
|WorkersAvailable|Доступные рабочие процессы|Count|Средняя|Доступные рабочие процессы|Нет измерений|
|WorkersUsed|Использованные рабочие процессы|Count|Средняя|Использованные рабочие процессы|Нет измерений|
|CpuPercentage|Процент использования ЦП|Процент|Средняя|Процент использования ЦП|Экземпляр|
|MemoryPercentage|Процент использования памяти|Процент|Среднее|Процент использования памяти|Экземпляр|

## <a name="next-steps"></a>Дополнительная информация
* [Прочитайте о метриках в Azure Monitor](monitoring-overview-metrics.md)
* [Создайте оповещения на основе метрик](insights-receive-alert-notifications.md)
* [Экспортируйте метрики в хранилище, концентратор событий или Log Analytics](monitoring-overview-of-diagnostic-logs.md)
