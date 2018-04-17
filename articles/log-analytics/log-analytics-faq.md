---
title: Log Analytics — часто задаваемые вопросы | Документация Майкрософт
description: Ответы на часто задаваемые вопросы о службе Azure Log Analytics.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: magoedte
ms.openlocfilehash: 22da58df653b31c46145ebbbd1f6f6a26b0e9f29
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="log-analytics-faq"></a>Часто задаваемые вопросы по Log Analytics
В этом списке вопросов и ответов от Майкрософт приведены часто задаваемые вопросы о Log Analytics в Microsoft Azure. Если у вас возникли дополнительные вопросы о Log Analytics, задайте их на [форуме](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights). Если вопрос задается часто, мы добавим его в эту статью, чтобы его можно было найти быстро и легко.

## <a name="general"></a>Общие сведения

### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>В. Использует ли Log Analytics тот же агент, что и центр безопасности Azure?

О. С начала июня 2017 г. в центре безопасности Azure используется Microsoft Monitoring Agent для сбора и хранения данных. Дополнительные сведения см. в статье [Вопросы и ответы по миграции платформы центра безопасности Azure](../security-center/security-center-platform-migration-faq.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>В. Какие проверки выполняют AD и решение по оценке SQL?

О. В следующем запросе показано описание всех проверок, которые выполняются в настоящее время:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Результаты можно экспортировать в Excel для дальнейшего анализа.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>В. Почему в консоли System Center Operations Manager отображается другое значение (не OMS)?

О. В зависимости от того, какую версию накопительного обновления Operations Manager вы используете, может отображаться узел *System Center Advisor*, *Operational Insights* или *Log Analytics*.

Обновление текстовой строки до *OMS* включено в пакет управления, который необходимо импортировать вручную. Чтобы просмотреть текущую текстовую строку и функциональные возможности, следуйте инструкциям в последней статье о накопительном пакете обновления SCOM в базе знаний и обновите консоль.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>В. Существует ли локальная версия Log Analytics?

О. Нет. Log Analytics — это масштабируемая облачная служба, которая обрабатывает и хранит больше объемы данных. 

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>В. Как можно понять, что Log Analytics больше не собирает данные?

О. Если для подписки и рабочей области, созданной до 2 апреля 2018 года в ценовой категории *Бесплатный*, в день передано более 500 МБ данных, то до конца дня сбор данных останавливается. Достижение ежедневного ограничения является распространенной причиной, по которой Log Analytics прекращает сбор данных или по которой данные отсутствуют.  

В Log Analytics создается событие типа *Пульс*. Также служба позволяет определить остановку сбора данных. 

Выполните следующий запрос в поле поиска, чтобы проверить, был ли достигнут лимит и отсутствуют ли данные: `Heartbeat | summarize max(TimeGenerated)`.

Чтобы проверить конкретный компьютер, выполните следующий запрос: `Heartbeat | where Computer=="contosovm" | summarize max(TimeGenerated)`.

Если сбор данных останавливается (в зависимости от выбранного диапазона времени), записи не возвращаются.   

В следующей таблице описаны причины, по которым сбор данных останавливается, и приведены рекомендуемые действия, чтобы его возобновить:

| Причина прекращения сбора данных                       | Чтобы возобновить сбор данных |
| -------------------------------------------------- | ----------------  |
| Достигнут предел бесплатного сбора данных<sup>1</sup>       | Дождитесь следующего месяца для автоматического перезапуска или<br> перейдите на платную ценовую категорию |
| Подписка Azure находится в состоянии "Приостановлено" по причине: <br> Период бесплатной пробной версии завершен <br> Истек срок действия Azure Pass <br> Достигнут лимит ежемесячной суммы расходов (например, на подписку MSDN или Visual Studio)                          | Измените подписку на платную <br> Измените подписку на платную <br> Удалите ограничение или подождите, пока оно сбросится |

<sup>1</sup> Если ваша рабочая область находится в ценовой категории *Бесплатный*, вы можете отправлять в службу не более 500 МБ данных в день. По достижении ежедневного лимита сбор данных останавливается до следующего дня. Данные, передаваемые во время остановки сбора данных, не индексируется и не доступны для поиска. Когда сбор данных возобновляется, обработка выполняется только для новых передаваемых данных. 

Log Analytics использует время в формате UTC и каждый день начинается в полночь по времени UTC. Если рабочая область достигает ежедневного ограничения, обработка возобновляется на следующий день в течение первого часа в формате UTC.

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>В. Как получить уведомление о прекращении сбора данных?

О. Выполните действия, описанные в разделе [Создание правила оповещения](log-analytics-alerts-creating.md#create-an-alert-rule), чтобы получать уведомления при прекращении сбора данных.

При создании оповещения, когда прекращается сбор данных, задайте параметрам следующие значения:
- **имени**  — *остановка сбора данных*;
- **серьезности** — *предупреждение*;
- **поисковому запросу** — `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`;
- **Временное окно** — *30 минут*;
- **Периодичность предупреждений** — каждые *десять* минут;
- параметру **создания оповещения на основе** задайте *число результатов*;
- **числу результатов** — значение *больше 0*.

Это предупреждение будет срабатывать при возвращении результатов запросов, если пульс отсутствует более 15 минут.  Выполните действия, описанные в статье[Добавление действий в правила оповещений в Log Analytics](log-analytics-alerts-actions.md), чтобы настроить действия электронной почты, веб-перехватчика или Runbook для правила оповещения.

## <a name="configuration"></a>Параметр Configuration
### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>В. Можно ли изменить имя таблицы или контейнера больших двоичных объектов, используемое для чтения из Azure Diagnostics (WAD)?

О. Нет, в настоящее время невозможно выполнить чтение из произвольных таблиц или контейнеров в хранилище Azure.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>В. Какие IP-адреса использует служба Log Analytics? Как сделать так, чтобы мой брандмауэр разрешал только трафик к службам Log Analytics?

О. В основе службы Log Analytics лежит Azure. IP-адреса Log Analytics находятся в [диапазонах IP-адресов центра обработки данных Microsoft Azure](http://www.microsoft.com/download/details.aspx?id=41653).

При развертывании службы фактические IP-адреса службы Log Analytics изменяются. DNS-имена, обеспечивающие передачу данных через брандмауэр, описаны в [требованиях к системе](log-analytics-concept-hybrid.md#prerequisites).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>В. Я применяю ExpressRoute для подключения к Azure. Будет ли трафик Log Analytics использовать подключение ExpressRoute?

О. Различные типы трафика ExpressRoute описаны в [документации по ExpressRoute](../expressroute/expressroute-faqs.md#supported-services).

Трафик к Log Analytics использует общедоступный пиринговый канал ExpressRoute.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>В. Есть ли простой способ переместить существующую рабочую область Log Analytics в другую рабочую область или подписку Azure для Log Analytics?

О. С помощью командлета `Move-AzureRmResource` вы можете переместить рабочую область Log Analytics, а также учетную запись службы автоматизации из одной подписки Azure в другую. Дополнительные сведения см. в документации [Move-AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx).

Это изменение также можно выполнить на портале Azure.

Нельзя перемещать данные из одной рабочей области Log Analytics в другую или изменять регион, в котором хранятся данные Log Analytics.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>В. Как добавить Log Analytics в SCOM?

О. Чтобы подключить Operations Manager к Log Analytics, выполните обновление до последнего накопительного пакета обновлений и импортируйте пакеты управления.

>[!NOTE]
>Подключение Operations Manager к Log Analytics возможно только для SCOM 2012 с пакетом обновления SP1 и более поздних версий.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>В. Как проверить, что агент может взаимодействовать с Log Analytics?

Ответ. Чтобы убедиться, что агент может взаимодействовать с OMS, выберите элементы "Панель управления", "Безопасность и параметры", **Microsoft Monitoring Agent**.

В разделе **Azure Log Analytics (OMS)** найдите зеленый флажок. Зеленый флажок подтверждает, что агент может взаимодействовать со службой Azure.

Желтый значок предупреждения означает, что агент испытывает проблемы при взаимодействии с Log Analytics. Одна из основных причин — служба Microsoft Monitoring Agent остановлена. Перезапустите службу с помощью диспетчера управления службами.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>В. Как остановить взаимодействие агента с Log Analytics?

О. В SCOM удалите компьютер из списка компьютеров, находящихся под управлением OMS. Operations Manager обновит конфигурацию агента, чтобы больше не отправлять отчеты в Log Analytics. Для агентов, подключенных к Log Analytics напрямую, можно отключить взаимодействие, выбрав элементы "Панель управления", "Безопасность и параметры", **Microsoft Monitoring Agent**.
В разделе **Azure Log Analytics (OMS)** удалите все рабочие области.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>В. Почему возникает ошибка при попытке переместить рабочую область из одной подписки Azure в другую?

О. При использовании портала Azure убедитесь, что для перемещения выбрана только рабочая область. Не выбирайте решения, так как они автоматически переместятся после перемещения рабочей области. 

Убедитесь, что у вас есть разрешение в обеих подписках Azure.

## <a name="agent-data"></a>Данные агента
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>В. Каков объем данных, которые можно отправить с помощью агента в Log Analytics? Существует ли максимальный объем данных для одного клиента?
О. Для плана "Бесплатный" ежедневное ограничение составляет 500 МБ. Для планов "Стандартный" и "Премиум" ограничений по объему отправляемых данных нет. Как облачная служба, Log Analytics может автоматически масштабироваться с учетом объема данных, поступающих от клиента, даже если это несколько ТБ в день.

Агент Log Analytics оказывает минимальное влияние на систему. Том данных зависит от решений, которые включены. Подробные сведения об объеме данных и сведения для каждого решения можно найти в статье об [использовании данных](log-analytics-usage.md).

Дополнительные сведения можно прочитать в [блоге клиентов](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html), в котором они показывают результаты после оценки использования ресурсов (памяти) агентом OMS.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>В. Какой объем пропускной способности сети использует Microsoft Management Agent (MMA) при отправке данных в Log Analytics?

О. Пропускная способность зависит от объема отправляемых данных. При передаче по сети данные сжимаются.

### <a name="q-how-much-data-is-sent-per-agent"></a>В. Какой объем данных отправляет каждый агент?

О. Объем данных, отправляемых каждым агентом, зависит от:

* решения, которое вы используете;
* количества журналов и счетчиков производительности, которые вы собираете;
* объема данных в журналах.

Ценовой категории "Бесплатный" достаточно для нескольких серверов и анализа типичного объема данных. Общее использование отображается на странице [Использование](log-analytics-usage.md) .

Для компьютеров, на которых может запускаться агент WireData, объем отправляемых данных можно просмотреть с помощью следующего запроса:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Дополнительная информация
* [Приступая к работе с Log Analytics](log-analytics-get-started.md) .
