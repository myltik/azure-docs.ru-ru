---
title: Пересылка данных отчетов Azure Automation DSC в Log Analytics
description: В этой статье показано, как отправлять данные отчетов Desired State Configuration (DSC) в Log Analytics для получения дополнительных сведений и возможностей управления.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: ac0da2b73341f0c6ed4f1e99e077996525890ab7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="forward-azure-automation-dsc-reporting-data-to-log-analytics"></a>Пересылка данных отчетов Azure Automation DSC в Log Analytics

Служба автоматизации может отправлять данные о состоянии узла DSC в рабочую область Log Analytics.  
Сведения о состоянии соответствия для узлов и отдельных ресурсов DSC в конфигурации узла можно просмотреть на портале Azure или с помощью PowerShell. С помощью Log Analytics можно:

* получать сведения о соответствии требованиям для управляемых узлов и отдельных ресурсов;
* активировать сообщение электронной почты или предупреждение (в зависимости от состояния соответствия);
* создавать сложные запросы к управляемым узлам;
* сопоставлять состояние соответствия в учетных записях службы автоматизации;
* визуализировать журнал соответствия узла с течением времени.

## <a name="prerequisites"></a>предварительным требованиям

Чтобы начать отправку отчетов Automation DSC в Log Analytics, необходимо следующее:

* Выпуск за ноябрь 2016 года или более поздний выпуск [Azure PowerShell](/powershell/azure/overview) (вер. 2.3.0).
* Учетная запись службы автоматизации Azure. Дополнительные сведения см. в статье [Приступая к работе со службой автоматизации Azure](automation-offering-get-started.md)
* Рабочая область Log Analytics с предложением службы **Автоматизация и управление**. Дополнительные сведения см. в статье [Начало работы с Log Analytics](../log-analytics/log-analytics-get-started.md).
* Как минимум один узел Azure Automation DSC. Дополнительные сведения см. в статье [Подключение компьютеров для управления с помощью Azure Automation DSC](automation-dsc-onboarding.md).

## <a name="set-up-integration-with-log-analytics"></a>Настройка интеграции с Log Analytics

Чтобы начать импорт данных из Azure Automation DSC в Log Analytics, выполните следующие действия:

1. Войдите в свою учетную запись Azure в PowerShell. См. статью [Вход с помощью Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.0.0).
1. Получите идентификатор _ResourceId_ для учетной записи службы автоматизации, выполнив следующую команду PowerShell (при наличии нескольких учетных записей службы автоматизации выберите _ResourceID_ для учетной записи, которую требуется настроить):

  ```powershell
  # Find the ResourceId for the Automation Account
  Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
  ```
1. Получите идентификатор _ResourceId_ для рабочей области Log Analytics, выполнив следующую команду PowerShell (при наличии нескольких рабочих областей выберите _ResourceID_ для рабочей области, которую требуется настроить):

  ```powershell
  # Find the ResourceId for the Log Analytics workspace
  Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
  ```
1. Выполните следующую команду PowerShell, заменив `<AutomationResourceId>` и `<WorkspaceResourceId>` значениями _ResourceId_ из предыдущих шагов:

  ```powershell
  Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Categories "DscNodeStatus"
  ```

Чтобы остановить импорт данных из Azure Automation DSC в Log Analytics, выполните следующую команду PowerShell:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Categories "DscNodeStatus"
```

## <a name="view-the-dsc-logs"></a>Просмотр журналов DSC

Когда интеграция с Log Analytics для данных Automation DSC будет настроена, отобразится кнопка **Поиск по журналам** в колонке **Узлы DSC** в учетной записи службы автоматизации. Нажмите кнопку **Поиск по журналам**, чтобы просмотреть журналы для данных узла DSC.

![Кнопка поиска по журналам](media/automation-dsc-diagnostics/log-search-button.png)

Откроется колонка **Поиск по журналам**, и для каждого узла DSC отобразятся данные операции **DscNodeStatusData**. Для каждого [ресурса DSC](https://msdn.microsoft.com/powershell/dsc/resources), вызванного в конфигурации такого узла, отобразятся данные операции **DscResourceStatusData**.

Для операции **DscResourceStatusData** отображаются сведения об ошибках при сбое в работе любых ресурсов DSC.

Щелкните каждую операцию в списке, чтобы просмотреть сведения о ней.

Кроме того, можно просмотреть журналы, выполнив поиск в Log Analytics. См. статью [Поиск данных по журналам](../log-analytics/log-analytics-log-searches.md).
Чтобы найти журналы DSC, введите запрос `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category = "DscNodeStatus"`

Можно сузить запрос, использовав имя операции. Например: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category = "DscNodeStatus" OperationName = "DscNodeStatusData"

### <a name="send-an-email-when-a-dsc-compliance-check-fails"></a>Отправка сообщения электронной почты при сбое проверки соответствия DSC

Наши клиенты часто интересуются возможностью отправки электронного сообщения или SMS-сообщения при возникновении ошибки в конфигурации DSC.   

Чтобы создать правило генерации оповещений, начните с создания поиска по журналам для записей отчетов DSC, которые должны вызывать оповещение.  Щелкните кнопку **Оповещение**, чтобы создать и настроить правило генерации оповещений.

1. На странице обзора Log Analytics щелкните **Поиск по журналу**.
1. Поищите по журналам свое оповещение при помощи следующего поискового запроса: `Type=AzureDiagnostics Category=DscNodeStatus NodeName_s=DSCTEST1 OperationName=DscNodeStatusData ResultType=Failed`

  Если вы настроили для рабочей области журналы из более чем одной учетной записи службы автоматизации или подписки, то можете группировать оповещения по подписке или учетной записи службы автоматизации.  
  Имя учетной записи службы автоматизации можно получить из поля "Ресурс" для поискового запроса DscNodeStatusData.  
1. Чтобы открыть экран **Добавить правило оповещения**, щелкните **Оповещение** в верхней части страницы. Дополнительные сведения о параметрах для настройки оповещения см. в разделе [Правила оповещения](../log-analytics/log-analytics-alerts.md#alert-rules).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Поиск ресурсов DSC со сбоями по всем узлам

Одно из преимуществ Log Analytics — возможность поиска проверок со сбоями по узлам.
Чтобы найти все экземпляры ресурсов DSC со сбоями, выполните следующие действия:

1. На странице обзора Log Analytics щелкните **Поиск по журналу**.
1. Поищите по журналам свое оповещение при помощи следующего поискового запроса: `Type=AzureDiagnostics Category=DscNodeStatus OperationName=DscResourceStatusData ResultType=Failed`

### <a name="view-historical-dsc-node-status"></a>Просмотр состояния узла DSC за предыдущие периоды

Наконец, вам может понадобиться визуализировать изменение состояния узла DSC с течением времени.  
Для поиска состояния узла DSC за предыдущие периоды можно использовать следующий запрос:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

При этом отобразится диаграмма состояния узла с течением времени.

## <a name="log-analytics-records"></a>Записи Log Analytics

При диагностике службы автоматизации Azure в Log Analytics создаются записи двух категорий.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Свойство | ОПИСАНИЕ |
| --- | --- |
| TimeGenerated |Дата и время запуска проверки соответствия. |
| OperationName |DscNodeStatusData |
| ResultType |Подтверждено ли соответствие узла. |
| NodeName_s |Имя управляемого узла. |
| NodeComplianceStatus_s |Подтверждено ли соответствие узла. |
| DscReportStatus |Успешно ли прошла проверка соответствия. |
| ConfigurationMode | Применение конфигурации к узлу. Возможные значения: __ApplyOnly__, __ApplyandMonitior__ и __ApplyandAutoCorrect__. <ul><li>__ApplyOnly__ — DSC применяет конфигурацию и не выполняет дальнейшие действия, пока новая конфигурация не будет отправлена в целевой узел или получена с сервера. После первоначального применения новой конфигурации DSC не проверяет наличие отклонений от ранее настроенного состояния. Перед вступлением в силу __ApplyOnly__ DSC пытается применить конфигурацию, пока это не будет успешно реализовано. </li><li> __ApplyAndMonitor__ — значение по умолчанию. LCM применяет любую новую конфигурацию. Если после начального применения новой конфигурации состояние целевого узла отклоняется от требуемого, DSC сообщает о расхождении в журналах. Перед вступлением в силу __ApplyAndMonitor__ DSC пытается применить конфигурацию, пока это не будет успешно реализовано.</li><li>__ApplyAndAutoCorrect__ — DSC применяет любые новые конфигурации. Если после начального применения новой конфигурации состояние целевого узла отклоняется от требуемого, DSC сообщает о расхождении в журналах, а затем повторно применяет текущую конфигурацию.</li></ul> |
| HostName_s | Имя управляемого узла. |
| IPAddress | Адрес IPv4 управляемого узла. |
| Категория | DscNodeStatus |
| Ресурс | Имя учетной записи службы автоматизации Azure. |
| Tenant_g | GUID, идентифицирующий клиента для вызывающего объекта. |
| NodeId_g |Идентификатор GUID для определения управляемого узла. |
| DscReportId_g |Идентификатор GUID для определения отчета. |
| LastSeenTime_t |Дата и время последнего просмотра отчета. |
| ReportStartTime_t |Дата и время начала отчета. |
| ReportEndTime_t |Дата и время завершения отчета. |
| NumberOfResources_d |Количество ресурсов DSC, которые вызывались в примененной к узлу конфигурации. |
| SourceSystem | Способ сбора данных в Log Analytics. Всегда имеет значение *Azure* для системы диагностики Azure. |
| ResourceId |Указывает учетную запись службы автоматизации Azure. |
| ResultDescription | Описание для этой операции. |
| SubscriptionId | Идентификатор подписки Azure (GUID) для учетной записи службы автоматизации. |
| ResourceGroup | Имя группы ресурсов для учетной записи службы автоматизации. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |Идентификатор GUID для корреляции отчета о соответствии. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Свойство | ОПИСАНИЕ |
| --- | --- |
| TimeGenerated |Дата и время запуска проверки соответствия. |
| OperationName |DscResourceStatusData|
| ResultType |Подтверждено ли соответствие ресурса. |
| NodeName_s |Имя управляемого узла. |
| Категория | DscNodeStatus |
| Ресурс | Имя учетной записи службы автоматизации Azure. |
| Tenant_g | GUID, идентифицирующий клиента для вызывающего объекта. |
| NodeId_g |Идентификатор GUID для определения управляемого узла. |
| DscReportId_g |Идентификатор GUID для определения отчета. |
| DscResourceId_s |Имя для экземпляра ресурса DSC. |
| DscResourceName_s |Имя ресурса DSC. |
| DscResourceStatus_s |Подтверждено ли соответствие ресурса DSC. |
| DscModuleName_s |Имя модуля PowerShell, который содержит ресурс DSC. |
| DscModuleVersion_s |Версия модуля PowerShell, который содержит ресурс DSC. |
| DscConfigurationName_s |Имя примененной к узлу конфигурации. |
| ErrorCode_s | Код ошибки при сбое ресурса. |
| ErrorMessage_s |Сообщение об ошибке при сбое ресурса. |
| DscResourceDuration_d |Период выполнения ресурса DSC в секундах. |
| SourceSystem | Способ сбора данных в Log Analytics. Всегда имеет значение *Azure* для системы диагностики Azure. |
| ResourceId |Указывает учетную запись службы автоматизации Azure. |
| ResultDescription | Описание для этой операции. |
| SubscriptionId | Идентификатор подписки Azure (GUID) для учетной записи службы автоматизации. |
| ResourceGroup | Имя группы ресурсов для учетной записи службы автоматизации. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |Идентификатор GUID для корреляции отчета о соответствии. |

## <a name="summary"></a>Сводка

Отправляя данные о состоянии Automation DSC в Log Analytics, можно лучше понять, в каком состоянии находятся ваши узлы Automation DSC. Для этого вы можете:

* настроить оповещения, уведомляющие вас о проблемах;
* с помощью пользовательских представлений и поисковых запросов визуализировать результаты модуля Runbook, состояние задания Runbook и другие связанные ключевые индикаторы или метрики.  

Log Analytics предоставляет расширенный оперативный контроль над данными Automation DSC и позволяет быстрее устранять инциденты.  

## <a name="next-steps"></a>Дополнительная информация

* Чтобы узнать больше о том, как создавать различные поисковые запросы и просматривать журналы Automation DSC с помощью Log Analytics, см. статью [Поиск данных по журналам](../log-analytics/log-analytics-log-searches.md).
* Дополнительные сведения об использовании Azure Automation DSC см. в статье [Приступая к работе с DSC службы автоматизации Azure](automation-dsc-getting-started.md).
* Чтобы узнать больше о Log Analytics и источниках собираемых данных, ознакомьтесь с разделом [Подключение службы Azure к Log Analytics](../log-analytics/log-analytics-azure-storage.md).

