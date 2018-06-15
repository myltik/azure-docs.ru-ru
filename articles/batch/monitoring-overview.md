---
title: Мониторинг пакетной службы Azure | Документация Майкрософт
description: Сведения о службах, метриках, журналах диагностики и других функциях мониторинга для пакетной службы Azure.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: danlep
ms.openlocfilehash: 29ac86ed5c744d37150b0f1b2db17f60306fe77e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31799805"
---
# <a name="monitor-batch-solutions"></a>Мониторинг решений пакетной службы

Azure и пакетная служба предоставляют широкий набор служб, инструментов и API-интерфейсов для мониторинга решений пакетной службы. Эта обзорная статья поможет вам выбрать правильный подход к мониторингу в соответствии с вашими требованиями.

Обзор компонентов и служб Azure, доступных для мониторинга ресурсов Azure, см. в статье [Мониторинг приложений и ресурсов в Azure](../monitoring-and-diagnostics/monitoring-overview.md).

## <a name="subscription-level-monitoring"></a>Мониторинг на уровне подписки

На уровне подписки, в том числе для учетных записей пакетной службы, [журнал действий Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) позволяет собирать операционные данные о событиях по [нескольким категориям](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md#categories-in-the-activity-log).

В частности, для учетных записей пакетной службы в журнал действий собираются данные о создании и удалении учетных записей, а также об управлении ключами.

Для получения событий из журнала действий можно использовать портал Azure. На портале щелкните **Все службы** > **Журнал действий**. Также можно создать запрос сведений о событиях с помощью Azure CLI, командлетов PowerShell или REST API Azure Monitor. Есть возможность экспортировать журнал действий или настроить [оповещения журнала действий](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md).

## <a name="batch-account-level-monitoring"></a>Мониторинг на уровне учетной записи пакетной службы

Для мониторинга отдельных учетных записей пакетной службы можно применить [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Azure Monitor собирает [метрики](../monitoring-and-diagnostics/monitoring-overview-metrics.md) и (если настроено) [журналы диагностики](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) для ресурсов, относящихся к уровню учетной записи пакетной службы, таких как пулы, задания и задачи. Эти данные можно собирать и использовать вручную или программными средствами, чтобы отслеживать действия в учетной записи пакетной службы и диагностировать проблемы. Дополнительные сведения см. в статье [События журнала для диагностики и мониторинга решений пакетной службы](batch-diagnostics.md).
 
> [!NOTE]
> По умолчанию метрики доступны в учетной записи пакетной службы за 30-дневный период со скользящей ротацией. Для сбора данных об учетной записи пакетной службы необходимо включить ведение журнала диагностики, хранение и (или) обработка которого может повлечь некоторые расходы на использование ресурсов. 

## <a name="batch-resource-monitoring"></a>Мониторинг ресурсов пакетной службы

В приложениях пакетной службы вы можете применить API-интерфейсы пакетной службы для отслеживания и запроса состояний ресурсов, таких как задания, задачи, узлы и пулы. Например: 

* [Подсчет задач по состоянию](batch-get-task-counts.md)
* [эффективное создание запросов на вывод списка ресурсов пакетной службы](batch-efficient-list-queries.md);
* [создание зависимостей задач](batch-task-dependencies.md);
* использование [задач диспетчера заданий](/rest/api/batchservice/job/add#jobmanagertask);
* мониторинг [состояния задач](/rest/api/batchservice/task/list#taskstate);
* мониторинг [состояния узлов](/rest/api/batchservice/computenode/list#computenodestate);
* мониторинг [состояния пулов](/rest/api/batchservice/pool/get#poolstate);
* мониторинг [использования пулов в учетной записи](/rest/api/batchservice/pool/listusagemetrics);
* [подсчет количества узлов с определенным состоянием в пуле](/rest/api/batchservice/account/listpoolnodecounts).

## <a name="vm-performance-counters-and-application-monitoring"></a>Мониторинг счетчиков производительности и приложений для виртуальной машины

* Службу [Application Insights](../application-insights/app-insights-overview.md) можно использовать в Azure, чтобы программными средствами отслеживать доступность, производительность и потребление для заданий и задач пакетной службы. Вы можете легко получить счетчики производительности из вычислительных узлов (виртуальных машин) и произвольные данные о задачах на этих виртуальных машинах. 

  Примеры можно найти в статье [Monitor and debug an Azure Batch .NET application with Application Insights](monitor-application-insights.md) (Мониторинг и отладка приложения .NET пакетной службы с помощью Application Insights) и в [примерах кода](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights) для нее.

  > [!NOTE]
  > Использование Application Insights может повлечь дополнительные затраты. Ознакомьтесь с [информацией о тарифных планах](https://azure.microsoft.com/pricing/details/application-insights/). 
  >

* [BatchLabs](https://github.com/Azure/BatchLabs) — это бесплатное автономное клиентское средство с множеством функций для создания, отладки и мониторинга приложений пакетной службы Azure. Скачайте [пакет установки](https://azure.github.io/BatchLabs/) для Mac, Linux или Windows. (Необязательно.) Настройте в решении пакетной службы [отображение данных из Application Insights](https://github.com/Azure/batch-insights), таких как счетчики производительности виртуальных машин в BatchLabs.


## <a name="next-steps"></a>Дополнительная информация

* См. дополнительные сведения об [API-интерфейсах и средствах пакетной службы](batch-apis-tools.md) для сборки решений пакетной службы.
* Узнайте больше о [ведении журналов диагностики](batch-diagnostics.md) для пакетной службы.