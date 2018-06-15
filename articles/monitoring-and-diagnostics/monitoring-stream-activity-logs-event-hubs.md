---
title: Потоковая передача журнала действий Azure в концентраторы событий
description: Узнайте, как настроить потоковую передачу журнала действий Azure в концентраторы событий.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/02/2018
ms.author: johnkem
ms.component: activitylog
ms.openlocfilehash: 1f1a131d4e0cf900d04acc9730b04e1375f396a6
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264305"
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Потоковая передача журнала действий Azure в концентраторы событий
Можно осуществлять потоковую передачу [журнала действий Azure](monitoring-overview-activity-logs.md) почти в реальном времени в любое приложение:

* с помощью встроенной возможности **Экспорт** на портале;
* включив идентификатор правила служебной шины Azure в профиле журнала с помощью командлетов Azure PowerShell или Azure CLI.

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Что можно делать с журналом действий и концентраторами событий
Ниже приведены два способа использования потоковой передачи журнала действий.

* **Потоковая передача в сторонние системы ведения журнала и сбора телеметрии.** Со временем концентраторы событий Azure будут использоваться для передачи журнала действий в сторонние решения SIEM и Log Analytics.
* **Создание пользовательской платформы для телеметрии и ведения журнала.** Если у вас уже есть пользовательская платформа для телеметрии и ведения журнала или вы только планируете ее создать, высокая масштабируемость публикации и подписки концентраторов событий позволит вам гибко настраивать прием журнала действий. Чтобы узнать больше, ознакомьтесь с [видео Дэна Росановы (Dan Rosanova) об использовании концентраторов событий в глобальной платформе телеметрии](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-the-activity-log"></a>Включение потоковой передачи журнала действий
Потоковую передачу журнала действий можно включить программно или с помощью портала. Можно также выбрать пространство имен концентраторов событий и политику общего доступа для этого пространства имен. В этом пространстве имен создается концентратор событий insights-logs-operationallogs, когда происходит первое событие журнала действий. 

Если у вас нет пространства имен концентраторов событий, необходимо сначала создать его. Если потоковая передача событий журнала действий уже выполнялась в это пространство имен концентраторов событий, то созданный ранее концентратор событий будет использован повторно. 

Политика общего доступа определяет разрешения, предоставленные для механизма потоковой передачи. В настоящее время для потоковой передачи журналов в концентраторы событий нужны разрешения **Управление**, **Отправка** и **Прослушивание**. Политики общего доступа для пространства имен концентраторов событий можно создать или изменить на портале Azure, на вкладке **Настройка** соответствующего пространства имен. 

Чтобы изменить профиль журнала действий и разрешить потоковую передачу, пользователь, который вносит изменение, должен иметь разрешение ListKey для соответствующего правила авторизации концентраторов событий. Пространство имен концентраторов событий не обязательно должно находиться в подписке, выдающей журналы. Достаточно, чтобы пользователь, настраивающий этот режим, имел необходимый доступ RBAC к обеим подпискам.

### <a name="via-the-azure-portal"></a>Использование портала Azure
1. Перейдите в раздел **Журнал действий**, используя раздел поиска **Все службы** в левой части портала.
   
   ![Выбор пункта "Журнал действий" из списка служб на портале](./media/monitoring-stream-activity-logs-event-hubs/activity.png)
2. Нажмите кнопку **Экспорт** в верхней части журнала.
   
   ![Кнопка "Экспорт" на портале](./media/monitoring-stream-activity-logs-event-hubs/export.png)

   Обратите внимание на то, что параметры фильтра, которые были применены при просмотре журнала действий в предыдущем представлении, не оказывают влияния на параметры экспорта. Они предназначены исключительно для фильтрации данных, отображаемых при просмотре журналов действий на портале.
3. В открывшемся разделе выберите **Все регионы**. Не выбирайте какие-либо определенные регионы.
   
   ![Раздел "Экспорт"](./media/monitoring-stream-activity-logs-event-hubs/export-audit.png)

   > [!WARNING]  
   > Если выбрать не **Все регионы**, а другие параметры, то вы пропустите ключевые события, которые ожидаете получить. Журнал действий является глобальным (а не региональным), поэтому с большей частью событий не связан какой-либо регион. 
   >

4. Нажмите кнопку **Сохранить**, чтобы сохранить эти параметры. Параметры будут немедленно применены к подписке
5. Если у вас несколько подписок, повторите это действие и отправьте все данные в тот же концентратор событий.

### <a name="via-powershell-cmdlets"></a>Использование командлетов PowerShell
Если профиль журнала уже существует, сначала удалите его, а затем создайте новый.

1. Используйте `Get-AzureRmLogProfile`, чтобы проверить, существует ли профиль журнала.  Если профиль журнала существует, найдите свойство *name*.
2. Удалите журнал профиля с помощью `Remove-AzureRmLogProfile`, используя значение свойства *name*.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzureRmLogProfile -Name "default"
    ```
3. Создайте профиль журнала с помощью `Add-AzureRmLogProfile`:

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzureRmLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespaceName/authorizationrules/RootManageSharedAccessKey"

   Add-AzureRmLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```

### <a name="via-azure-cli"></a>С помощью интерфейса командной строки Azure
Если профиль журнала уже существует, сначала удалите его, а затем создайте новый.

1. Используйте `az monitor log-profiles list`, чтобы проверить, существует ли профиль журнала.
2. Удалите журнал профиля с помощью `az monitor log-profiles delete --name "<log profile name>`, используя значение свойства *name*.
3. Создайте профиль журнала с помощью `az monitor log-profiles create`:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

## <a name="consume-the-log-data-from-event-hubs"></a>Использование данных журнала из концентраторов событий
Схема для журнала действий представлена в разделе [Мониторинг действий подписки с помощью журнала действий Azure](monitoring-overview-activity-logs.md). Каждое событие сохраняется в массиве больших двоичных объектов JSON, которые называются *записями*.

## <a name="next-steps"></a>Дополнительная информация
* [Архивация журнала действий Azure](monitoring-archive-activity-log.md)
* [Общие сведения о журнале действий Azure](monitoring-overview-activity-logs.md)
* [Настройка объекта webhook для оповещений журнала действий Azure](insights-auditlog-to-webhook-email.md)

