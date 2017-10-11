---
title: "Примеры для быстрого запуска Azure Monitor с помощью PowerShell. | Документация Майкрософт"
description: "Используйте PowerShell для доступа к функциям Azure Monitor, например автоматическому масштабированию, оповещениям, объектам webhook и поиску в журналах действий."
author: kamathashwin
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c0761814-7148-4ab5-8c27-a2c9fa4cfef5
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: ashwink
ms.openlocfilehash: 48f064884c2a6d0a55cc58a44169ed03c62de46d
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2017
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Примеры для быстрого запуска Azure Monitor с помощью PowerShell
В этой статье показаны примеры команд PowerShell, с помощью которых можно быстро получить доступ к функциям Azure Monitor. Azure Monitor позволяет выполнять автомасштабирование облачных служб, виртуальных машин и веб-приложений, отправлять оповещения и осуществлять вызов URL-адресов на основе значений настроенных данных телеметрии.

> [!NOTE]
> Azure Monitor — это новое название для Azure Insights, актуальное с 25 сентября 2016 г. При этом пространства имен и соответствующие команды будут по-прежнему содержать фрагмент старого названия (insights).
> 
> 

## <a name="set-up-powershell"></a>Настройка PowerShell
Если вы этого еще не сделали, настройте PowerShell для выполнения на своем компьютере. Дополнительные сведения см. в разделе [Общие сведения об Azure PowerShell](/powershell/azure/overview).

## <a name="examples-in-this-article"></a>Примеры в этой статье
Примеры в статье демонстрируют, как можно использовать командлеты Azure Monitor. Можно также просмотреть полный список командлетов PowerShell (для мониторинга) в документации [Azure Monitor Cmdlets](https://msdn.microsoft.com/library/azure/mt282452#40v=azure.200#41.aspx).

## <a name="sign-in-and-use-subscriptions"></a>Вход в систему и использование подписок
Сначала войдите в свою подписку Azure.

```PowerShell
Login-AzureRmAccount
```

Для этого необходимо войти в систему. После этого вы увидите свою учетную запись, идентификатор клиента и идентификатор подписки по умолчанию. Все командлеты Azure будут работать в контексте подписки по умолчанию. Чтобы просмотреть список доступных вам подписок, используйте следующую команду.

```PowerShell
Get-AzureRmSubscription
```

Чтобы сменить рабочий контекст на другую подписку, используйте следующую команду.

```PowerShell
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Получение журнала действий для подписки
Используйте командлет `Get-AzureRmLog` .  Ниже приведено несколько типичных примеров.

Получение записей журнала, начиная с указанной даты и времени и до настоящего момента.

```PowerShell
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Получение записей журнала в пределах диапазона дат и времени.

```PowerShell
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Получение записей журнала для определенной группы ресурсов.

```PowerShell
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Получение записей журнала от конкретного поставщика ресурсов в пределах диапазона даты и времени.

```PowerShell
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Получение всех записей журнала для конкретной вызывающей стороны.

```PowerShell
Get-AzureRmLog -Caller 'myname@company.com'
```

Следующая команда извлекает последние 1000 событий из журнала:

```PowerShell
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog` поддерживает много других параметров. Дополнительные сведения см. в справке по `Get-AzureRmLog`.

> [!NOTE]
> `Get-AzureRmLog` предоставляет данные журнала только за 15 дней. С помощью параметра **-MaxEvents** можно запрашивать N последних событий за 15 дней. Чтобы получить события старше 15 дней, используйте REST API или пакет SDK (пример на C# с использованием пакета SDK). Если не указать **StartTime**, то значением **EndTime** по умолчанию будет минус один час. Если не указать **EndTime**, то значением по умолчанию будет текущее время. Все значения времени указаны в формате UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Извлечение журнала оповещений
Чтобы просмотреть все события оповещения, можно запросить журналы Azure Resource Manager, используя следующие примеры.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Чтобы просмотреть журнал для конкретного правила генерации оповещений, можно использовать командлет `Get-AzureRmAlertHistory` , передав идентификатор ресурса этого правила.

```PowerShell
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

Командлет `Get-AzureRmAlertHistory` поддерживает различные параметры. Дополнительные сведения см. в документации командлета [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>Извлечение информации о правилах генерации оповещений
Все следующие команды оперируют с группой ресурсов montest.

Просмотр всех свойств правила генерации оповещений.

```PowerShell
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Извлечение всех оповещений о группе ресурсов.

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest
```

Извлечение всех правил генерации оповещений для целевого ресурса. Например, можно извлечь все правила генерации оповещений, установленные для виртуальной машины.

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule` поддерживает и другие параметры. Дополнительную информацию см. в документации [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx).

## <a name="create-metric-alerts"></a>Создание оповещений о метриках
С помощью командлета `Add-AlertRule` можно создать, обновить или отключить правило генерации оповещений.

С помощью `New-AzureRmAlertRuleEmail` и `New-AzureRmAlertRuleWebhook` можно создать свойства электронного адреса и веб-перехватчика, соответственно. В командлете правила генерации оповещений назначьте их в качестве действий свойства **Actions** правила генерации оповещений.

В следующей таблице описаны параметры и значения, используемые для создания оповещения с использованием метрики.

| Параметр | value |
| --- | --- |
| Имя |simpletestdiskwrite |
| Расположение этого правила генерации оповещений |Восток США |
| ResourceGroup |montest |
| TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| MetricName созданного оповещения |\PhysicalDisk (_Total) \Disk операции записи в секунду. В разделе `Get-MetricDefinitions` командлет о том, как получить точные имена метрик |
| operator |GreaterThan |
| Пороговое значение (число/с для этой метрики) |1 |
| WindowSize (в формате чч:мм:сс) |00:05:00 |
| агрегатор (статистические данные о метрике — в этом случае при использовании среднего значения) |Средняя |
| пользовательские сообщения электронной почты (строковый массив) |'foo@example.com','bar@example.com' |
| отправка сообщений электронной почты владельцам, участникам и читателям |-SendToServiceOwners |

Создание действия электронного сообщения

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Создание действия веб-перехватчика

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Создание правила генерации оповещений на основе метрики загруженности ЦП (%) для классической виртуальной машины

```PowerShell
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Извлечение правила генерации оповещений

```PowerShell
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Командлет добавления оповещения также обновляет правило, если уже существует правило генерации оповещений с заданными свойствами. Чтобы отключить правило оповещения, необходимо добавить параметр **-DisableRule**.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Получение списка доступных метрик для оповещений
С помощью командлета `Get-AzureRmMetricDefinition` можно просмотреть список всех метрик для конкретного ресурса.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

В следующем примере создается таблица, содержащая имена метрик и их единицы измерения.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Полный список доступных параметров для `Get-AzureRmMetricDefinition` представлен в разделе [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).

## <a name="create-and-manage-autoscale-settings"></a>Создание параметров автомасштабирования и управление ими
Для ресурса, например веб-приложения, виртуальной машины, облачной службы или масштабируемого набора виртуальных машин, можно настроить только один параметр автомасштабирования.
Однако у каждого параметра автомасштабирования может быть несколько профилей. Например, один профиль для масштабирования на основе производительности, а второй — на основе расписания. Для каждого профиля можно настроить несколько правил. Дополнительные сведения об автомасштабировании см. в статье [Автомасштабирование облачной службы](../cloud-services/cloud-services-how-to-scale.md).

Вот какие действия мы выполним.

1. Создадим правила.
2. Создадим профили для сопоставления с созданными ранее правилами.
3. (Необязательно.) Создадим уведомления для автомасштабирования, настроив свойства веб-перехватчика и электронного адреса.
4. Создадим параметры автомасштабирования с именем целевого ресурса, сопоставив профили и уведомления, созданные на предыдущих шагах.

В следующих примерах показано, как создать параметр автомасштабирования для масштабируемого набора виртуальных машин для операционной системы Windows, основанный на метрике использования ЦП.

Сначала создайте правило для развертывания, которое увеличивает количество экземпляров.

```PowerShell
$rule1 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Затем создайте правило для свертывания, которое уменьшает количество экземпляров.

```PowerShell
$rule2 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

После этого создайте профиль для этих правил.

```PowerShell
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Создайте действие веб-перехватчика.

```PowerShell
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Создайте свойство уведомления для параметра автомасштабирования, добавив в него электронный адрес и веб-перехватчик, созданные ранее.

```PowerShell
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Наконец, создайте параметр автомасштабирования, чтобы добавить в него профиль, который вы создали.

```PowerShell
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Дополнительные сведения об управлении параметрами автомасштабирования см. в документации [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Журнал автомасштабирования
В следующем примере показано, как можно просмотреть последние события автомасштабирования и оповещения. Используйте поиск по журналу событий, чтобы просматривать историю автомасштабирования.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Для получения журнала автомасштабирования можно использовать командлет `Get-AzureRmAutoScaleHistory` .

```PowerShell
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Дополнительные сведения см. в документации [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Просмотр сведений о параметре автомасштабирования
Чтобы получить дополнительные сведения о параметре автомасштабирования, можно использовать командлет `Get-Autoscalesetting` .

В следующем примере отображаются сведения о всех параметрах автомасштабирования в группе ресурсов myrg1.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

В следующем примере отображаются сведения о всех параметрах автомасштабирования в группе ресурсов myrg1, и отдельно — о параметре автомасштабирования MyScaleVMSSSetting.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Удаление параметра автомасштабирования
Для удаления параметра автомасштабирования можно использовать командлет `Remove-Autoscalesetting` .

```PowerShell
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Управление профилями журнала действий
Вы можете создать *профиль журнала*, экспортировать данные из журнала действий в учетную запись хранения и настроить для них период хранения. При необходимости можно также осуществить потоковую передачу этих данных в концентратор событий. Обратите внимание, что в настоящее время доступна предварительная версия этой функции и можно создать только один профиль журнала на подписку. Чтобы создать профили журнала и управлять ими, используйте следующие командлеты в текущей подписке. Вы также можете выбрать другую подписку. Хотя по умолчанию PowerShell использует текущую подписку, ее всегда можно сменить с помощью командлета `Set-AzureRmContext`. Вы можете настроить журнал действий для маршрутизации данных в любую учетную запись хранения или концентратор событий в пределах этой подписки. Данные записываются как файлы больших двоичных объектов в формате JSON.

### <a name="get-a-log-profile"></a>Получение профиля журнала
Чтобы получить существующие профили журнала, используйте командлет `Get-AzureRmLogProfile` .

### <a name="add-a-log-profile-without-data-retention"></a>Добавление профиля журнала без хранения данных
```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Удаление профиля журнала
```PowerShell
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Добавление профиля журнала с хранением данных
Можно указать свойство **-RetentionInDays** с положительным целым числом дней хранения данных.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Добавление профиля журнала с периодом удержания и концентратором событий
Помимо направления данных в учетную запись хранения, их также можно в концентратор событий. Обратите внимание, что это предварительная версия, и конфигурация учетной записи хранения является обязательной, а конфигурация концентратора событий — нет.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Настройка журналов диагностики
Многие службы Azure предоставляют дополнительные журналы и данные телеметрии. Их можно настроить соответствующим образом, чтобы сохранять в учетной записи хранения Azure, отправлять в концентраторы событий и (или) отправлять в рабочую область OMS Log Analytics. Эта операция может выполняться только на уровне ресурса, и учетная запись хранения или концентратор событий должны находиться в том же регионе, что и целевой ресурс, для которого настроены параметры диагностики.

### <a name="get-diagnostic-setting"></a>Получение параметра диагностики
```PowerShell
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Отключение параметра диагностики

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Включение параметра диагностики без периода удержания

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Включение параметра диагностики с периодом удержания

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Включение параметра диагностики с периодом удержания для определенной категории журналов

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Включение параметра диагностики для концентратора событий

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Включение параметра диагностики для OMS

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId 76d785fd-d1ce-4f50-8ca3-858fc819ca0f -Enabled $true

```
