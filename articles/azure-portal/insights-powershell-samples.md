<properties
	pageTitle="Azure Insights. Примеры для быстрого запуска Azure Insights с помощью PowerShell | Microsoft Azure"
	description="Примеры команд PowerShell, которые помогут быстро обращаться к функциям мониторинга Azure Insights."
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Примеры для быстрого запуска Azure Insights с помощью PowerShell

В этой статье показаны примеры команд PowerShell, которые помогут быстро обратиться к функциям мониторинга Azure Insights. Azure Insights позволяет выполнять автомасштабирование облачных служб, виртуальных машин и веб-приложений, отправлять оповещения и осуществлять вызов URL-адресов на основе значений настроенных данных телеметрии.

## Настройка PowerShell
Если вы этого еще не сделали, настройте PowerShell для выполнения на своем компьютере. Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

## Примеры в этой статье

Примеры в статье демонстрируют, как можно использовать командлеты Azure Insights. Можно также просмотреть полный список командлетов Azure Insights (для мониторинга) в разделе [Azure Insights Cmdlets](https://msdn.microsoft.com/library/mt282452.aspx).


## Вход в систему и использование подписок

Для начала выполните вход в свою подписку Azure.

```
Login-AzureRmAccount
```

Для этого необходимо войти в систему. После этого отобразятся ваша учетная запись, идентификатор клиента и идентификатор подписки по умолчанию. Все командлеты Azure будут работать в контексте подписки по умолчанию. Чтобы просмотреть список доступных вам подписок, используйте следующую команду.

```
Get-AzureRmSubscription
```

Чтобы сменить рабочий контекст на другую подписку, используйте следующую команду.

```
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## Извлечение журналов аудита для подписки
Используйте командлет `Get-AzureRmLog`. Ниже приведено несколько типичных примеров.

Получение записей журнала, начиная с указанной даты и времени и до настоящего момента.

```
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Получение записей журнала в пределах диапазона дат и времени.

```
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Получение записей журнала для определенной группы ресурсов.

```
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Получение записей журнала от конкретного поставщика ресурсов в пределах диапазона даты и времени.

```
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Получение всех записей журнала для конкретной вызывающей стороны.

```
Get-AzureRmLog -Caller 'myname@company.com'
```

Следующая команда извлекает последние 1000 событий из журнала аудита.

```
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog` поддерживает много других параметров. Дополнительные сведения см. в справке по `Get-AzureRmLog`.

>[AZURE.NOTE] `Get-AzureRmLog` предоставляет данные журнала только за 15 дней. С помощью параметра **-MaxEvents** можно запрашивать N последних событий за 15 дней. Чтобы получить события старше 15 дней, используйте REST API или пакет SDK (пример на C# с использованием пакета SDK). Если не указать **StartTime**, то значением **EndTime** по умолчанию будет минус один час. Если не указать **EndTime**, то значением по умолчанию будет текущее время. Все значения времени указаны в формате UTC.

## Извлечение журнала оповещений
Чтобы просмотреть все события оповещения, можно запросить журналы Azure Resource Manager (ARM), используя следующие примеры.

```
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Чтобы просмотреть журнал для конкретного правила генерации оповещений, можно использовать командлет `Get-AzureRmAlertHistory`, передав идентификатор ресурса этого правила.

```
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

Командлет `Get-AzureRmAlertHistory` поддерживает различные параметры. Дополнительные сведения см. в разделе [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).


## Извлечение информации о правилах генерации оповещений
Все следующие команды оперируют с группой ресурсов montest.

Просмотр всех свойств правила генерации оповещений.

```
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Извлечение всех оповещений о группе ресурсов.

```
Get-AzureRmAlertRule -ResourceGroup montest
```

Извлечение всех правил генерации оповещений для целевого ресурса. Например, можно извлечь все правила генерации оповещений, установленные для виртуальной машины.

```
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule` поддерживает и другие параметры. Дополнительную информацию см. в разделе [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx).

## Создание правил генерации оповещений
С помощью командлета `Add-AlertRule` можно создать, обновить или отключить правило генерации оповещений.

С помощью `New-AzureRmAlertRuleEmail` и `New-AzureRmAlertRuleWebhook` можно создать свойства электронного адреса и веб-перехватчика, соответственно. В командлете правила генерации оповещений назначьте их в качестве действий свойства **Actions** правила генерации оповещений.

Следующий раздел содержит пример, показывающий, как создать правило генерации оповещений с различными параметрами.

### Правило генерации оповещений на основе метрики
В следующей таблице описаны параметры и значения, используемые для создания оповещения с использованием метрики.


|Параметр|value|
|---|---|
|Имя|	simpletestdiskwrite|
|Расположение этого правила генерации оповещений|	Восток США|
|ResourceGroup|	montest|
|TargetResourceId|	/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig|
|MetricName созданного оповещения|	\\PhysicalDisk(\_Total)\\Disk Writes/sec. Чтобы узнать, как извлекать точные имена метрик, см. описание командлета `Get-MetricDefinitions` ниже.| |Оператор| GreaterThan| |Пороговое значение (значений в секунду для этой метрики)| 1| |WindowSize (в формате чч:мм:сс)| 00:05:00| |Статистическое выражение (статистика метрики, в которой в данном случае используется среднее значение)| Average| |Настраиваемые электронные адреса (массив строк)|'foo@example.com','bar@example.com'| |Отправка сообщений владельцам, участникам и читателям| -SendToServiceOwners|

Создание действия электронного сообщения

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Создание действия веб-перехватчика

```
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Создание правила генерации оповещений на основе метрики загруженности ЦП (%) для классической виртуальной машины

```
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Извлечение правила генерации оповещений

```
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Командлет добавления оповещения также обновляет правило, если уже существует правило генерации оповещений с заданными свойствами. Чтобы отключить правило генерации оповещений, необходимо добавить параметр **-DisableRule**.

### Оповещение о событии журнала аудита

>[AZURE.NOTE] Пока что доступна только предварительная версия этой функции.

В этом сценарии после успешного запуска веб-сайта вы отправите сообщение для подписки в группе ресурсов *abhingrgtest123*.

Настройка правила электронной почты

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Настройка правила веб-перехватчика

```
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Создание правила для события

```
Add-AzureRmLogAlertRule -Name superalert1 -Location "East US" -ResourceGroup myrg1 -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup abhingrgtest123 -Actions $actionEmail, $actionWebhook
```

Извлечение правила генерации оповещений

```
Get-AzureRmAlertRule -Name superalert1 -ResourceGroup myrg1 -DetailedOutput
```

Командлет `Add-AlertRule` позволяет указать и другие параметры. Дополнительные сведения см. в разделе [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx).

## Получение списка доступных метрик для оповещений
С помощью командлета `Get-AzureRmMetricDefinition` можно просмотреть список всех метрик для конкретного ресурса.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

В следующем примере создается таблица, содержащая имена метрик и их единицы измерения.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Полный список доступных параметров для `Get-AzureRmMetricDefinition` представлен в разделе [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).

Чтобы просмотреть список доступных метрик и их единиц измерения для различных служб Azure, см. раздел [ЗАГОЛОВОК ВЕБ-СТРАНИЦЫ](http://link).

## Создание параметров автомасштабирования и управление ими
Для ресурса, например веб-приложения, виртуальной машины, облачной службы или набора масштабирования виртуальных машин, можно настроить только один параметр автомасштабирования. Однако у каждого параметра автомасштабирования может быть несколько профилей. Например, один профиль для масштабирования на основе производительности, а второй — на основе расписания. Для каждого профиля можно настроить несколько правил. Дополнительные сведения об автомасштабирования см. в разделе [Автомасштабирование приложения](../cloud-services/cloud-services-how-to-scale.md).

Вот какие действия мы выполним.

1. Создадим правила.
2. Создадим профили для сопоставления с созданными ранее правилами.
3. (Необязательно.) Создадим уведомления для автомасштабирования, настроив свойства веб-перехватчика и электронного адреса.
4. Создадим параметры автомасштабирования с именем целевого ресурса, сопоставив профили и уведомления, созданные на предыдущих шагах.

В следующих примерах показано, как создать параметр автомасштабирования для набора масштабирования виртуальных машины для операционной системы Windows, основанный на метрике использования ЦП.

Сначала создайте правило для развертывания, которое увеличивает количество экземпляров.

```
$rule1 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 0.01 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```		

Затем создайте правило для свертывания, которое уменьшает количество экземпляров.

```
$rule2 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 2 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```

После этого создайте профиль для этих правил.

```
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Создайте действие веб-перехватчика.

```
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Создайте свойство уведомления для параметра автомасштабирования, добавив в него электронный адрес и веб-перехватчик, созданные ранее.

```
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Наконец, создайте параметр автомасштабирования, чтобы добавить в него профиль, который вы создали.

```
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Дополнительные сведения об управлении параметрами автомасштабирования см. в разделе [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## Журнал автомасштабирования
В следующем примере показано, как можно просмотреть последние события автомасштабирования и оповещения. Чтобы просмотреть журнал автомасштабирования, используйте функцию поиска в журналах аудита.

```
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Для получения журнала автомасштабирования можно использовать командлет `Get-AzureRmAutoScaleHistory`.

```
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Дополнительные сведения см. в разделе [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### Просмотр сведений о параметре автомасштабирования
Чтобы получить дополнительные сведения о параметре автомасштабирования, можно использовать командлет `Get-Autoscalesetting`.

В следующем примере отображаются сведения о всех параметрах автомасштабирования в группе ресурсов myrg1.

```
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

В следующем примере отображаются сведения о всех параметрах автомасштабирования в группе ресурсов myrg1, и отдельно — о параметре автомасштабирования MyScaleVMSSSetting.

```
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### Удаление параметра автомасштабирования
Для удаления параметра автомасштабирования можно использовать командлет `Remove-Autoscalesetting`.

```
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## Управление профилями журнала для журналов аудита

Можно создать *профиль журнала* и экспортировать данные из журналов аудита в учетную запись хранения, настроив для них период удержания. При необходимости можно также осуществить потоковую передачу этих данных в концентратор событий. Обратите внимание, что в настоящее время доступна предварительная версия этой функции, и можно создать только один профиль журнала на подписку. Для создания профилями журнала и управления ими можно использовать следующие командлеты для вашей текущей подписки. Можно также выбрать другую подписку. Несмотря на то, что по умолчанию PowerShell использует текущую подписку, ее всегда можно сменить, используя `Set-AzureRmContext`. Вы можете настроить журналы аудита для маршрутизации данных в любую учетную запись хранения или концентратор событий в пределах этой подписки. Данные записываются как файлы больших двоичных объектов в формате JSON.

### Получение профиля журнала
Чтобы получить существующие профили журнала, используйте командлет `Get-AzureRmLogProfile`.

### Добавление профиля журнала без хранения данных

```
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### Удаление профиля журнала

```
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### Добавление профиля журнала с хранением данных

Можно указать свойство **-RetentionInDays** с положительным целым числом дней хранения данных.

```
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### Добавление профиля журнала с периодом удержания и концентратором событий
Помимо направления данных в учетную запись хранения, их также можно в концентратор событий. Обратите внимание, что это предварительная версия, и конфигурация учетной записи хранения является обязательной, а конфигурация концентратора событий — нет.

```
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## Настройка журналов диагностики
Многие службы Azure предоставляют дополнительные журналы и данные телеметрии, в том числе группы безопасности сети Azure, балансировщики нагрузки программного обеспечения, хранилище ключей, службы поиска Azure и приложения логики, и их можно настроить для сохранения данных в вашей учетной записи хранения Azure. Эта операция может выполняться только на уровне ресурсов, и учетная запись хранения должна находиться в том же регионе, что и целевой ресурс, для которого настроены параметры диагностики.

### Получение параметра диагностики

```
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Отключение параметра диагностики

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Включение параметра диагностики без периода удержания

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Включение параметра диагностики с периодом удержания

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Включение параметра диагностики с периодом удержания для определенной категории журналов

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

<!---HONumber=AcomDC_0330_2016-->