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
ms.date: 9/6/2017
ms.author: ancav
ms.translationtype: HT
ms.sourcegitcommit: 9b7316a5bffbd689bdb26e9524129ceed06606d5
ms.openlocfilehash: 6f40f0cdb998bda9d936ecf87089e85713ed9e1b
ms.contentlocale: ru-ru
ms.lasthandoff: 09/08/2017

---
# <a name="supported-metrics-with-azure-monitor"></a>Метрики, поддерживаемые Azure Monitor
Azure Monitor обеспечивает несколько способов взаимодействия с метриками, включая создание диаграмм метрик на портале, доступ к метрикам через REST API или запрос метрик с помощью PowerShell или интерфейса командной строки. Ниже приведен полный список метрик, доступных в настоящее время в конвейере метрик Azure Monitor.

> [!NOTE]
> Другие метрики могут быть доступны на портале или при использовании интерфейсов API прежних версий. Этот список содержит только метрики, которые доступны при использовании общедоступной предварительной версии объединенного конвейера метрик Azure Monitor.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|qpu_metric|QPU|Count|Средняя|QPU. Диапазоны: 0–100 для S1, 0–200 для S2 и 0–400 для S4.|
|memory_metric|Память|Байты|Средняя|Память. Диапазоны: 0–25 ГБ для S1, 0–50 ГБ для S2 и 0–100 ГБ для S4.|
|TotalConnectionRequests|Общее число запросов на подключение|Count|Средняя|Общее число запросов на подключение. Поступившие запросы.|
|SuccessfullConnectionsPerSec|Число успешных подключений в секунду|Число/с|Средняя|Частота успешных установок подключений.|
|TotalConnectionFailures|Общее число неудачных подключений|Count|Средняя|Общее число неудачных попыток подключения.|
|CurrentUserSessions|Текущие пользовательские сеансы|Count|Средняя|Текущее число установленных пользовательских сеансов.|
|QueryPoolBusyThreads|Занятые потоки в пуле запросов|Count|Средняя|Число занятых потоков в пуле потоков запросов.|
|CommandPoolJobQueueLength|Длина очереди заданий пула команд|Count|Средняя|Число заданий в очереди пула потоков команд.|
|ProcessingPoolJobQueueLength|Длина очереди заданий пула обработки|Count|Средняя|Количество заданий, не связанных с вводом-выводом, в очереди пула потоков обработки.|
|CurrentConnections|Подключение: текущие подключения|Count|Средняя|Текущее число установленных клиентских подключений.|
|CleanerCurrentPrice|Память: текущая цена очистки|Count|Средняя|Текущая цена памяти, $/байт/время, нормализованная до 1000.|
|CleanerMemoryShrinkable|Память: сжимаемая память очистки|Байты|Средняя|Объем памяти (в байтах), доступной для очистки с помощью средства фоновой очистки.|
|CleanerMemoryNonshrinkable|Память: несжимаемая память очистки|Байты|Средняя|Объем памяти (в байтах), недоступной для очистки с помощью средства фоновой очистки.|
|MemoryUsage|Память: использование памяти|Байты|Средняя|Использование памяти процессом сервера согласно вычислениям стоимости памяти очистки. Равно сумме значений счетчика Process\PrivateBytes и объема сопоставленных в памяти данных за вычетом памяти, сопоставленной или выделенной xVelocity в подсистеме, выполняющейся в памяти аналитики (VertiPaq) сверх предела памяти подсистемы VertiPaq.|
|MemoryLimitHard|Память: твердый лимит памяти|Байты|Средняя|Твердый лимит памяти, из файла конфигурации.|
|MemoryLimitHigh|Память: верхний предел памяти|Байты|Средняя|Верхняя граница объема памяти, из файла конфигурации.|
|MemoryLimitLow|Память: нижний предел памяти|Байты|Средняя|Нижняя граница объема памяти, из файла конфигурации.|
|MemoryLimitVertiPaq|Память: ограничение памяти VertiPaq|Байты|Средняя|Ограничение памяти, из файла конфигурации.|
|Квота|Память: квота|Байты|Средняя|Текущая квота памяти, в байтах. Квота памяти также называется выделением или резервированием памяти.|
|QuotaBlocked|Память: заблокировано квот|Count|Средняя|Текущее число запросов на квоту, заблокированных до высвобождения других квот памяти.|
|VertiPaqNonpaged|Память: размер нестраничных данных VertiPaq|Байты|Средняя|Размер памяти (в байтах), заблокированной в рабочем множестве для использования в подсистеме памяти.|
|VertiPaqPaged|Память: размер страничных данных VertiPaq|Байты|Средняя|Размер памяти (в байтах), занятой страничными данными в памяти.|
|RowsReadPerSec|Обработка: считано строк в секунду|Число/с|Средняя|Интенсивность считывания строк из всех реляционных баз данных.|
|RowsConvertedPerSec|Обработка: преобразовано строк в секунду|Число/с|Средняя|Интенсивность преобразованных в ходе обработки строк.|
|RowsWrittenPerSec|Обработка: записано строк в секунду|Число/с|Средняя|Интенсивность записанных в ходе обработки строк.|
|CommandPoolBusyThreads|Потоки: занятые потоки в пуле команд|Count|Средняя|Число занятых потоков в пуле потоков команд.|
|CommandPoolIdleThreads|Потоки: бездействующие потоки в пуле команд|Count|Средняя|Число бездействующих потоков в пуле потоков команд.|
|LongParsingBusyThreads|Потоки: занятые потоки продолжительного анализа|Count|Средняя|Число занятых потоков в пуле потоков продолжительного анализа.|
|LongParsingIdleThreads|Потоки: бездействующие потоки продолжительного анализа|Count|Средняя|Число бездействующих потоков в пуле потоков продолжительного анализа.|
|LongParsingJobQueueLength|Потоки: длина очереди заданий продолжительного анализа|Count|Средняя|Число заданий в очереди пула потоков продолжительного анализа.|
|ProcessingPoolBusyIOJobThreads|Потоки: занятые потоки ввода-вывода в пуле обработки заданий|Count|Средняя|Количество потоков, выполняющих задания ввода-вывода, в пуле потоков обработки.|
|ProcessingPoolBusyNonIOThreads|Потоки: занятые потоки в пуле обработки, не связанные с вводом-выводом|Count|Средняя|Количество потоков, выполняющих задания, не связанные с вводом-выводом в пуле потоков обработки.|
|ProcessingPoolIOJobQueueLength|Потоки: длина очереди заданий ввода-вывода пула обработки|Count|Средняя|Количество заданий ввода-вывода в очереди пула потоков обработки.|
|ProcessingPoolIdleIOJobThreads|Потоки: бездействующие потоки ввода-вывода в пуле обработки заданий|Count|Средняя|Количество бездействующих потоков, выполняющих задания ввода-вывода, в пуле потоков обработки.|
|ProcessingPoolIdleNonIOThreads|Потоки: бездействующие потоки в пуле обработки, не связанные с вводом-выводом|Count|Средняя|Количество бездействующих потоков в пуле потоков обработки, выделенных для заданий, не связанных с вводом-выводом.|
|QueryPoolIdleThreads|Потоки: бездействующие потоки в пуле запросов|Count|Средняя|Количество бездействующих потоков, выполняющих задания ввода-вывода, в пуле потоков обработки.|
|QueryPoolJobQueueLength|Потоки: длина очереди заданий пула запросов|Count|Средняя|Число заданий в очереди пула потоков запросов.|
|ShortParsingBusyThreads|Потоки: занятые потоки быстрого анализа|Count|Средняя|Число занятых потоков в пуле потоков быстрого анализа.|
|ShortParsingIdleThreads|Потоки: бездействующие потоки быстрого анализа|Count|Средняя|Число бездействующих потоков в пуле потоков быстрого анализа.|
|ShortParsingJobQueueLength|Потоки: длина очереди заданий быстрого анализа|Count|Средняя|Число заданий в очереди пула потоков быстрого анализа.|
|memory_thrashing_metric|Пробуксовка памяти|Процент|Средняя|Среднее значение пробуксовки памяти.|
|mashup_engine_qpu_metric|QPU подсистемы M|Count|Средняя|Использование QPU процессами подсистемы гибридных веб-приложений|
|mashup_engine_memory_metric|Память подсистемы M|Байты|Средняя|Использование памяти процессами подсистемы гибридных веб-приложений|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|TotalRequests|Total Gateway Requests|Count|Всего|Число запросов к шлюзу|
|SuccessfulRequests|Successful Gateway Requests|Count|Всего|Число успешных запросов к шлюзу|
|UnauthorizedRequests|Неавторизованные запросы к шлюзу|Count|Всего|Число неавторизованных запросов к шлюзу|
|FailedRequests|Failed Gateway Requests|Count|Всего|Количество сбоев при обработке запросов к шлюзу.|
|OtherRequests|Другие запросы к шлюзу|Count|Всего|Количество других запросов к шлюзу|
|Длительность|Общая длительность запросов шлюза|Миллисекунды|Среднее и максимальное значения|Общая длительность запросов шлюза в миллисекундах|
|Емкость|Емкость (предварительная версия)|Процент|Среднее и максимальное значения|Метрика использования для службы ApiManagement|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|TotalJob|Всего заданий|Count|Всего|Общее количество заданий|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|CoreCount|Dedicated Core Count|Count|Всего|Общее число выделенных ядер в учетной записи пакетной службы.|
|TotalNodeCount|Dedicated Node Count|Count|Всего|Общее число выделенных узлов в учетной записи пакетной службы.|
|LowPriorityCoreCount|LowPriority Core Count|Count|Всего|Общее число низкоприоритетных ядер в учетной записи пакетной службы.|
|TotalLowPriorityNodeCount|Low-Priority Node Count|Count|Всего|Общее число низкоприоритетных узлов в учетной записи пакетной службы.|
|CreatingNodeCount|Creating Node Count|Count|Всего|Количество создаваемых узлов.|
|StartingNodeCount|Starting Node Count|Count|Всего|Число узлов, которые запускаются.|
|WaitingForStartTaskNodeCount|Waiting For Start Task Node Count|Count|Всего|Число узлов, ожидающих завершения задачи запуска.|
|StartTaskFailedNodeCount|Start Task Failed Node Count|Count|Всего|Число узлов, на которых произошел сбой задачи запуска.|
|IdleNodeCount|Idle Node Count|Count|Всего|Количество узлов в неактивном состоянии.|
|OfflineNodeCount|Offline Node Count|Count|Всего|Количество узлов не в сети.|
|RebootingNodeCount|Rebooting Node Count|Count|Всего|Количество перезапускаемых узлов.|
|ReimagingNodeCount|Reimaging Node Count|Count|Всего|Число узлов, для которых пересоздается образ.|
|RunningNodeCount|Running Node Count|Count|Всего|Число работающих узлов.|
|LeavingPoolNodeCount|Leaving Pool Node Count|Count|Всего|Количество узлов, покидающих пул.|
|UnusableNodeCount|Unusable Node Count|Count|Всего|Число узлов, непригодных для использования.|
|PreemptedNodeCount|Preempted Node Count|Count|Всего|Количество замещенных узлов.|
|TaskStartEvent|Task Start Events|Count|Всего|Общее число запущенных задач.|
|TaskCompleteEvent|Task Complete Events|Count|Всего|Общее число завершенных задач.|
|TaskFailEvent|Task Fail Events|Count|Всего|Общее число задач, завершившихся сбоем.|
|PoolCreateEvent|Pool Create Events|Count|Всего|Общее число созданных пулов.|
|PoolResizeStartEvent|Pool Resize Start Events|Count|Всего|Общее число запущенных задач изменения размера пула.|
|PoolResizeCompleteEvent|Pool Resize Complete Events|Count|Всего|Общее число завершенных задач изменения размера пула.|
|PoolDeleteStartEvent|Pool Delete Start Events|Count|Всего|Общее число запущенных задач удаления пула.|
|PoolDeleteCompleteEvent|Pool Delete Complete Events|Count|Всего|Общее число завершенных задач удаления пула.|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|connectedclients|Подключенные клиенты|Count|Максимальная||
|totalcommandsprocessed|Всего операций|Count|Всего||
|cachehits|Попаданий в кэш|Count|Всего||
|cachemisses|Промахов в кэше|Count|Всего||
|getcommands|Операций считывания|Count|Всего||
|setcommands|Операций записи|Count|Всего||
|evictedkeys|Исключенные ключи|Count|Всего||
|totalkeys|Всего ключей|Count|Максимальная||
|expiredkeys|Ключи с истекшим сроком действия|Count|Всего||
|usedmemory|Используемая память|Байты|Максимальная||
|usedmemoryRss|RSS используемой памяти|Байты|Максимальная||
|serverLoad|Загрузка сервера|Процент|Максимальная||
|cacheWrite|Запись в кэш|Байт/с|Максимальная||
|cacheRead|Чтение из кэша|Байт/с|Максимальная||
|percentProcessorTime|ЦП|Процент|Максимальная||
|connectedclients0|Connected Clients (Shard 0)|Count|Максимальная||
|totalcommandsprocessed0|Total Operations (Shard 0)|Count|Всего||
|cachehits0|Cache Hits (Shard 0)|Count|Всего||
|cachemisses0|Cache Misses (Shard 0)|Count|Всего||
|getcommands0|Gets (Shard 0)|Count|Всего||
|setcommands0|Sets (Shard 0)|Count|Всего||
|evictedkeys0|Evicted Keys (Shard 0)|Count|Всего||
|totalkeys0|Total Keys (Shard 0)|Count|Максимальная||
|expiredkeys0|Expired Keys (Shard 0)|Count|Всего||
|usedmemory0|Used Memory (Shard 0)|Байты|Максимальная||
|usedmemoryRss0|Used Memory RSS (Shard 0)|Байты|Максимальная||
|serverLoad0|Server Load (Shard 0)|Процент|Максимальная||
|cacheWrite0|Cache Write (Shard 0)|Байт/с|Максимальная||
|cacheRead0|Cache Read (Shard 0)|Байт/с|Максимальная||
|percentProcessorTime0|CPU (Shard 0)|Процент|Максимальная||
|connectedclients1|Connected Clients (Shard 1)|Count|Максимальная||
|totalcommandsprocessed1|Total Operations (Shard 1)|Count|Всего||
|cachehits1|Cache Hits (Shard 1)|Count|Всего||
|cachemisses1|Cache Misses (Shard 1)|Count|Всего||
|getcommands1|Gets (Shard 1)|Count|Всего||
|getcommands1|Sets (Shard 1)|Count|Всего||
|evictedkeys1|Evicted Keys (Shard 1)|Count|Всего||
|totalkeys1|Total Keys (Shard 1)|Count|Максимальная||
|expiredkeys1|Expired Keys (Shard 1)|Count|Всего||
|usedmemory1|Used Memory (Shard 1)|Байты|Максимальная||
|usedmemoryRss1|Used Memory RSS (Shard 1)|Байты|Максимальная||
|serverLoad1|Server Load (Shard 1)|Процент|Максимальная||
|cacheWrite1|Cache Write (Shard 1)|Байт/с|Максимальная||
|cacheRead1|Cache Read (Shard 1)|Байт/с|Максимальная||
|percentProcessorTime1|CPU (Shard 1)|Процент|Максимальная||
|connectedclients2|Connected Clients (Shard 2)|Count|Максимальная||
|totalcommandsprocessed2|Total Operations (Shard 2)|Count|Всего||
|cachehits2|Cache Hits (Shard 2)|Count|Всего||
|cachemisses2|Cache Misses (Shard 2)|Count|Всего||
|getcommands2|Gets (Shard 2)|Count|Всего||
|getcommands2|Sets (Shard 2)|Count|Всего||
|evictedkeys2|Evicted Keys (Shard 2)|Count|Всего||
|totalkeys2|Total Keys (Shard 2)|Count|Максимальная||
|expiredkeys2|Expired Keys (Shard 2)|Count|Всего||
|usedmemory2|Used Memory (Shard 2)|Байты|Максимальная||
|usedmemoryRss2|Used Memory RSS (Shard 2)|Байты|Максимальная||
|serverLoad2|Server Load (Shard 2)|Процент|Максимальная||
|cacheWrite2|Cache Write (Shard 2)|Байт/с|Максимальная||
|cacheRead2|Cache Read (Shard 2)|Байт/с|Максимальная||
|percentProcessorTime2|CPU (Shard 2)|Процент|Максимальная||
|connectedclients3|Connected Clients (Shard 3)|Count|Максимальная||
|totalcommandsprocessed3|Total Operations (Shard 3)|Count|Всего||
|cachehits3|Cache Hits (Shard 3)|Count|Всего||
|cachemisses3|Cache Misses (Shard 3)|Count|Всего||
|getcommands3|Gets (Shard 3)|Count|Всего||
|setcommands3|Sets (Shard 3)|Count|Всего||
|evictedkeys3|Evicted Keys (Shard 3)|Count|Всего||
|totalkeys3|Total Keys (Shard 3)|Count|Максимальная||
|expiredkeys3|Expired Keys (Shard 3)|Count|Всего||
|usedmemory3|Used Memory (Shard 3)|Байты|Максимальная||
|usedmemoryRss3|Used Memory RSS (Shard 3)|Байты|Максимальная||
|serverLoad3|Server Load (Shard 3)|Процент|Максимальная||
|cacheWrite3|Cache Write (Shard 3)|Байт/с|Максимальная||
|cacheRead3|Cache Read (Shard 3)|Байт/с|Максимальная||
|percentProcessorTime3|CPU (Shard 3)|Процент|Максимальная||
|connectedclients4|Connected Clients (Shard 4)|Count|Максимальная||
|totalcommandsprocessed4|Total Operations (Shard 4)|Count|Всего||
|cachehits4|Cache Hits (Shard 4)|Count|Всего||
|cachemisses4|Cache Misses (Shard 4)|Count|Всего||
|getcommands4|Gets (Shard 4)|Count|Всего||
|setcommands4|Sets (Shard 4)|Count|Всего||
|evictedkeys4|Evicted Keys (Shard 4)|Count|Всего||
|totalkeys4|Total Keys (Shard 4)|Count|Максимальная||
|expiredkeys4|Expired Keys (Shard 4)|Count|Всего||
|usedmemory4|Used Memory (Shard 4)|Байты|Максимальная||
|usedmemoryRss4|Used Memory RSS (Shard 4)|Байты|Максимальная||
|serverLoad4|Server Load (Shard 4)|Процент|Максимальная||
|cacheWrite4|Cache Write (Shard 4)|Байт/с|Максимальная||
|cacheRead4|Cache Read (Shard 4)|Байт/с|Максимальная||
|percentProcessorTime4|CPU (Shard 4)|Процент|Максимальная||
|connectedclients5|Connected Clients (Shard 5)|Count|Максимальная||
|totalcommandsprocessed5|Total Operations (Shard 5)|Count|Всего||
|cachehits5|Cache Hits (Shard 5)|Count|Всего||
|cachemisses5|Cache Misses (Shard 5)|Count|Всего||
|getcommands5|Gets (Shard 5)|Count|Всего||
|getcommands5|Sets (Shard 5)|Count|Всего||
|evictedkeys5|Evicted Keys (Shard 5)|Count|Всего||
|totalkeys5|Total Keys (Shard 5)|Count|Максимальная||
|expiredkeys5|Expired Keys (Shard 5)|Count|Всего||
|usedmemory5|Used Memory (Shard 5)|Байты|Максимальная||
|usedmemoryRss5|Used Memory RSS (Shard 5)|Байты|Максимальная||
|serverLoad5|Server Load (Shard 5)|Процент|Максимальная||
|cacheWrite5|Cache Write (Shard 5)|Байт/с|Максимальная||
|cacheRead5|Cache Read (Shard 5)|Байт/с|Максимальная||
|percentProcessorTime5|CPU (Shard 5)|Процент|Максимальная||
|connectedclients6|Connected Clients (Shard 6)|Count|Максимальная||
|totalcommandsprocessed6|Total Operations (Shard 6)|Count|Всего||
|cachehits6|Cache Hits (Shard 6)|Count|Всего||
|cachemisses6|Cache Misses (Shard 6)|Count|Всего||
|getcommands6|Gets (Shard 6)|Count|Всего||
|setcommands6|Sets (Shard 6)|Count|Всего||
|evictedkeys6|Evicted Keys (Shard 6)|Count|Всего||
|totalkeys6|Total Keys (Shard 6)|Count|Максимальная||
|expiredkeys6|Expired Keys (Shard 6)|Count|Всего||
|usedmemory6|Used Memory (Shard 6)|Байты|Максимальная||
|usedmemoryRss6|Used Memory RSS (Shard 6)|Байты|Максимальная||
|serverLoad6|Server Load (Shard 6)|Процент|Максимальная||
|cacheWrite6|Cache Write (Shard 6)|Байт/с|Максимальная||
|cacheRead6|Cache Read (Shard 6)|Байт/с|Максимальная||
|percentProcessorTime6|CPU (Shard 6)|Процент|Максимальная||
|connectedclients7|Connected Clients (Shard 7)|Count|Максимальная||
|totalcommandsprocessed7|Total Operations (Shard 7)|Count|Всего||
|cachehits7|Cache Hits (Shard 7)|Count|Всего||
|cachemisses7|Cache Misses (Shard 7)|Count|Всего||
|getcommands7|Gets (Shard 7)|Count|Всего||
|setcommands7|Sets (Shard 7)|Count|Всего||
|evictedkeys7|Evicted Keys (Shard 7)|Count|Всего||
|totalkeys7|Total Keys (Shard 7)|Count|Максимальная||
|expiredkeys7|Expired Keys (Shard 7)|Count|Всего||
|usedmemory7|Used Memory (Shard 7)|Байты|Максимальная||
|usedmemoryRss7|Used Memory RSS (Shard 7)|Байты|Максимальная||
|serverLoad7|Server Load (Shard 7)|Процент|Максимальная||
|cacheWrite7|Cache Write (Shard 7)|Байт/с|Максимальная||
|cacheRead7|Cache Read (Shard 7)|Байт/с|Максимальная||
|percentProcessorTime7|CPU (Shard 7)|Процент|Максимальная||
|connectedclients8|Connected Clients (Shard 8)|Count|Максимальная||
|totalcommandsprocessed8|Total Operations (Shard 8)|Count|Всего||
|cachehits8|Cache Hits (Shard 8)|Count|Всего||
|cachemisses8|Cache Misses (Shard 8)|Count|Всего||
|getcommands8|Gets (Shard 8)|Count|Всего||
|setcommands8|Sets (Shard 8)|Count|Всего||
|evictedkeys8|Evicted Keys (Shard 8)|Count|Всего||
|totalkeys8|Total Keys (Shard 8)|Count|Максимальная||
|expiredkeys8|Expired Keys (Shard 8)|Count|Всего||
|usedmemory8|Used Memory (Shard 8)|Байты|Максимальная||
|usedmemoryRss8|Used Memory RSS (Shard 8)|Байты|Максимальная||
|serverLoad8|Server Load (Shard 8)|Процент|Максимальная||
|cacheWrite8|Cache Write (Shard 8)|Байт/с|Максимальная||
|cacheRead8|Cache Read (Shard 8)|Байт/с|Максимальная||
|percentProcessorTime8|CPU (Shard 8)|Процент|Максимальная||
|connectedclients9|Connected Clients (Shard 9)|Count|Максимальная||
|totalcommandsprocessed9|Total Operations (Shard 9)|Count|Всего||
|cachehits9|Cache Hits (Shard 9)|Count|Всего||
|cachemisses9|Cache Misses (Shard 9)|Count|Всего||
|getcommands9|Gets (Shard 9)|Count|Всего||
|setcommands9|Sets (Shard 9)|Count|Всего||
|evictedkeys9|Evicted Keys (Shard 9)|Count|Всего||
|totalkeys9|Total Keys (Shard 9)|Count|Максимальная||
|expiredkeys9|Expired Keys (Shard 9)|Count|Всего||
|usedmemory9|Used Memory (Shard 9)|Байты|Максимальная||
|usedmemoryRss9|Used Memory RSS (Shard 9)|Байты|Максимальная||
|serverLoad9|Server Load (Shard 9)|Процент|Максимальная||
|cacheWrite9|Cache Write (Shard 9)|Байт/с|Максимальная||
|cacheRead9|Cache Read (Shard 9)|Байт/с|Максимальная||
|percentProcessorTime9|CPU (Shard 9)|Процент|Максимальная||

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classicСompute/virtualMachines

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Процент|Средняя|Процент выделенных вычислительных единиц, используемых в настоящее время виртуальными машинами.|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Всего|Количество байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Всего|Количество байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|
|Скорость чтения с диска (байт/с)|Скорость чтения с диска|Байт/с|Средняя|Среднее количество байтов, считанных с диска за период мониторинга.|
|Скорость записи на диск (байт/с)|Скорость записи на диск|Байт/с|Средняя|Среднее количество байтов, записанных на диск за период мониторинга.|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Средняя|Количество операций вода-вывода в секунду при чтении с диска.|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Средняя|Количество операций вода-вывода в секунду при записи на диск.|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|TotalCalls|Total Calls|Count|Всего|Общее число вызовов.|
|SuccessfulCalls|Successful Calls|Count|Всего|Число успешных вызовов.|
|TotalErrors|Total Errors|Count|Всего|Общее число вызовов, вернувших сообщение об ошибке (код ответа HTTP 4xx или 5xx).|
|BlockedCalls|Blocked Calls|Count|Всего|Число вызовов, превысивших ограничение скорости или квоты.|
|ServerErrors|Server Errors|Count|Всего|Число вызовов, приведших к внутренней ошибке сервера (код ответа HTTP 5xx).|
|ClientErrors|Client Errors|Count|Всего|Число вызовов, приведших к ошибке на стороне клиента (код ответа HTTP 4xx).|
|DataIn|Входящие данные:|Байты|Всего|Размер входящих данных в байтах.|
|DataOut|Выходные данные|Байты|Всего|Размер исходящих данных в байтах.|
|Задержка|Задержка|MilliSeconds|Средняя|Время задержки в миллисекундах.|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Процент|Средняя|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Всего|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Всего|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Всего|Общее число байтов, считанных с диска за период мониторинга.|
|Скорость записи на диск|Скорость записи на диск|Байты|Всего|Общее число байтов, записанных на диск за период мониторинга.|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Средняя|Количество операций вода-вывода в секунду при чтении с диска.|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Средняя|Количество операций вода-вывода в секунду при записи на диск.|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets;

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Процент|Средняя|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Всего|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Всего|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Всего|Общее число байтов, считанных с диска за период мониторинга.|
|Скорость записи на диск|Скорость записи на диск|Байты|Всего|Общее число байтов, записанных на диск за период мониторинга.|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Средняя|Количество операций вода-вывода в секунду при чтении с диска.|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Средняя|Количество операций вода-вывода в секунду при записи на диск.|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Процент|Средняя|Процент выделенных вычислительных единиц, которые в настоящее время используются виртуальными машинами.|
|Сеть (входящий трафик)|Сеть (входящий трафик)|Байты|Всего|Число байтов, полученных через все сетевые интерфейсы виртуальных машин (входящий трафик).|
|Сеть (исходящий трафик)|Сеть (исходящий трафик)|Байты|Всего|Число байтов, переданных из всех сетевых интерфейсов виртуальных машин (исходящий трафик).|
|Скорость чтения с диска|Скорость чтения с диска|Байты|Всего|Общее число байтов, считанных с диска за период мониторинга.|
|Скорость записи на диск|Скорость записи на диск|Байты|Всего|Общее число байтов, записанных на диск за период мониторинга.|
|Disk Read Operations/Sec|Disk Read Operations/Sec|Число/с|Средняя|Количество операций вода-вывода в секунду при чтении с диска.|
|Disk Write Operations/Sec|Disk Write Operations/Sec|Число/с|Средняя|Количество операций вода-вывода в секунду при записи на диск.|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|DCIApiCalls|Customer Insights API Calls|Count|Всего||
|DCIMappingImportOperationSuccessfulLines|Mapping Import Operation Successful Lines|Count|Всего||
|DCIMappingImportOperationFailedLines|Mapping Import Operation Failed Lines|Count|Всего||
|DCIMappingImportOperationTotalLines|Mapping Import Operation Total Lines|Count|Всего||
|DCIMappingImportOperationRuntimeInSeconds|Mapping Import Operation Runtime In Seconds|Секунды|Всего||
|DCIOutboundProfileExportSucceeded|Outbound Profile Export Succeeded|Count|Всего||
|DCIOutboundProfileExportFailed|Outbound Profile Export Failed|Count|Всего||
|DCIOutboundProfileExportDuration|Outbound Profile Export Duration|Секунды|Всего||
|DCIOutboundKpiExportSucceeded|Outbound Kpi Export Succeeded|Count|Всего||
|DCIOutboundKpiExportFailed|Outbound Kpi Export Failed|Count|Всего||
|DCIOutboundKpiExportDuration|Outbound Kpi Export Duration|Секунды|Всего||
|DCIOutboundKpiExportStarted|Outbound Kpi Export Started|Секунды|Всего||
|DCIOutboundKpiRecordCount|Outbound Kpi Record Count|Секунды|Всего||
|DCIOutboundProfileExportCount|Outbound Profile Export Count|Секунды|Всего||
|DCIOutboundInitialProfileExportFailed|Outbound Initial Profile Export Failed|Секунды|Всего||
|DCIOutboundInitialProfileExportSucceeded|Outbound Initial Profile Export Succeeded|Секунды|Всего||
|DCIOutboundInitialKpiExportFailed|Outbound Initial Kpi Export Failed|Секунды|Всего||
|DCIOutboundInitialKpiExportSucceeded|Outbound Initial Kpi Export Succeeded|Секунды|Всего||
|DCIOutboundInitialProfileExportDurationInSeconds|Outbound Initial Profile Export Duration In Seconds|Секунды|Всего||
|AdlaJobForStandardKpiFailed|Adla Job For Standard Kpi Failed In Seconds|Секунды|Всего||
|AdlaJobForStandardKpiTimeOut|Adla Job For Standard Kpi TimeOut In Seconds|Секунды|Всего||
|AdlaJobForStandardKpiCompleted|Adla Job For Standard Kpi Completed In Seconds|Секунды|Всего||
|ImportASAValuesFailed|Import ASA Values Failed Count|Count|Всего||
|ImportASAValuesSucceeded|Import ASA Values Succeeded Count|Count|Всего||

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|JobEndedSuccess|Successful Jobs|Count|Всего|Количество успешно выполненных заданий.|
|JobEndedFailure|Failed Jobs|Count|Всего|Количество невыполненных заданий.|
|JobEndedCancelled|Cancelled Jobs|Count|Всего|Количество отмененных заданий.|
|JobAUEndedSuccess|Successful AU Time|Секунды|Всего|Общее время AU успешно выполненных заданий.|
|JobAUEndedFailure|Failed AU Time|Секунды|Всего|Общее время AU невыполненных заданий.|
|JobAUEndedCancelled|Cancelled AU Time|Секунды|Всего|Общее время AU отмененных заданий.|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|TotalStorage|Общий объем хранилища|Байты|Максимальная|Общий объем данных, хранимых в учетной записи.|
|DataWritten|Записанные данные|Байты|Всего|Общий объем данных, записанных в учетной записи.|
|DataRead|Данных прочитано|Байты|Всего|Общий объем данных, прочитанных в учетной записи.|
|WriteRequests|Запросы на запись|Count|Всего|Количество запросов на запись данных в учетную запись.|
|ReadRequests|Запросы на чтение|Count|Всего|Количество запросов на чтение данных для учетной записи.|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Средняя|Нагрузка ЦП|
|compute_limit|Ограничение на единицы вычислений|Count|Средняя|Ограничение на единицы вычислений|
|compute_consumption_percent|Процент использования единиц вычислений|Процент|Средняя|Процент использования единиц вычислений|
|memory_percent|Процент памяти|Процент|Средняя|Процент памяти|
|io_consumption_percent|Процент ввода-вывода данных|Процент|Средняя|Процент ввода-вывода данных|
|storage_percent|Storage percentage|Процент|Средняя|Storage percentage|
|storage_used|Storage used|Байты|Средняя|Storage used|
|storage_limit|Storage limit|Байты|Средняя|Storage limit|
|active_connections|Общее количество активных подключений|Count|Средняя|Общее количество активных подключений|
|connections_failed|Общее количество неудачных подключений|Count|Средняя|Общее количество неудачных подключений|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|cpu_percent|Нагрузка ЦП|Процент|Средняя|Нагрузка ЦП|
|compute_limit|Ограничение на единицы вычислений|Count|Средняя|Ограничение на единицы вычислений|
|compute_consumption_percent|Процент использования единиц вычислений|Процент|Средняя|Процент использования единиц вычислений|
|memory_percent|Процент памяти|Процент|Средняя|Процент памяти|
|io_consumption_percent|Процент ввода-вывода данных|Процент|Средняя|Процент ввода-вывода данных|
|storage_percent|Storage percentage|Процент|Средняя|Storage percentage|
|storage_used|Storage used|Байты|Средняя|Storage used|
|storage_limit|Storage limit|Байты|Средняя|Storage limit|
|active_connections|Общее количество активных подключений|Count|Средняя|Общее количество активных подключений|
|connections_failed|Общее количество неудачных подключений|Count|Средняя|Общее количество неудачных подключений|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Число предпринятых попыток отправки сообщений телеметрии.|Count|Всего|Число предпринятых попыток отправки в Центр Интернета вещей сообщений телеметрии из устройства в облако.|
|d2c.telemetry.ingress.success|Число отправленных сообщений телеметрии.|Count|Всего|Число успешно отправленных в Центр Интернета вещей сообщений телеметрии из устройства в облако.|
|c2d.commands.egress.complete.success|Завершенные команды.|Count|Всего|Число успешно завершенных устройством команд, отправленных из облака.|
|c2d.commands.egress.abandon.success|Прерванные команды.|Count|Всего|Число прерванных устройством команд, отправленных из облака.|
|c2d.commands.egress.reject.success|Отклоненные команды.|Count|Всего|Число отмененных устройством команд, отправленных из облака.|
|devices.totalDevices|Общее число устройств.|Count|Всего|Число устройств, зарегистрированных в Центре Интернета вещей.|
|devices.connectedDevices.allProtocol|Подключенные устройства|Count|Всего|Число устройств, подключенных к Центру Интернета вещей.|
|d2c.telemetry.egress.success|Telemetry messages delivered|Count|Всего|Общее число сообщений, успешно записанных в конечные точки.|
|d2c.telemetry.egress.dropped|Удаленные сообщения.|Count|Всего|Число сообщений, отброшенных из-за неработоспособности конечной точки доставки|
|d2c.telemetry.egress.orphaned|Потерянные сообщения.|Count|Всего|Число сообщений, не соответствующих какому-либо маршруту, включая резервный.|
|d2c.telemetry.egress.invalid|Недопустимые сообщения.|Count|Всего|Число сообщений, которые не были доставлены из-за несовместимости с конечной точкой.|
|d2c.telemetry.egress.fallback|Сообщения, соответствующие условиям для резервной конечной точки.|Count|Всего|Число сообщений, записанных в резервную конечную точку.|
|d2c.endpoints.egress.eventHubs|Сообщения, доставленные в конечные точки концентратора событий.|Count|Всего|Число успешных записей сообщений в конечные точки концентратора событий.|
|d2c.endpoints.latency.eventHubs|Message latency for Event Hub endpoints|Миллисекунды|Средняя|Средняя задержка в миллисекундах между поступлением сообщения в Центр Интернета вещей и его поступлением на конечную точку концентратора событий.|
|d2c.endpoints.egress.serviceBusQueues|Сообщения, доставленные в конечные точки очереди служебной шины.|Count|Всего|Число успешных записей сообщений в конечные точки очереди служебной шины.|
|d2c.endpoints.latency.serviceBusQueues|Message latency for Service Bus Queue endpoints|Миллисекунды|Средняя|Средняя задержка в миллисекундах между поступлением сообщения в Центр Интернета вещей и его поступлением на конечную точку очереди служебной шины.|
|d2c.endpoints.egress.serviceBusTopics|Сообщения, доставленные в конечные точки раздела служебной шины.|Count|Всего|Число успешных записей сообщений в конечные точки раздела служебной шины.|
|d2c.endpoints.latency.serviceBusTopics|Message latency for Service Bus Topic endpoints|Миллисекунды|Средняя|Средняя задержка в миллисекундах между поступлением сообщения в Центр Интернета вещей и его поступлением на конечную точку раздела служебной шины.|
|d2c.endpoints.egress.builtIn.events|Сообщения, доставленные во встроенную конечную точку (сообщения и события).|Count|Всего|Число успешных записей сообщений во встроенную конечную точку (сообщения и события).|
|d2c.endpoints.latency.builtIn.events|Задержка сообщения для встроенной конечной точки (сообщения и события).|Миллисекунды|Средняя|Средняя задержка в миллисекундах между поступлением сообщения в Центр Интернета вещей и его поступлением на встроенную конечную точку (сообщения и события). |
|d2c.Endpoints.egress.Storage|Сообщения, доставленные в конечные точки хранилища|Count|Всего|Общее число сообщений, успешно записанных в конечные точки хранилища|
|d2c.Endpoints.latency.Storage|Задержка сообщений для конечных точек хранилища|Миллисекунды|Средняя|Средняя задержка между поступлением сообщения в Центр Интернета вещей и в конечную точку хранилища в миллисекундах|
|d2c.endpoints.egress.storage.bytes|Данные, записанные в хранилище|Байты|Всего|Объем данных, записанных в конечные точки хранилища (в байтах)|
|d2c.Endpoints.egress.Storage.BLOBs|Большие двоичные объекты, записанные в хранилище|Count|Всего|Количество больших двоичных объектов, записанных в конечные точки хранилища|
|d2c.twin.read.success|Успешные операции чтения с двойников, инициированные устройством.|Count|Всего|Число всех успешных операций чтения с двойников, инициированных устройством.|
|d2c.twin.read.failure|Неудачные операции чтения с двойников, инициированные устройством.|Count|Всего|Число всех неудачных операций чтения с двойников, инициированных устройством.|
|d2c.twin.read.size|Размер ответа операций чтения с двойников, инициированных устройством.|Байты|Средняя|Среднее, минимальное и максимальное значение всех успешных операций чтения, инициированных устройством.|
|d2c.twin.update.success|Успешные обновления двойников, инициированные устройством.|Count|Всего|Число всех успешных обновлений двойников, инициированных устройством.|
|d2c.twin.update.failure|Неудачные обновления двойников, инициированные устройством.|Count|Всего|Число всех неудачных обновлений двойников, инициированных устройством.|
|d2c.twin.update.size|Размер обновлений двойников, инициированных устройством.|Байты|Средняя|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных устройством.|
|c2d.methods.success|Успешные вызовы прямых методов.|Count|Всего|Число всех успешных вызовов прямых методов.|
|c2d.methods.failure|Неудачные вызовы прямых методов.|Count|Всего|Число всех неудачных вызовов прямых методов.|
|c2d.methods.requestSize|Размер запроса вызовов прямых методов.|Байты|Средняя|Среднее, минимальное и максимальное значение всех успешных запросов прямых методов.|
|c2d.methods.responseSize|Размер ответа вызовов прямых методов.|Байты|Средняя|Среднее, минимальное и максимальное значения всех успешных ответов прямых методов.|
|c2d.twin.read.success|Успешные операции чтения с двойников, инициированные из серверной части.|Count|Всего|Число всех успешных операций чтения с двойников, инициированных из серверной части.|
|c2d.twin.read.failure|Неудачные операции чтения с двойников, инициированные из серверной части.|Count|Всего|Число всех неудачных операций чтения с двойников, инициированных из серверной части.|
|c2d.twin.read.size|Размер ответа операций чтения с двойников, инициированных из серверной части.|Байты|Средняя|Среднее, минимальное и максимальное значения всех успешных операций чтения с двойников, инициированных из серверной части.|
|c2d.twin.update.success|Успешные обновления двойников, инициированные из серверной части.|Count|Всего|Число всех успешных обновлений двойников, инициированных из серверной части.|
|c2d.twin.update.failure|Неудачные обновления двойников, инициированные из серверной части.|Count|Всего|Число всех неудачных обновлений двойников, инициированных из серверной части.|
|c2d.twin.update.size|Размер обновлений двойников, инициированных из серверной части.|Байты|Средняя|Средний, минимальный и максимальный размеры всех успешных обновлений двойников, инициированных из серверной части.|
|twinQueries.success|Успешные запросы двойников.|Count|Всего|Число всех успешных запросов двойников.|
|twinQueries.failure|Неудачные запросы двойников.|Count|Всего|Количество всех неудачных запросов двойников.|
|twinQueries.resultSize|Размер результатов запросов двойников.|Байты|Средняя|Среднее, минимальное и максимальное значения размера результатов всех успешных запросов двойников.|
|jobs.createTwinUpdateJob.success|Успешные операции создания заданий обновления двойников.|Count|Всего|Количество всех успешных созданий заданий обновления двойников.|
|jobs.createTwinUpdateJob.failure|Неудачные операции создания заданий обновления двойников.|Count|Всего|Количество всех неудачных операций создания заданий обновления двойников.|
|jobs.createDirectMethodJob.success|Успешные операции создания заданий вызова методов.|Count|Всего|Количество всех успешных операций создания заданий вызова прямых методов.|
|jobs.createDirectMethodJob.failure|Неудачные операции создания заданий вызова методов.|Count|Всего|Количество всех неудачных операций создания заданий вызова прямых методов.|
|jobs.listJobs.success|Успешные вызовы получения списка заданий.|Count|Всего|Количество всех успешных вызовов для получения списка заданий.|
|jobs.listJobs.failure|Неудачные вызовы получения списка заданий.|Count|Всего|Количество всех неудачных вызовов для получения списка заданий.|
|jobs.cancelJob.success|Успешные отмены заданий.|Count|Всего|Количество всех успешных вызовов для отмены заданий.|
|jobs.cancelJob.failure|Неудачные отмены заданий.|Count|Всего|Количество всех неудачных вызовов для отмены заданий.|
|jobs.queryJobs.success|Успешные запросы заданий.|Count|Всего|Количество всех успешных вызовов для запроса заданий.|
|jobs.queryJobs.failure|Неудачные запросы заданий.|Count|Всего|Количество всех неудачных вызовов для запроса заданий.|
|jobs.completed|Завершенные задания|Count|Всего|Количество всех выполненных заданий.|
|jobs.failed|Неудачные задания.|Count|Всего|Количество всех неудачных заданий.|
|d2c.telemetry.ingress.sendThrottle|Количество ошибок регулирования|Count|Всего|Количество ошибок регулирования из-за регулирования пропускной способности устройства|
|dailyMessageQuotaUsed|Общее количество используемых сообщений|Count|Средняя|Количество сообщений, использованных сегодня|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|INREQS|Входящие запросы отправки|Count|Всего|Общее количество входящих запросов отправки для концентратора уведомлений|
|SUCCREQ|Выполненные запросы|Count|Всего|Общее количество успешных запросов для пространства имен|
|FAILREQ|Невыполненные запросы|Count|Всего|Общее количество неудачных запросов для пространства имен|
|SVRBSY|Ошибки из-за занятости сервера|Count|Всего|Общее количество ошибок из-за занятости сервера для пространства имен|
|INTERR|Внутренние ошибки сервера|Count|Всего|Общее количество внутренних ошибок сервера для пространства имен|
|MISCERR|Другие ошибки|Count|Всего|Общее количество неудачных запросов для пространства имен|
|INMSGS|Входящие сообщения|Count|Всего|Общее количество входящих сообщений для пространства имен|
|OUTMSGS|Исходящие сообщения|Count|Всего|Общее количество исходящих сообщений для пространства имен|
|EHINMBS|Входящие байты|Байты|Всего|Пропускная способность входящих сообщений концентратора событий для пространства имен|
|EHOUTMBS|Исходящие байты|Байты|Всего|Общее количество исходящих сообщений для пространства имен|
|EHABL|Archive backlog messages|Count|Всего|Архивные сообщения концентратора событий в списке невыполненных работ для пространства имен|
|EHAMSGS|Archive messages|Count|Всего|Архивные сообщения концентратора событий в пространстве имен|
|EHAMBS|Archive message throughput|Байты|Всего|Пропускная способность архивных сообщений концентратора событий в пространстве имен|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|RunsStarted|Runs Started|Count|Всего|Число запущенных выполнений рабочего процесса.|
|RunsCompleted|Runs Completed|Count|Всего|Число завершенных выполнений рабочего процесса.|
|RunsSucceeded|Runs Succeeded|Count|Всего|Число успешно завершенных выполнений рабочего процесса.|
|RunsFailed|Runs Failed|Count|Всего|Число выполнений рабочего процесса, завершившихся сбоем.|
|RunsCancelled|Runs Cancelled|Count|Всего|Число отмененных выполнений рабочего процесса.|
|RunLatency|Run Latency|Секунды|Средняя|Задержка завершенных выполнений рабочего процесса.|
|RunSuccessLatency|Run Success Latency|Секунды|Средняя|Задержка успешно завершенных выполнений рабочего процесса.|
|RunThrottledEvents|Run Throttled Events|Count|Всего|Число событий регулирования действия или триггера рабочего процесса.|
|RunFailurePercentage|Run Failure Percentage|Процент|Всего|Процент выполнений рабочего процесса, завершившихся сбоем.|
|ActionsStarted|Actions Started |Count|Всего|Число запущенных действий рабочего процесса.|
|ActionsCompleted|Actions Completed |Count|Всего|Число завершенных действий рабочего процесса.|
|ActionsSucceeded|Actions Succeeded |Count|Всего|Число успешно выполненных действий рабочего процесса.|
|ActionsFailed|Actions Failed|Count|Всего|Число действий рабочего процесса, завершившихся сбоем.|
|ActionsSkipped|Actions Skipped |Count|Всего|Число пропущенных действий рабочего процесса.|
|ActionLatency|Action Latency |Секунды|Средняя|Задержка завершенных действий рабочего процесса.|
|ActionSuccessLatency|Action Success Latency |Секунды|Средняя|Задержка успешно выполненных действий рабочего процесса.|
|ActionThrottledEvents|Action Throttled Events|Count|Всего|Число событий регулирования действия рабочего процесса.|
|TriggersStarted|Triggers Started |Count|Всего|Число запущенных триггеров рабочего процесса.|
|TriggersCompleted|Triggers Completed |Count|Всего|Число завершенных триггеров рабочего процесса.|
|TriggersSucceeded|Triggers Succeeded |Count|Всего|Число успешно выполненных триггеров рабочего процесса.|
|TriggersFailed|Triggers Failed |Count|Всего|Число триггеров рабочего процесса, завершившихся со сбоем.|
|TriggersSkipped|Triggers Skipped|Count|Всего|Число пропущенных триггеров рабочего процесса.|
|TriggersFired|Triggers Fired |Count|Всего|Число активированных триггеров рабочего процесса.|
|TriggerLatency|Trigger Latency |Секунды|Средняя|Задержка завершенных триггеров рабочего процесса.|
|TriggerFireLatency|Trigger Fire Latency |Секунды|Средняя|Задержка активированных триггеров рабочего процесса.|
|TriggerSuccessLatency|Trigger Success Latency |Секунды|Средняя|Задержка успешно выполненных триггеров рабочего процесса.|
|TriggerThrottledEvents|Trigger Throttled Events|Count|Всего|Число событий регулирования триггера рабочего процесса.|
|BillableActionExecutions|Billable Action Executions|Count|Всего|Число выполненных действий рабочего процесса, за которые выставляется счет.|
|BillableTriggerExecutions|Billable Trigger Executions|Count|Всего|Число выполненных триггеров рабочего процесса, за которые выставляется счет.|
|TotalBillableExecutions|Total Billable Executions|Count|Всего|Число выполнений рабочего процесса, за которые выставляется счет.|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|Пропускная способность|Пропускная способность|Байт/с|Средняя|Количество байтов в секунду, обрабатываемых шлюзом приложений|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|BytesIn|BytesIn|Count|Всего|Входящий трафик (в байтах) Azure|
|BytesOut|BytesOut|Count|Всего|Исходящий трафик (в байтах) Azure|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|registration.all|Операции регистрации|Count|Всего|Число всех успешных операций регистрации (создание, запрос, обновление и удаление). |
|registration.create|Операции создания регистрации|Count|Всего|Число всех успешных операций создания регистраций.|
|registration.update|Операции обновления регистрации|Count|Всего|Число всех успешных операций обновления регистраций.|
|registration.get|Операции чтения регистрации|Count|Всего|Число всех успешных запросов регистрации.|
|registration.delete|Операции удаления регистрации|Count|Всего|Число всех успешных операций удаления регистраций.|
|Входящий трафик|Входящие сообщения|Count|Всего|Число всех успешно отправленных вызовов API. |
|incoming.scheduled|Отправлено запланированных push-уведомлений|Count|Всего|Отменено запланированных push-уведомлений|
|incoming.scheduled.cancel|Отменено запланированных push-уведомлений|Count|Всего|Отменено запланированных push-уведомлений|
|scheduled.pending|Запланированных уведомлений в состоянии ожидания|Count|Всего|Запланированных уведомлений в состоянии ожидания|
|installation.all|Операции управления установкой|Count|Всего|Операции управления установкой|
|installation.get|Операции получения установки|Count|Всего|Операции получения установки|
|installation.upsert|Операции создания или обновления установки|Count|Всего|Операции создания или обновления установки|
|installation.patch|Операции исправления установки|Count|Всего|Операции исправления установки|
|installation.delete|Операции удаления установки|Count|Всего|Операции удаления установки|
|outgoing.allpns.success|Успешные уведомления|Count|Всего|Общее число успешных уведомлений.|
|outgoing.allpns.invalidpayload|Ошибки полезных данных|Count|Всего|Количество неудачных отправлений из-за того, что PNS вернула ошибку полезных данных.|
|outgoing.allpns.pnserror|Ошибки внешней системы уведомлений|Count|Всего|Количество неудачных отправлений из-за ошибки связи с PNS (за исключением проблем с проверкой подлинности).|
|outgoing.allpns.channelerror|Ошибки канала|Count|Всего|Количество неудачных отправлений из-за недопустимого канала, не связанного с соответствующим регулируемым или просроченным приложением.|
|outgoing.allpns.badorexpiredchannel|Ошибки из-за поврежденного или просроченного канала|Count|Всего|Количество неудачных отправлений из-за недопустимого или просроченного канала, маркера или идентификатора регистрации в регистрации.|
|outgoing.wns.success|Успешные уведомления WNS|Count|Всего|Общее число успешных уведомлений.|
|outgoing.wns.invalidcredentials|Ошибки авторизации WNS (недопустимые учетные данные)|Count|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных. (Windows Live не распознает учетные данные.)|
|outgoing.wns.badchannel|Ошибка из-за поврежденного канала WNS|Count|Всего|Количество неудачных отправлений из-за ошибки распознавания универсального кода ресурса (URI) канала в регистрации (состояние WNS: 404 — не найдено).|
|outgoing.wns.expiredchannel|Ошибка из-за просроченного канала WNS|Count|Всего|Количество неудачных отправлений из-за просроченного универсального кода ресурса (URI) канала (состояние WNS: 410 — потеряно).|
|outgoing.wns.throttled|Регулируемые уведомления WNS|Count|Всего|Количество неудачных отправлений из-за регулирования этого приложения с помощью WNS (состояние WNS: 406 — неприемлемо).|
|outgoing.wns.tokenproviderunreachable|Ошибки авторизации WNS (нет доступа)|Count|Всего|Windows Live недоступна.|
|outgoing.wns.invalidtoken|Ошибки авторизации WNS (недопустимый маркер)|Count|Всего|WNS предоставлен недопустимый маркер (состояние WNS: 401 — не санкционировано).|
|outgoing.wns.wrongtoken|Ошибки авторизации WNS (неправильный маркер)|Count|Всего|Маркер, предоставленный для WNS, допустимый, но он предназначен для другого приложения (состояние WNS: 403 — запрещено). Это возможно, если универсальный код ресурса (URI) канала в регистрации связан с другим приложением. Убедитесь, что клиентское приложение связано с приложением, учетные данные которого находятся в центре уведомлений.|
|outgoing.wns.invalidnotificationformat|Недопустимый формат уведомления WNS|Count|Всего|Недопустимый формат уведомления (состояние WNS: 400). Обратите внимание, что WNS не отклоняет все недопустимые полезные данные.|
|outgoing.wns.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления WNS|Count|Всего|Полезные данные уведомления слишком большие (состояние WNS: 413).|
|outgoing.wns.channelthrottled|Канал WNS регулируется|Count|Всего|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (заголовок ответа WNS: состояние уведомления X-WNS: канал регулируется).|
|outgoing.wns.channeldisconnected|Канал WNS отсоединен|Count|Всего|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (заголовок ответа WNS: состояние подключения устройства X-WNS: отключено).|
|outgoing.wns.dropped|Пропущенные уведомления WNS|Count|Всего|Уведомление было удалено из-за регулирования универсального кода ресурса (URI) канала в регистрации (состояние уведомления X-WNS: пропущено. Состояние подключения устройства не X-WNS: отключено).|
|outgoing.wns.pnserror|Ошибки WNS|Count|Всего|Уведомление не доставлено из-за ошибок связи с WNS.|
|outgoing.wns.authenticationerror|Ошибки проверки подлинности WNS|Count|Всего|Уведомление не доставлено из-за ошибок связи Windows Live с использованием недопустимых учетных данных или неправильного маркера.|
|outgoing.apns.success|Успешные уведомления APNS|Count|Всего|Общее число успешных уведомлений.|
|outgoing.apns.invalidcredentials|Ошибки авторизации APNS|Count|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|
|outgoing.apns.badchannel|Ошибка из-за поврежденного канала APNS|Count|Всего|Количество неудачных отправлений из-за недопустимого маркера (код состояния APNS: 8).|
|outgoing.apns.expiredchannel|Ошибка из-за просроченного канала APNS|Count|Всего|Количество маркеров, которые были аннулированы каналом обратной связи APNS.|
|outgoing.apns.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления APNS|Count|Всего|Количество неудачных отправлений из-за большого объема полезных данных (код состояния APNS: 7).|
|outgoing.apns.pnserror|Ошибки APNS|Count|Всего|Количество неудачных отправлений из-за ошибок связи с APNS.|
|outgoing.gcm.success|Успешные уведомления GCM|Count|Всего|Общее число успешных уведомлений.|
|outgoing.gcm.invalidcredentials|Ошибки авторизации GCM (недопустимые учетные данные)|Count|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|
|outgoing.gcm.badchannel|Ошибка из-за поврежденного канала GCM|Count|Всего|Количество неудачных отправлений из-за ошибки распознавания идентификатора регистрации в регистрации (результат GCM: недействительная регистрация).|
|outgoing.gcm.expiredchannel|Ошибка из-за просроченного канала GCM|Count|Всего|Количество неудачных отправлений из-за просроченного идентификатора регистрации в регистрации (результат GCM: не зарегистрировано).|
|outgoing.gcm.throttled|Регулируемые уведомления GCM|Count|Всего|Количество неудачных отправлений из-за регулирования этого приложения GCM (код состояния GCM: 501–599; либо результат: недоступен).|
|outgoing.gcm.invalidnotificationformat|Недопустимый формат уведомления GCM|Count|Всего|Количество неудачных отправлений из-за неправильного формата полезных данных (результат GCM: недопустимый ключ данных или недопустимый TTL).|
|outgoing.gcm.invalidnotificationsize|Ошибка из-за недопустимого размера уведомления GCM|Count|Всего|Количество неудачных отправлений из-за большого объема полезных данных (результат GCM: слишком большое сообщение).|
|outgoing.gcm.wrongchannel|Ошибка из-за неправильного канала GCM|Count|Всего|Количество неудачных отправлений из-за того, что идентификатор регистрации в регистрации не связан с текущим приложением (результат GCM: недопустимое имя пакета).|
|outgoing.gcm.pnserror|Ошибки GCM|Count|Всего|Количество неудачных отправлений из-за ошибок связи с GCM.|
|outgoing.gcm.authenticationerror|Ошибки проверки подлинности GCM|Count|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных по причине использования блокированных учетных данных или неправильно настроенного идентификатора отправителя в приложении (результат GCM: несовпадающий код отправителя).|
|outgoing.mpns.success|Успешные уведомления MPNS|Count|Всего|Общее число успешных уведомлений.|
|outgoing.mpns.invalidcredentials|Недопустимые учетные данные MPNS|Count|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|
|outgoing.mpns.badchannel|Ошибка из-за поврежденного канала MPNS|Count|Всего|Количество неудачных отправлений из-за ошибки распознавания универсального кода ресурса (URI) канала в регистрации (состояние MPNS: 404 — не найдено).|
|outgoing.mpns.throttled|Регулируемые уведомления MPNS|Count|Всего|Количество неудачных отправлений из-за регулирования этого приложения MPNS (WNS MPNS: 406 — неприемлемо).|
|outgoing.mpns.invalidnotificationformat|Недопустимый формат уведомления MPNS|Count|Всего|Количество неудачных отправлений из-за большого объема полезных данных уведомления.|
|outgoing.mpns.channeldisconnected|Канал MPNS отсоединен|Count|Всего|Количество неудачных отправлений из-за отсоединения универсального кода ресурса (URI) канала в регистрации (состояние MPNS: 412 — не найдено).|
|outgoing.mpns.dropped|Пропущенные уведомления MPNS|Count|Всего|Количество отправлений, пропущенных MPNS (заголовок ответа MPNS: состояние уведомления X — очередь переполнена или подавлено).|
|outgoing.mpns.pnserror|Ошибки MPNS|Count|Всего|Количество неудачных отправлений из-за ошибок связи с MPNS.|
|outgoing.mpns.authenticationerror|Ошибки проверки подлинности MPNS|Count|Всего|Количество неудачных отправлений из-за непринятия PNS предоставленных учетных данных или по причине использования блокированных учетных данных.|
|notificationhub.pushes|Все исходящие уведомления|Count|Всего|Все исходящие уведомления из центра уведомлений.|
|incoming.all.requests|Все входящие запросы|Count|Всего|Общее количество входящих запросов для концентратора уведомлений|
|Incoming.all.failedrequests|Все неудачные входящие запросы|Count|Всего|Общее количество неудачных входящих запросов для концентратора уведомлений|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Description (Описание)|
|---|---|---|---|---|
|SearchLatency|Search Latency|Секунды|Средняя|Среднее время задержки поиска для службы поиска.|
|SearchQueriesPerSecond|Search queries per second|Число/с|Средняя|Число поисковых запросов в секунду для службы поиска.|
|ThrottledSearchQueriesPercentage|Throttled search queries percentage|Процент|Средняя|Процент отрегулированных поисковых запросов для службы поиска.|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|CPUXNS|CPU usage per namespace|Процент|Максимальная|Метрика использования ЦП пространства имен служебной шины для премиум-обслуживания|
|WSXNS|Memory size usage per namespace|Процент|Максимальная|Метрика использования памяти пространства имен служебной шины для премиум-обслуживания|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|cpu_percent|Процент использования ЦП|Процент|Средняя|Процент использования ЦП|
|physical_data_read_percent|Процент операций ввода/вывода данных|Процент|Средняя|Процент операций ввода/вывода данных|
|log_write_percent|Log IO percentage|Процент|Средняя|Log IO percentage|
|dtu_consumption_percent|Процент использования DTU|Процент|Средняя|Процент использования DTU|
|storage|Total database size|Байты|Максимальная|Total database size|
|connection_successful|Успешные подключения|Count|Всего|Успешные подключения|
|connection_failed|Неудачные подключения|Count|Всего|Неудачные подключения|
|blocked_by_firewall|Заблокировано брандмауэром|Count|Всего|Заблокировано брандмауэром|
|deadlock|Взаимоблокировки|Count|Всего|Взаимоблокировки.|
|storage_percent|Размер базы данных в процентах|Процент|Максимальная|Размер базы данных в процентах|
|xtp_storage_percent|Процент хранилища выполняющейся в памяти OLTP|Процент|Средняя|Процент хранилища выполняющейся в памяти OLTP|
|workers_percent|Workers percentage|Процент|Средняя|Workers percentage|
|sessions_percent|Процент использования сеансов|Процент|Средняя|Процент использования сеансов|
|dtu_limit|Лимит DTU|Count|Средняя|Лимит DTU|
|dtu_used|DTU used|Count|Средняя|DTU used|
|dwu_limit|Лимит DWU|Count|Максимальная|Лимит DWU|
|dwu_consumption_percent|DWU percentage|Процент|Максимальная|DWU percentage|
|dwu_used|DWU used|Count|Максимальная|DWU used|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|cpu_percent|Процент использования ЦП|Процент|Средняя|Процент использования ЦП|
|database_cpu_percent|Процент использования ЦП|Процент|Средняя|Процент использования ЦП|
|physical_data_read_percent|Процент операций ввода/вывода данных|Процент|Средняя|Процент операций ввода/вывода данных|
|database_physical_data_read_percent|Процент операций ввода/вывода данных|Процент|Средняя|Процент операций ввода/вывода данных|
|log_write_percent|Log IO percentage|Процент|Средняя|Log IO percentage|
|database_log_write_percent|Log IO percentage|Процент|Средняя|Log IO percentage|
|dtu_consumption_percent|Процент использования DTU|Процент|Средняя|Процент использования DTU|
|database_dtu_consumption_percent|Процент использования DTU|Процент|Средняя|Процент использования DTU|
|storage_percent|Storage percentage|Процент|Средняя|Storage percentage|
|workers_percent|Workers percentage|Процент|Средняя|Workers percentage|
|database_workers_percent|Workers percentage|Процент|Средняя|Workers percentage|
|sessions_percent|Процент использования сеансов|Процент|Средняя|Процент использования сеансов|
|database_sessions_percent|Процент использования сеансов|Процент|Средняя|Процент использования сеансов|
|eDTU_limit|eDTU limit|Count|Средняя|eDTU limit|
|storage_limit|Storage limit|Байты|Средняя|Storage limit|
|eDTU_used|eDTU used|Count|Средняя|eDTU used|
|storage_used|Storage used|Байты|Средняя|Storage used|
|database_storage_used|Storage used|Байты|Средняя|Storage used|
|xtp_storage_percent|Процент хранилища выполняющейся в памяти OLTP|Процент|Средняя|Процент хранилища выполняющейся в памяти OLTP|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|dtu_consumption_percent|Процент использования DTU|Процент|Средняя|Процент использования DTU|
|database_dtu_consumption_percent|Процент использования DTU|Процент|Средняя|Процент использования DTU|
|storage_used|Storage used|Байты|Средняя|Storage used|
|database_storage_used|Storage used|Байты|Средняя|Storage used|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|ResourceUtilization|Использование единиц потоковой передачи (%)|Процент|Максимальная|Использование единиц потоковой передачи (%)|
|InputEvents|Входные события|Count|Всего|Входные события|
|InputEventBytes|Байты входного события|Байты|Всего|Байты входного события|
|LateInputEvents|События позднего ввода|Count|Всего|События позднего ввода|
|OutputEvents|Выходные события|Count|Всего|Выходные события|
|ConversionErrors|Ошибки преобразования данных|Count|Всего|Ошибки преобразования данных|
|Ошибки|Ошибки среды выполнения|Count|Всего|Ошибки среды выполнения|
|DroppedOrAdjustedEvents|Неупорядоченные события|Count|Всего|Неупорядоченные события|
|AMLCalloutRequests|Запросы функций|Count|Всего|Запросы функций|
|AMLCalloutFailedRequests|Неудачные запросы функций|Count|Всего|Неудачные запросы функций|
|AMLCalloutInputEvents|События функций|Count|Всего|События функций|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|Процент загруженности ЦП|Процент использования ЦП|Процент|Средняя|Процент использования ЦП|
|Процент использования памяти|Процент использования памяти|Процент|Средняя|Процент использования памяти|
|Длина очереди диска|Длина дисковой очереди|Count|Всего|Длина дисковой очереди|
|Длина очереди HTTP|Длина очереди HTTP:|Count|Всего|Длина очереди HTTP:|
|Получено байт|Входящие данные:|Байты|Всего|Входящие данные:|
|Передано байт|Выходные данные|Байты|Всего|Выходные данные|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (за исключением функций)

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|CpuTime|Время ЦП:|Секунды|Всего|Время ЦП:|
|Запросы|Запросы|Count|Всего|Запросы|
|Получено байт|Входящие данные:|Байты|Всего|Входящие данные:|
|Передано байт|Выходные данные|Байты|Всего|Выходные данные|
|Http101|Http 101|Count|Всего|Http 101|
|Http2xx|HTTP 2xx|Count|Всего|HTTP 2xx|
|Http3xx|HTTP 3xx:|Count|Всего|HTTP 3xx:|
|Http401|HTTP 401:|Count|Всего|HTTP 401:|
|Http403|HTTP 403:|Count|Всего|HTTP 403:|
|Http404|HTTP 404:|Count|Всего|HTTP 404:|
|Http406|HTTP 406:|Count|Всего|HTTP 406:|
|Http4xx|HTTP 4xx:|Count|Всего|HTTP 4xx:|
|Http5xx|Ошибки HTTP-сервера:|Count|Всего|Ошибки HTTP-сервера:|
|MemoryWorkingSet|рабочий набор памяти;|Байты|Средняя|рабочий набор памяти;|
|AverageMemoryWorkingSet|средний размер рабочего набора памяти;|Байты|Средняя|средний размер рабочего набора памяти;|
|AverageResponseTime|Среднее время ответа:|Секунды|Средняя|Среднее время ответа:|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (функции)

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|Получено байт|Входящие данные:|Байты|Всего|Входящие данные:|
|Передано байт|Выходные данные|Байты|Всего|Выходные данные|
|Http5xx|Ошибки HTTP-сервера:|Count|Всего|Ошибки HTTP-сервера:|
|MemoryWorkingSet|рабочий набор памяти;|Байты|Средняя|рабочий набор памяти;|
|AverageMemoryWorkingSet|средний размер рабочего набора памяти;|Байты|Средняя|средний размер рабочего набора памяти;|
|FunctionExecutionUnits|Function Execution Units|Count|Средняя|Единицы выполнения функции.|
|FunctionExecutionCount|Function Execution Count|Count|Средняя|Число выполнений функции.|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|CpuTime|Время ЦП:|Секунды|Всего|Время ЦП:|
|Запросы|Запросы|Count|Всего|Запросы|
|Получено байт|Входящие данные:|Байты|Всего|Входящие данные:|
|Передано байт|Выходные данные|Байты|Всего|Выходные данные|
|Http101|Http 101|Count|Всего|Http 101|
|Http2xx|HTTP 2xx|Count|Всего|HTTP 2xx|
|Http3xx|HTTP 3xx:|Count|Всего|HTTP 3xx:|
|Http401|HTTP 401:|Count|Всего|HTTP 401:|
|Http403|HTTP 403:|Count|Всего|HTTP 403:|
|Http404|HTTP 404:|Count|Всего|HTTP 404:|
|Http406|HTTP 406:|Count|Всего|HTTP 406:|
|Http4xx|HTTP 4xx:|Count|Всего|HTTP 4xx:|
|Http5xx|Ошибки HTTP-сервера:|Count|Всего|Ошибки HTTP-сервера:|
|MemoryWorkingSet|рабочий набор памяти;|Байты|Средняя|рабочий набор памяти;|
|AverageMemoryWorkingSet|средний размер рабочего набора памяти;|Байты|Средняя|средний размер рабочего набора памяти;|
|AverageResponseTime|Среднее время ответа:|Секунды|Средняя|Среднее время ответа:|
|FunctionExecutionUnits|Function Execution Units|Count|Средняя|Единицы выполнения функции.|
|FunctionExecutionCount|Function Execution Count|Count|Средняя|Function Execution Count|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|Запросы|Запросы|Count|Всего|Запросы|
|Получено байт|Входящие данные:|Байты|Всего|Входящие данные:|
|Передано байт|Выходные данные|Байты|Всего|Выходные данные|
|Http101|Http 101|Count|Всего|Http 101|
|Http2xx|HTTP 2xx|Count|Всего|HTTP 2xx|
|Http3xx|HTTP 3xx:|Count|Всего|HTTP 3xx:|
|Http401|HTTP 401:|Count|Всего|HTTP 401:|
|Http403|HTTP 403:|Count|Всего|HTTP 403:|
|Http404|HTTP 404:|Count|Всего|HTTP 404:|
|Http406|HTTP 406:|Count|Всего|HTTP 406:|
|Http4xx|HTTP 4xx:|Count|Всего|HTTP 4xx:|
|Http5xx|Ошибки HTTP-сервера:|Count|Всего|Ошибки HTTP-сервера:|
|AverageResponseTime|Среднее время ответа:|Секунды|Средняя|Среднее время ответа:|
|Процент загруженности ЦП|Процент использования ЦП|Процент|Средняя|Процент использования ЦП|
|Процент использования памяти|Процент использования памяти|Процент|Средняя|Процент использования памяти|
|Длина очереди диска|Длина дисковой очереди|Count|Всего|Длина дисковой очереди|
|Длина очереди HTTP|Длина очереди HTTP:|Count|Всего|Длина очереди HTTP:|
|ActiveRequests|Активные запросы|Count|Всего|Активные запросы|
|TotalFrontEnds|Общее число внешних интерфейсов|Count|Средняя|Общее число внешних интерфейсов|
|SmallAppServicePlanInstances|Рабочие роли плана службы приложений уровня "Малый"|Count|Средняя|Рабочие роли плана службы приложений уровня "Малый"|
|MediumAppServicePlanInstances|Рабочие процессы плана службы приложений уровня "Средний"|Count|Средняя|Рабочие процессы плана службы приложений уровня "Средний"|
|LargeAppServicePlanInstances|Рабочие процессы плана службы приложений уровня "Крупный"|Count|Средняя|Рабочие процессы плана службы приложений уровня "Крупный"|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Метрика|Отображаемое имя метрики|Единица измерения|Тип статистической обработки|Описание|
|---|---|---|---|---|
|WorkersTotal|Общее количество рабочих процессов|Count|Средняя|Общее количество рабочих процессов|
|WorkersAvailable|Доступные рабочие процессы|Count|Средняя|Доступные рабочие процессы|
|Workersused|Использованные рабочие процессы|Count|Средняя|Использованные рабочие процессы|

## <a name="next-steps"></a>Дальнейшие действия
* [Прочитайте о метриках в Azure Monitor](monitoring-overview-metrics.md)
* [Создайте оповещения на основе метрик](insights-receive-alert-notifications.md)
* [Экспортируйте метрики в хранилище, концентратор событий или Log Analytics](monitoring-overview-of-diagnostic-logs.md)

