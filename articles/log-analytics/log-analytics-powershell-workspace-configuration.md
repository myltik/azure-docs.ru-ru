<properties
	pageTitle="Использование PowerShell для создания и настройки рабочей области Log Analytics | Microsoft Azure"
	description="Log Analytics использует данные с серверов в вашей локальной или облачной инфраструктуре. При генерировании системой диагностики Azure можно брать данные компьютера из хранилища Azure."
	services="log-analytics"
	documentationCenter=""
	authors="richrundmsft"
	manager="jochan"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="powershell"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="richrund"/>

# Управление Log Analytics с помощью PowerShell

[Командлеты PowerShell Log Analytics](http://msdn.microsoft.com/library/mt188224.aspx) можно использовать для выполнения различных функций в Log Analytics как из командной строки, так и в составе сценария. Примеры задач, которые можно выполнять с помощью PowerShell.

+ Создание рабочей области
+ Добавление или удаление решения
+ Импорт и экспорт сохраненных поисков
+ Добавление агента Log Analytics в виртуальную машину Azure
+ Настройка Log Analytics для индексирования данных, собранных системой диагностики Azure

Эта статья содержит два примера кода, иллюстрирующих некоторые доступные в PowerShell функции. Сведения о других функциях см. в [справочнике по командлетам PowerShell Log Analytics](http://msdn.microsoft.com/library/mt188224.aspx).

> [AZURE.NOTE] Компонент Log Analytics раньше назывался Operational Insights, именно поэтому данное имя и используется в командлетах.

## Предварительные требования

Чтобы использовать PowerShell с рабочей областью Log Analytics, необходимо следующее.

+ Подписка Azure. 
+ Рабочая область Azure Log Analytics, связанная с вашей подпиской Azure.

Если вы создали рабочую область OMS, но еще не связали ее с подпиской Azure, такую связь можно выполнить на портале Azure, портале OMS или с помощью командлетов Get-AzureRMOperationalInsightsLinkTargets и New-AzureRMOperationalInsightsWorkspace.

## Создание рабочей области Log Analytics, добавление решений и сохраненных поисков

Этот пример сценария иллюстрирует следующие задачи.

1.	Создание рабочей области
2.	Вывод списка доступных решений
3.	Добавление решений в рабочую область
4.	Импорт сохраненных поисков
5.	Экспорт сохраненных поисков

```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {        
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName="C:" CounterName="Current Disk Queue Length"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($soln in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $soln -Enabled $true
}

#List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

```

## Настройка Log Analytics для индексации системы диагностики Azure 

Для безагентного отслеживания ресурсов Azure, включая веб-роли и рабочие роли, кластеры Service Fabric, группы безопасности сети, хранилища ключей и шлюзы приложений, сначала следует разрешить системе диагностики Azure запись в учетную запись хранения, после чего можно настроить Log Analytics для сбора журналов из учетной записи хранения.

Можно также использовать PowerShell, чтобы настроить рабочую область Log Analytics в одной подписке Azure для сбора журналов из других подписок Azure.

В приведенном ниже примере показано, как выполнить следующие задачи.

1.	Вывод списка существующих учетных записей хранения и расположений, откуда Log Analytics будет индексировать данные.
2.	Создание новой конфигурации для чтения из учетной записи хранения.
3.	Обновление созданной конфигурации для индексирования данных из дополнительных расположений
4.	Удаление созданной конфигурации.

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two with the storage account resource id and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles", "insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

## Дальнейшие действия

- Дополнительные сведения об использовании PowerShell для настройки Log Analytics см. в описании [командлетов PowerShell Log Analytics](http://msdn.microsoft.com/library/mt188224.aspx) .

<!---HONumber=AcomDC_0518_2016-->