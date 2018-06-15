---
title: Решение для анализа сетей Azure в Log Analytics | Документация Майкрософт
description: Решение для анализа сетей Azure можно использовать в Log Analytics для просмотра журналов групп безопасности сети Azure и шлюза приложений Azure.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: ewinner
editor: ''
ms.assetid: 66a3b8a1-6c55-4533-9538-cad60c18f28b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: richrund
ms.openlocfilehash: 12172e81ed6b4d79ee200ee1ca79803ad58d6d19
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2018
ms.locfileid: "30263536"
---
# <a name="azure-networking-monitoring-solutions-in-log-analytics"></a>Решения для мониторинга сетей Azure в Log Analytics

Log Analytics предлагает следующие решения для мониторинга сетей.
* Монитор производительности сети (NPM):
 * отслеживание работоспособности сети.
* Анализ шлюзов приложений Azure для проверки:
 * журналы шлюза приложений Azure;
 * метрику шлюза приложений Azure.
* Решения для отслеживания и аудита сетевой активности в облачной сети.
* [Анализ трафика](https://docs.microsoft.com/azure/networking/network-monitoring-overview#traffic-analytics). 
* Анализ групп безопасности сети Azure

## <a name="network-performance-monitor-npm"></a>Монитор производительности сети

Решение по управлению [Монитор производительности сети](https://docs.microsoft.com/azure/networking/network-monitoring-overview) — это решение для мониторинга сети, которое отслеживает работоспособность и доступность сетей.  Оно используется для отслеживания подключения между следующими ресурсами:

* общедоступное облако и локальная среда;
* центры обработки данных и расположения пользователей (филиалы);
* подсети, в которых размещены различные уровни многоуровневого приложения.

Дополнительную информацию см. в статье [Решение монитора производительности сети в Azure Log Analytics](https://docs.microsoft.com/azure/networking/network-monitoring-overview).

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Шлюз приложений Azure и анализ групп безопасности сети
Чтобы использовать эти решения, выполните следующие действия.
1. Добавьте решение по управлению в Log Analytics.
2. Включите диагностику для аналитики журналов в рабочей области Log Analytics. Необязательно записывать журналы в хранилище BLOB-объектов Azure.

Диагностику и соответствующее решение можно включить как для одного, так и для обоих компонентов (шлюз приложений и группы безопасности сети).

Если не включить ведение журналов диагностики для определенного типа ресурсов, но установить решение, то колонки панели мониторинга для этого ресурса будут пустыми, а также появится сообщение об ошибке.

> [!NOTE]
> В январе 2017 г. поддерживаемый способ отправки журналов из шлюзов приложений и групп безопасности сети в Log Analytics был изменен. Если отобразится устаревшее решение **Анализ сетевой активности Azure (не рекомендуется)**, то выполните действия, описанные в разделе [Миграция из устаревшего решения для анализа сетевой активности](#migrating-from-the-old-networking-analytics-solution).
>
>

## <a name="review-azure-networking-data-collection-details"></a>Просмотр сведений о сборе данных о сетях Azure
Решения для управления анализом шлюзов приложений и групп безопасности сети Azure собирают журналы диагностики непосредственно из шлюзов приложений и групп безопасности сети Azure. Необязательно записывать журналы в хранилище BLOB-объектов Azure. Для сбора данных агенты не требуются.

В следующей таблице приведены методы сбора данных и другие сведения о сборе данных для анализа шлюзов приложений и групп безопасности сети Azure.

| платформа | Direct Agent | Агент Systems Center Operations Manager | Таблицы Azure | Нужен ли Operations Manager? | Отправка данных агента Operations Manager через группу управления | Частота сбора |
| --- | --- | --- | --- | --- | --- | --- |
| Таблицы Azure |  |  |&#8226; |  |  |при входе |


## <a name="azure-application-gateway-analytics-solution-in-log-analytics"></a>Решение для анализа шлюзов приложений Azure в Log Analytics

![Символ "Аналитика службы приложений Azure"](./media/log-analytics-azure-networking/azure-analytics-symbol.png)

Шлюзы приложений поддерживают следующие журналы:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

Шлюзы приложений поддерживают следующие метрики:

* пропускная способность за 5 минут.

### <a name="install-and-configure-the-solution"></a>Установка и настройка решения
Установите и настройте решение для анализа шлюзов приложений Azure, выполнив следующие указания:

1. Включите решение для анализа шлюзов приложений Azure из [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) или выполните инструкции из статьи [Добавление решений для управления Azure Log Analytics в рабочую область](log-analytics-add-solutions.md).
2. Включите ведение журнала диагностики для [шлюзов приложений](../application-gateway/application-gateway-diagnostics.md), для которых требуется выполнять мониторинг.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Включение диагностики шлюза приложений Azure на портале

1. На портале Azure перейдите к ресурсу шлюза приложений, который необходимо отслеживать.
2. Выберите *Журналы диагностики*, чтобы открыть следующую страницу:

   ![снимок экрана: ресурс шлюза приложений Azure](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics01.png)
3. Щелкните *Включить диагностику*, чтобы открыть следующую страницу:

   ![снимок экрана: ресурс шлюза приложений Azure](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics02.png)
4. Чтобы включить диагностику, нажмите кнопку *Вкл.* в разделе *Состояние*.
5. Установите флажок *Send to Log Analytics* (Отправить в Log Analytics).
6. Выберите существующую рабочую область Log Analytics или создайте новую.
7. В разделе **Журнал** установите флажки для всех типов журналов, которые необходимо собирать.
8. Щелкните *Сохранить*, чтобы включить ведение журнала диагностики в Log Analytics.

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Включение диагностики сети Azure с помощью PowerShell

Следующий сценарий PowerShell приведен в качестве примера того, как включить ведение журналов диагностики для шлюзов приложений:

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzureRmApplicationGateway -Name 'ContosoGateway'

Set-AzureRmDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Использование анализа шлюзов приложений Azure
![снимок экрана: элемент "Анализ шлюзов приложений Azure"](./media/log-analytics-azure-networking/log-analytics-appgateway-tile.png)

Выбрав элемент **Azure Application Gateway analytics** (Анализ шлюзов приложений Azure) в разделе "Обзор", можно просмотреть сводные данные журналов и подробные сведения по следующим категориям:

* Журналы доступа к шлюзу приложений:
  * ошибки клиента и сервера для журналов доступа шлюза приложений;
  * количество запросов в час для каждого шлюза приложений;
  * количество неудачных запросов в час для каждого шлюза приложений;
  * ошибки агентов пользователя для шлюзов приложений.
* Производительность шлюза приложений:
  * работоспособность узла для шлюза приложений;
  * максимальное количество и 95-й процентиль для неудачных запросов шлюза приложений.

![снимок экрана: панель мониторинга "Анализ шлюзов приложений Azure"](./media/log-analytics-azure-networking/log-analytics-appgateway01.png)

![снимок экрана: панель мониторинга "Анализ шлюзов приложений Azure"](./media/log-analytics-azure-networking/log-analytics-appgateway02.png)

На панели мониторинга **Azure Application Gateway analytics** (Анализ шлюзов приложений Azure) просмотрите сводные данные в колонках, а затем щелкните одну из них, чтобы просмотреть подробные сведения на странице поиска журналов.

На любой из страниц поиска журналов можно просмотреть результаты по времени, подробные результаты и историю поиска журналов. Для сужения области результатов выполните фильтрацию по аспектам.


## <a name="azure-network-security-group-analytics-solution-in-log-analytics"></a>Решение для анализа групп безопасности сети Azure в Log Analytics

![Символ "Аналитика групп безопасности сетей Azure"](./media/log-analytics-azure-networking/azure-analytics-symbol.png)

Группы безопасности сети поддерживают следующие журналы:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Установка и настройка решения
Установите и настройте решение для анализа сетей Azure, выполнив следующие указания:

1. Включите решение для анализа групп безопасности сети Azure из [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) или выполните инструкции из статьи [Добавление решений для управления Azure Log Analytics в рабочую область](log-analytics-add-solutions.md).
2. Включите ведение журнала диагностики для ресурсов [групп безопасности сети](../virtual-network/virtual-network-nsg-manage-log.md), для которых требуется выполнять мониторинг.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Включение диагностики групп безопасности сети Azure на портале

1. На портале Azure перейдите к ресурсу группы безопасности сети, который необходимо отслеживать.
2. Выберите *Журналы диагностики*, чтобы открыть следующую страницу:

   ![снимок экрана: ресурс группы безопасности сети Azure](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics01.png)
3. Щелкните *Включить диагностику*, чтобы открыть следующую страницу:

   ![снимок экрана: ресурс группы безопасности сети Azure](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics02.png)
4. Чтобы включить диагностику, нажмите кнопку *Вкл.* в разделе *Состояние*.
5. Установите флажок *Send to Log Analytics* (Отправить в Log Analytics).
6. Выберите существующую рабочую область Log Analytics или создайте новую.
7. В разделе **Журнал** установите флажки для всех типов журналов, которые необходимо собирать.
8. Щелкните *Сохранить*, чтобы включить ведение журнала диагностики в Log Analytics.

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Включение диагностики сети Azure с помощью PowerShell

Следующий сценарий PowerShell приведен в качестве примера того, как включить ведение журналов диагностики для групп безопасности сети:
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzureRmNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzureRmDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Использование анализа групп безопасности сети Azure
Выбрав элемент **Azure Network Security Group analytics** (Анализ групп безопасности сети Azure) в разделе "Обзор", можно просмотреть сводные данные журналов и подробные сведения по следующим категориям:

* Заблокированные потоки группы безопасности сети:
  * правила группы безопасности сети с заблокированными потоками;
  * MAC-адреса c заблокированными потоками.
* Разрешенные потоки группы безопасности сети:
  * правила группы безопасности сети с разрешенными потоками;
  * MAC-адреса c разрешенными потоками.

![снимок экрана: панель мониторинга "Анализ групп безопасности сети Azure"](./media/log-analytics-azure-networking/log-analytics-nsg01.png)

![снимок экрана: панель мониторинга "Анализ групп безопасности сети Azure"](./media/log-analytics-azure-networking/log-analytics-nsg02.png)

На панели мониторинга **Azure Network Security Group analytics** (Анализ групп безопасности сети Azure) просмотрите сводные данные в колонках, а затем щелкните одну из них, чтобы просмотреть подробные сведения на странице поиска журналов.

На любой из страниц поиска журналов можно просмотреть результаты по времени, подробные результаты и историю поиска журналов. Для сужения области результатов выполните фильтрацию по аспектам.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Миграция из устаревшего решения для анализа сетевой активности
В январе 2017 г. поддерживаемый способ отправки журналов из шлюзов приложений Azure и групп безопасности сети Azure в Log Analytics был изменен. Эти изменения обеспечивают следующие преимущества:
+ Журналы записываются непосредственно в Log Analytics без необходимости использования учетной записи хранения.
+ Меньше задержка между моментом создания журналов и их доступностью в Log Analytics.
+ Меньше этапов настройки.
+ Общий формат для всех типов системы диагностики Azure.

Чтобы использовать обновленные решения, выполните следующие действия:

1. [Настройте непосредственную отправку данных диагностики из шлюзов приложений Azure в Log Analytics](#enable-azure-application-gateway-diagnostics-in-the-portal).
2. [Настройте непосредственную отправку данных диагностики из групп безопасности сети Azure в Log Analytics](#enable-azure-network-security-group-diagnostics-in-the-portal).
2. Включите решения для *анализа шлюзов приложений Azure* и *анализа групп безопасности сети Azure*, как описано в статье [Добавление решений для управления Log Analytics](log-analytics-add-solutions.md).
3. Обновите все сохраненные запросы, панели мониторинга и оповещения, чтобы использовать новый тип данных.
  + Тип меняется на AzureDiagnostics. Параметр ResourceType можно использовать для фильтрации по журналам сети.

    | Используйте такую замену: | Используйте следующую команду: |
    | --- | --- |
    | NetworkApplicationgateways &#124; where OperationName=="ApplicationGatewayAccess" | AzureDiagnostics &#124; where ResourceType="APPLICATIONGATEWAYS" and OperationName=="ApplicationGatewayAccess" |
    | NetworkApplicationgateways &#124; where OperationName=="ApplicationGatewayPerformance" | AzureDiagnostics &#124; where ResourceType=="APPLICATIONGATEWAYS" and OperationName=ApplicationGatewayPerformance |
    | NetworkSecuritygroups | AzureDiagnostics &#124; where ResourceType=="NETWORKSECURITYGROUPS" |

   + Для любого поля, имя которого содержит суффикс \_s, \_d или \_g, переведите первый знак в нижний регистр.
   + Для любого поля, имя которого содержит суффикс \_o, данные разбиваются на отдельные поля на основе имен вложенных полей.
4. Удалите устаревшее решение *Анализ сетевой активности Azure (не рекомендуется)*.
  + Если используется PowerShell, то выполните следующую команду: `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Данные, собранные до этого изменения, не отображаются в новом решении. Эти данные по-прежнему можно запрашивать с помощью старых имен типов и полей.

## <a name="troubleshooting"></a>Устранение неполадок
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Дополнительная информация
* Используйте [поиск по журналам в Log Analytics](log-analytics-log-searches.md) для просмотра подробных данных диагностики Azure.
