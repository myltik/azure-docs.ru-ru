---
title: "Анализ журналов диагностики Azure с помощью Log Analytics | Документация Майкрософт"
description: "Log Analytics может считывать журналы из служб Azure, которые записывают журналы диагностики Azure в хранилище BLOB-объектов в формате JSON."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: adf2f366-ea98-4250-ae66-6d2cfce5b4f9
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 08274c03dd1ebb7533efde4c01744ed5293fb4dd


---
# <a name="analyze-azure-diagnostic-logs-using-log-analytics"></a>Анализ журналов диагностики Azure с помощью Log Analytics
Log Analytics может собирать журналы из следующих служб Azure, которые записывают [журналы диагностики Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) в хранилище BLOB-объектов в формате JSON:

* Автоматизация (предварительная версия).
* Хранилище ключей (предварительная версия).
* Шлюз приложений (предварительная версия).
* Группа безопасности сети (предварительная версия).

В следующих разделах описывается, как использовать PowerShell для выполнения таких действий:

* Настройка службы Log Analytics для сбора журналов из хранилища для каждого ресурса.  
* Включение решения Log Analytics для службы Azure.

Чтобы служба Log Analytics могла собирать данные для этих ресурсов, необходимо включить систему диагностики Azure. Используйте командлет `Set-AzureRmDiagnosticSetting`, чтобы включить ведение журнала.

Дополнительные сведения о включении ведения журналов диагностики см. в следующих статьях:

* [хранилище ключей;](../key-vault/key-vault-logging.md)
* [Шлюз приложений](../application-gateway/application-gateway-diagnostics.md)
* [Группа безопасности сети](../virtual-network/virtual-network-nsg-manage-log.md)

Эта документация также включает сведения по таким темам:

* устранение неполадок при сборе данных;
* прекращение сбора данных.

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Настройка сбора журналов диагностики Azure в службе Log Analytics
Сбор журналов для этих служб и визуализация журналов в решениях выполняется с помощью сценариев PowerShell.

В приведенном ниже примере показано, как настроить ведение журналов по всем поддерживаемым ресурсам.

```
# update to be the storage account that logs will be written to. Storage account must be in the same region as the resource to monitor
# format is similar to "/subscriptions/ec11ca60-ab12-345e-678d-0ea07bbae25c/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
$storageAccountId = ""

$supportedResourceTypes = ("Microsoft.Automation/AutomationAccounts", "Microsoft.KeyVault/Vaults", "Microsoft.Network/NetworkSecurityGroups", "Microsoft.Network/ApplicationGateways")

# update location to match your storage account location
$resources = Get-AzureRmResource | where { $_.ResourceType -in $supportedResourceTypes -and $_.Location -eq "westus" }

foreach ($resource in $resources) {
    Set-AzureRmDiagnosticSetting -ResourceId $resource.ResourceId -StorageAccountId $storageAccountId -Enabled $true -RetentionEnabled $true -RetentionInDays 1
}
```


Для некоторых ресурсов можно выполнить предварительную настройку на портале Azure, выполнив действия, описанные в статье [Обзор журналов диагностики Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs).

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Настройка сбора журналов диагностики Azure в службе Log Analytics
Мы создали модуль сценария PowerShell, который выполняет экспорт двух командлетов для облегчения настройки службы Log Analytics:

1. `Add-AzureDiagnosticsToLogAnalyticsUI` предложит ввести входные данные и поможет выполнить простые настройки.
2. `Add-AzureDiagnosticsToLogAnalytics` использует ресурсы для отслеживания в качестве входных данных, а затем настраивает службу Log Analytics.  

### <a name="pre-requisites"></a>Предварительные требования
1. Azure PowerShell с командлетами оперативной аналитики версии 1.0.8 или более поздней.
   * [Установка и настройка Azure PowerShell](../powershell-install-configure.md)
   * Проверьте версию командлетов: `Import-Module AzureRM.OperationalInsights -MinimumVersion 1.0.8 `
2. Для ресурсов Azure , которые вы хотите отслеживать, можно настроить ведение журналов диагностики. Используйте `Set-AzureRmDiagnosticSetting`. Дополнительные сведения о включении диагностики см. в статье [Use Log Analytics to collect data from Azure storage accounts](log-analytics-azure-storage.md) (Сбор данных из учетных записей хранения Azure с помощью Log Analytics).
3. Рабочая область [Log Analytics](https://portal.azure.com/#create/Microsoft.LogAnalyticsOMS) .  
4. Модуль PowerShell AzureDiagnosticsAndLogAnalytics.
   * Скачайте модуль [AzureDiagnosticsAndLogAnalytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/) из коллекции PowerShell.

### <a name="option-1-run-the-interactive-configuration-scripts"></a>Вариант 1: выполнить сценарии интерактивной конфигурации
Откройте PowerShell и выполните:

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Run the UI configuration script
Add-AzureDiagnosticsToLogAnalyticsUI

```

Отобразится список доступных вариантов и будет предложено сделать выбор.
Будет предложено сделать выбор для каждого из следующих значений:

* Типы ресурсов (службы Azure), из которых следует собирать данные журналов.
* Экземпляры ресурсов, из которых следует собирать данные журналов.
* Рабочая область Log Analytics, которая будет собирать данные.

После выполнения этого сценария в службе Log Analytics должны отобразиться записи, примерно через 30 минут после записи новых диагностических данных в хранилище. Если по истечении этого времени записи не отобразились, обратитесь к разделу по устранению неполадок далее в этой статье.

### <a name="option-2-build-a-list-of-resources-and-pass-them-to-the-configuration-cmdlet"></a>Вариант 2: создать список ресурсов и передать его в командлет конфигурации
Будет создан список ресурсов, для которых включена система диагностики Azure, а затем эти ресурсы будут переданы в командлет конфигурации.

Дополнительные сведения о командлете можно просмотреть, запустив `Get-Help Add-AzureDiagnosticsToLogAnalytics`.

Дополнительные сведения об OMS см. в статье [Командлеты PowerShell для Log Analytics](https://msdn.microsoft.com/library/mt188224.aspx).

> [!NOTE]
> Если ресурс и рабочая область находятся в разных в подписках Azure, при необходимости можно переключаться между ними, используя `Select-AzureRmSubscription -SubscriptionId <Subscription the resource is in>`.
> 
> 

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Update the values below with the name of the resource that has Azure diagnostics enabled and the name of your Log Analytics workspace
$resourceName = "***example-resource***"
$workspaceName = "***log-analytics-workspace***"

# Find the resource to monitor:
$resource = Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" -ResourceNameContains $resourceName

# Find the Log Analytics workspace to configure, for example:
$workspace = Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces" -ResourceNameContains $workspaceName

# Perform configuration
Add-AzureDiagnosticsToLogAnalytics $resource $workspace

# Enable the Log Analytics solution
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -intelligencepackname KeyVault -Enabled $true

```
После выполнения этого сценария в службе Log Analytics должны отобразиться записи, примерно через 30 минут после записи новых диагностических данных в хранилище. Если по истечении этого времени записи не отобразились, обратитесь к разделу по устранению неполадок далее в этой статье.  

> [!NOTE]
> Эта конфигурация не отображается на портале Azure. Можно проверить конфигурацию с помощью командлета `Get-AzureRmOperationalInsightsStorageInsight` .  
> 
> 

## <a name="stopping-log-analytics-from-collecting-azure-diagnostic-logs"></a>Прекращение сбора журналов диагностики Azure в службе Log Analytics
Чтобы удалить конфигурацию Log Analytics для определенного ресурса, воспользуйтесь командлетом `Remove-AzureRmOperationalInsightsStorageInsight`.

## <a name="troubleshooting-configuration-for-azure-diagnostic-logs"></a>Устранение неполадок конфигурации для журналов диагностики Azure
*Проблема*

При настройке ресурса, данные которого регистрируются в классическом хранилище, отображается следующая ошибка:

```
Select-AzureSubscription : The subscription id 7691b0d1-e786-4757-857c-7360e61896c3 doesn't exist.

Parameter name: id
```

*Способы устранения:*

Войдите в API для классической модели развертывания, используя `Add-AzureAccount`.

## <a name="troubleshooting-data-collection-for-azure-diagnostic-logs"></a>Устранение неполадок при сборе данных для журналов диагностики Azure
Если данные для ресурса Azure не отображаются в службе Log Analytics, выполните следующие шаги по устранению неполадок:

* Убедитесь, что данные передаются в учетную запись хранения.
* Убедитесь, что решение Log Analytics включено для данной службы.
* Убедитесь, что служба Log Analytics настроена для чтения данных из хранилища.
* Обновите ключ учетной записи хранения.

### <a name="verify-data-is-flowing-to-the-storage-account"></a>Убедитесь, что данные передаются в учетную запись хранения
С помощью инструмента, позволяющего просмотр хранилища Azure (например, Visual Studio), или PowerShell можно проверить, передаются ли данные в учетную запись хранения.

Чтобы найти учетную запись хранения, которую ресурс должен использовать для входа, воспользуйтесь следующей командой PowerShell:

`Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" | select ResourceId | Get-AzureRmDiagnosticSetting `

Контейнер хранилища, используемый системой диагностики Azure, имеет имя в формате:  

`insights-logs-<Category>`

Дополнительные сведения о просмотре содержимого учетной записи хранения см. в статье [Using storage cmdlets to check a container for recent data](../storage/storage-powershell-guide-full.md) (Проверка наличия недавних данных в контейнере с помощью командлетов хранилища).

### <a name="verify-the-log-analytics-solution-for-the-service-is-enabled"></a>Убедитесь, что решение Log Analytics включено для данной службы.
Если используется `Add-AzureDiagnosticsToLogAnalyticsUI`, то правильное решение Log Analytics включается автоматически.

Чтобы проверить, включено ли решение, выполните следующую команду PowerShell:

`Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName`

Если решение не включено, его можно включить с помощью следующей команды PowerShell:

`Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName -IntelligencePackName $solution -Enabled $true`

Чтобы найти имя решения для каждого типа ресурсов, воспользуйтесь следующей командой PowerShell (эта переменная становится доступной после импорта модуля):

`$MonitorableResourcesToOMSSolutions`

### <a name="verify-that-log-analytics-is-configured-to-read-from-storage"></a>Убедитесь, что служба Log Analytics настроена для чтения данных из хранилища.
При добавлении дополнительных ресурсов Azure необходимо включить для них ведение журнала диагностики, а также настроить службу Log Analytics для работы с ними.
Чтобы выяснить, для каких ресурсов и учетных записей хранения настроена собирать журналы служба Log Analytics, воспользуйтесь следующей командой PowerShell:

```
# Find the Workspace ResourceGroup and Name
$logAnalyticsWorkspace = Get-AzureRmOperationalInsightsWorkspace

#Get the configuration for all resources:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name

#Get the just the resources configured:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name | select Containers
```

### <a name="update-the-storage-key"></a>Обновите ключ учетной записи хранения
Если изменить ключ для учетной записи хранения, конфигурацию Log Analytics также требуется обновить с помощью нового ключа.
Чтобы обновить конфигурацию Log Analytics с помощью нового ключа, воспользуйтесь следующей командой PowerShell:

`Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name –Name <Storage Insight Name> -StorageAccountKey $newKey `

Чтобы найти значение Storage Insight Name, воспользуйтесь командлетом `Get-AzureRmOperationalInsightsStorageInsight` , как показано в предыдущих примерах.

## <a name="next-steps"></a>Дальнейшие действия
* [Используйте хранилище BLOB-объектов для IIS и хранилище таблиц для событий](log-analytics-azure-storage-iis-table.md), чтобы считывать журналы служб Azure, которые записывают диагностические данные в табличное хранилище, или журналы IIS, записанные в хранилище BLOB-объектов.
* [Включите решения](log-analytics-add-solutions.md) , чтобы обеспечить глубокое понимание данных.
* [Воспользуйтесь запросами поиска](log-analytics-log-searches.md) для анализа данных.




<!--HONumber=Nov16_HO3-->


