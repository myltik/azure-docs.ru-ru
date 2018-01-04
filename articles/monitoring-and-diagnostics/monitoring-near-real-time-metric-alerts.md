---
title: "Оповещения на основе метрик практически в реальном времени в Azure Monitor | Документация Майкрософт"
description: "Оповещения на основе метрик практически в реальном времени позволяют выполнять мониторинг метрик ресурсов Azure каждую минуту."
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: cd1002929ad749ac1742e914a9f2411f09ec91d5
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/19/2017
---
# <a name="near-real-time-metric-alerts-preview"></a>Оповещения на основе метрик практически в реальном времени (предварительная версия)
Azure Monitor теперь поддерживает новый тип оповещений на основе метрик, который называется "Оповещения на основе метрик практически в реальном времени" (предварительная версия). Эта функция сейчас доступна в виде общедоступной предварительной версии.
Далее приведены сведения об отличиях этих оповещений от обычных оповещений на основе метрик.

- **Низкая задержка.** С помощью оповещений на основе метрик практически в реальном времени можно выполнять мониторинг изменений значений метрик каждую минуту.
- **Дополнительный контроль метрик.** Оповещения на основе метрик практически в реальном времени позволяют пользователям определить расширенные правила генерации оповещений. Оповещения теперь поддерживают мониторинг максимальных, минимальных, средних и общих значений метрик.
- **Объединенный мониторинг нескольких метрик.** Оповещения на основе метрик практически в реальном времени позволяют выполнять мониторинг нескольких метрик (сейчас только двух метрик) с помощью одного правила. Оповещения активируются, если обе метрики превышают соответствующие пороговые значения для заданного периода времени.
- **Модульная система отправки уведомлений.** Оповещения на основе метрик практически в реальном времени используют [группы действий](monitoring-action-groups.md). Эта функция позволяет пользователям создавать действия в модульном виде и повторно использовать их для множества правил генерации оповещений.

> [!NOTE]
> Функция оповещений на основе метрик практически в реальном времени сейчас доступна в виде общедоступной версии. Функциональные возможности и условия взаимодействия с пользователем могут изменяться.
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>Для каких ресурсов можно создать оповещения на основе метрик практически в реальном времени?
Далее приведен список типов ресурсов, поддерживаемых оповещениями на основе метрик практически в реальном времени:

* Microsoft.ApiManagement/service
* Microsoft.Automation/automationAccounts
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis;
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets;
* Microsoft.DataFactory/factories;
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses;
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Storage/storageAccounts
* Microsoft.Storage/storageAccounts/services
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.CognitiveServices/accounts

## <a name="near-real-time-metric-alerts-on-metrics-with-dimensions"></a>NEAR предупреждений в реальном времени метрики на метрики с измерениями
Рядом с метрика предупреждений в реальном времени поддерживает предупреждения о метрики с измерениями. Измерения — это способ фильтрации вашей метрики для необходимого уровня. Почти в реальном времени метрики оповещений о показателях с измерениями поддерживаются для следующих типов ресурсов

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts (поддерживается только для учетных записей хранения в регионах США)
* Microsoft.Storage/storageAccounts/services (поддерживается только для учетных записей хранения в регионах США)


## <a name="create-a-near-real-time-metric-alert"></a>Создание оповещений на основе метрик практически в реальном времени
Сейчас оповещения на основе метрик практически в реальном времени можно создать только на портале Azure. Реализация поддержки настройки оповещений на основе метрик практически в реальном времени с помощью PowerShell, интерфейса командной строки и REST API Azure Monitor ожидается в ближайшем времени.

Создать предупреждения опыт для почти в реальном времени метрики предупреждения перемещен в новый **Alerts(Preview)** столкнуться. Несмотря на то, текущие оповещения страниц отображает **предупреждения добавьте почти в реальном времени метрика**, вы будете перенаправлены на новый интерфейс.

Можно создать практически в режиме реального времени метрики предупреждение с помощью действия, описанные [здесь](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="managing-near-real-time-metric-alerts"></a>Управление оповещениями на основе метрик практически в реальном времени
После создания **почти в реальном времени метрики предупреждение**, им можно управлять с помощью действия, описанные [здесь](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="next-steps"></a>Дальнейшие действия

* [Дополнительные сведения о новом интерфейсе оповещения (Предварительная версия)](monitoring-overview-unified-alerts.md)
* [Дополнительные сведения о предупреждениях журнала в Azure Alerts (Предварительная версия)](monitor-alerts-unified-log.md)
* [Дополнительные сведения о предупреждениях в Azure](monitoring-overview-alerts.md)