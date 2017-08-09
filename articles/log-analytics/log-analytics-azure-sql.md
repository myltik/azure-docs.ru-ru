---
title: "Решение служб анализа SQL Azure в Log Analytics | Документация Майкрософт"
description: "Решение служб анализа SQL Azure поможет вам управлять базами данных SQL Azure."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: banders
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: cab45cc6dd621eb4a95ef5f1842ec38c25e980b6
ms.contentlocale: ru-ru
ms.lasthandoff: 07/28/2017

---


# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>Мониторинг базы данных SQL Azure с помощью служб анализа SQL Azure (предварительная версия) в Log Analytics

![Символ службы "Аналитика SQL Azure"](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Решение "Аналитика SQL Azure" в Azure Log Analytics собирает и отображает важные метрики производительности SQL Azure. На основе этих метрик можно создавать пользовательские правила мониторинга и оповещения. Вы можете отслеживать метрики базы данных SQL Azure и эластичных пулов в нескольких подписках Azure и эластичных пулах, а также визуализировать их. Решение также поможет вам определить проблемы на каждом уровне стека приложений.  Оно использует [метрики диагностики Azure](log-analytics-azure-storage.md) с представлениями Log Analytics, чтобы представить данные обо всех базах данных SQL Azure и эластичных пулах в единой рабочей области Log Analytics.

Сейчас эта предварительная версия решения поддерживает 150 000 баз данных SQL Azure и 5 000 эластичных пулов SQL на каждую рабочую область.

Решение "Аналитика SQL Azure", как и другие решения, доступные для Log Analytics, помогает отслеживать и получать уведомления о работоспособности ресурсов Azure — в данном случае о базах данных SQL Azure. База данных SQL Microsoft Azure представляет собой масштабируемую службу реляционных баз данных, предоставляющую возможности, аналогичные возможностям SQL Server, в приложениях, запущенных в облаке Azure. Log Analytics помогает собирать, коррелировать и визуализировать структурированные и неструктурированные данные.

## <a name="connected-sources"></a>Подключенные источники

Решение "Аналитика SQL Azure" не использует агенты для подключения к службе Log Analytics.

В следующей таблице описаны подключенные источники, которые поддерживаются этим решением.

| Подключенный источник | Поддержка | Описание |
| --- | --- | --- |
| [Агенты Windows](log-analytics-windows-agents.md) | Нет | Решение не использует прямые агенты Windows. |
| [Агенты Linux](log-analytics-linux-agents.md) | Нет | Решение не использует прямые агенты Linux. |
| [Группы управления SCOM](log-analytics-om-agents.md) | Нет | Решение не использует прямое подключение агента SCOM к Log Analytics. |
| [Учетная запись хранения Azure](log-analytics-azure-storage.md) | Нет | Log Analytics не считывает данные из учетной записи хранения. |
| [Настройка системы диагностики Azure для входа в Application Insights](log-analytics-azure-storage.md) | Да | Данные метрик Azure отправляются в Log Analytics из Azure непосредственно. |

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас ее нет, вы можете создать ее [бесплатно](https://azure.microsoft.com/free/).
- Рабочая область Log Analytics. Вы можете использовать имеющуюся рабочую область или же [создать ее](log-analytics-get-started.md), прежде чем начать использовать это решение.
- Включите систему диагностики Azure для баз данных SQL Azure и эластичных пулов и [настройте для них отправку данных в Log Analytics](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/).

## <a name="configuration"></a>Конфигурация

Чтобы добавить решение "Аналитика SQL Azure" в рабочую область, сделайте следующее.

1. Решение для анализа Azure SQL необходимо добавить в рабочую область из [Azure Мarketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview) или в соответствии с инструкциями по [добавлению решений Log Analytics из коллекции решений](log-analytics-add-solutions.md).
2. На портале Azure щелкните **Создать** (знак +), а затем в списке ресурсов выберите **Мониторинг и управление**.  
    ![Мониторинг и управление](./media/log-analytics-azure-sql/monitoring-management.png)
3. В списке **Мониторинг и управление** щелкните **Показать все**.
4. В списке **Рекомендуется** выберите **More** (Дополнительно), а затем в новом списке найдите и выберите **Службы анализа SQL Azure (предварительная версия)**.  
    ![Решение служб анализа SQL Azure](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. В колонке **Службы анализа SQL Azure (предварительная версия)** щелкните **Создать**.  
    ![Создание](./media/log-analytics-azure-sql/portal-create.png)
6. В колонке **Создание решения** выберите рабочую область, в которую необходимо добавить решение, а затем нажмите кнопку **Создать**.  
    ![Добавление в рабочую область](./media/log-analytics-azure-sql/add-to-workspace.png)


### <a name="to-configure-multiple-azure-subscriptions"></a>Настройка нескольких подписок Azure

Чтобы обеспечить поддержку нескольких подписок, используйте сценарий PowerShell. Дополнительные сведения см. в статье [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/) (Включение ведения журнала метрик ресурсов Azure с помощью PowerShell). Укажите идентификатор ресурса рабочей области в качестве параметра при выполнении скрипта для отправки диагностических данных из ресурсов в одной подписке Azure в рабочую область в другой подписке Azure.

**Пример**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>Использование решения

Вместе с решением в рабочую область добавляется элемент служб анализа SQL Azure. Кроме того, он появляется в общих сведениях. В элементе отображаются сведения о количестве баз данных и эластичных пулов SQL Azure, к которым подключено решение.

![Элемент служб анализа SQL Azure](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Просмотр данных службы "Аналитика SQL Azure"

Щелкните плитку **Azure SQL Analytics** (Аналитика SQL Azure), чтобы открыть панель мониторинга службы "Аналитика SQL Azure". Панель мониторинга содержит колонки, определенные ниже. В каждой колонке приведено до 15 ресурсов (подписка, сервер, эластичный пул и база данных). Щелкните любой из ресурсов, чтобы открыть для него панель мониторинга. Эластичный пул или база данных содержит диаграммы с метриками для выбранного ресурса. Щелкните диаграмму, чтобы открыть диалоговое окно поиска по журналам.

| Колонка | Описание |
|---|---|
| Подписки | Список подписок с количеством подключенных серверов, пулов и баз данных. |
| Серверы | Список серверов с количеством подключенных пулов и баз данных. |
| Эластичные пулы | Список подключенных эластичных пулов с максимальным объемом хранилища в ГБ и eDTU в течение наблюдаемого периода времени. |
|Базы данных | Список подключенных баз данных с максимальным объемом хранилища в ГБ и eDTU в течение наблюдаемого периода времени.|


### <a name="analyze-data-and-create-alerts"></a>Анализ данных и создание оповещений

Оповещения можно легко создать с помощью данных, поступающих из ресурсов базы данных SQL Azure. Вот несколько полезных запросов для [поиска по журналам](log-analytics-log-searches.md), которые можно использовать для предупреждений.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]


*Высокий уровень DTU в базе данных SQL Azure*

```
Type=AzureMetrics ResourceProvider="MICROSOFT.SQL" ResourceId=*"/DATABASES/"* MetricName=dtu_consumption_percent | measure Avg(Average) by Resource interval 5minutes
```

*Высокий уровень DTU в эластичном пуле базы данных SQL Azure*

```
Type=AzureMetrics ResourceProvider="MICROSOFT.SQL" ResourceId=*"/ELASTICPOOLS/"* MetricName=dtu_consumption_percent | measure avg(Average) by Resource interval 5minutes
```

Используя запросы на основе оповещений, можно создавать предупреждения об определенных пороговых значениях для баз данных и эластичных пулов SQL Azure. Чтобы настроить оповещение для рабочей области OMS, сделайте следующее.

#### <a name="to-configure-an-alert-for-your-workspace"></a>Чтобы настроить оповещение для рабочей области, сделайте следующее.

1. Перейдите на [портал OMS](http://mms.microsoft.com/) и войдите в систему.
2. Откройте рабочую область, настроенную для решения.
3. На странице обзора щелкните элемент **Службы анализа SQL Azure (предварительная версия)**.
4. Выполните один из примеров запросов.
5. В поиске по журналам щелкните **Оповещение**.  
![Создание оповещения в поиске](./media/log-analytics-azure-sql/create-alert01.png)
6. На странице **Добавить правило оповещения** настройте соответствующие свойства и определенные пороговые значения, а затем нажмите кнопку **Сохранить**.  
![Добавление правила оповещения](./media/log-analytics-azure-sql/create-alert02.png)

### <a name="act-on-azure-sql-analytics-data"></a>Выполнение действий над данными службы "Аналитика SQL Azure"

К примеру, один из наиболее полезных запросов, которые можно выполнить, заключается в сравнении использования DTU для всех эластичных пулов SQL Azure во всех подписках Azure. Единица пропускной способности базы данных (DTU) выражает относительную емкость уровня производительности пулов и баз данных уровня "Базовый", "Стандартный" и "Премиум". Единицы DTU получают на основе показателей ЦП, памяти, операций чтения и записи. С увеличением числа единиц DTU увеличивается также и мощность, предлагаемая на уровне производительности. Например, уровень производительности с 5 единицами DTU в пять раз выше уровня производительности с 1 единицей DTU. К каждому серверу и эластичному пулу применяется максимальная квота DTU.

Выполнив следующий запрос поиска по журналам, вы можете легко определить степень использования эластичных пулов SQL Azure.

```
Type=AzureMetrics ResourceId=*"/ELASTICPOOLS/"* MetricName=dtu_consumption_percent | measure avg(Average) by Resource | display LineChart
```

>[!NOTE]
> Если ваша рабочая область переведена на [язык запросов Log Analytics](log-analytics-log-search-upgrade.md), приведенный выше запрос будет изменен следующим образом.
>
>`search in (AzureMetrics) isnotempty(ResourceId) and "/ELASTICPOOLS/" and MetricName == "dtu_consumption_percent" | summarize AggregatedValue = avg(Average) by bin(TimeGenerated, 1h), Resource | render timechart`

Как видно из примера ниже, в одном эластичном пуле используется практически 100 % единиц DTU, тогда как показатели других чрезвычайно низкие. С помощью журналов действий Azure вы можете устранить потенциальные последние изменения в окружении.

![Результаты поиска по журналам. Высокая загрузка](./media/log-analytics-azure-sql/log-search-high-util.png)

## <a name="see-also"></a>См. также

- Используйте [поиск по журналам](log-analytics-log-searches.md) в Log Analytics для просмотра подробных данных SQL Azure.
- [Создавайте пользовательские панели мониторинга](log-analytics-dashboards.md), отображающие данные SQL Azure.
- [Создавайте оповещения](log-analytics-alerts.md), предупреждающие о возникновении определенных событий SQL Azure.

