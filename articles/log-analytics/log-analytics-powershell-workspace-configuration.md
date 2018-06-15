---
title: Использование PowerShell для создания и настройки рабочей области Log Analytics | Документация Майкрософт
description: Log Analytics использует данные с серверов в вашей локальной или облачной инфраструктуре. При генерировании системой диагностики Azure можно брать данные компьютера из хранилища Azure.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: 3b9b7ade-3374-4596-afb1-51b695f481c2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 11/21/2016
ms.author: richrund
ms.openlocfilehash: 6a3f91323a017533d2d012f1e81760396c17a643
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30179095"
---
# <a name="manage-log-analytics-using-powershell"></a>Управление Log Analytics с помощью PowerShell
[Командлеты PowerShell Log Analytics](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) можно использовать для выполнения различных функций в Log Analytics как из командной строки, так и в составе сценария.  Примеры задач, которые можно выполнять с помощью PowerShell.

* Создание рабочей области
* Добавление или удаление решения
* Импорт и экспорт сохраненных поисков
* Создание группы компьютеров
* Включение сбора журналов IIS с компьютеров, на которых установлен агент Windows
* Сбор счетчиков производительности с компьютеров под управлением Linux и Windows
* Сбор событий из системного журнала с компьютеров Linux 
* Сбор событий из журналов событий Windows
* Сбор пользовательских журналов событий
* Добавление агента Log Analytics в виртуальную машину Azure
* Настройка Log Analytics для индексирования данных, собранных системой диагностики Azure

Эта статья содержит два примера кода, иллюстрирующих некоторые доступные в PowerShell функции.  Сведения о других функциях см. в [справочнике по командлетам PowerShell Log Analytics](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx).

> [!NOTE]
> Компонент Log Analytics раньше назывался Operational Insights, поэтому именно такое имя используется в командлетах.
> 
> 

## <a name="prerequisites"></a>предварительным требованиям
Эти примеры работают с версией 2.3.0 или более поздней версией модуля AzureRm.OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Создание и настройка рабочей области Log Analytics
Этот пример сценария иллюстрирует следующие задачи.

1. Создание рабочей области
2. Вывод списка доступных решений
3. Добавление решений в рабочую область
4. Импорт сохраненных поисков
5. Экспорт сохраненных поисков
6. Создание группы компьютеров
7. Включение сбора журналов IIS с компьютеров, на которых установлен агент Windows
8. Сбор счетчиков производительности логического диска с компьютеров под управлением Linux ("Процент использования индексных дескрипторов"; "Свободно мегабайт"; "Процент используемого места"; "Количество обращений к диску (в секунду)"; "Количество обращений чтения или записи (в секунду))"
9. Сбор событий из системного журнала с компьютеров Linux
10. Сбор событий (ошибок и предупреждений) из журнала событий приложений с компьютеров Windows
11. Сбор данных счетчика производительности "Доступный объем памяти" (в МБ) с компьютеров Windows
12. Сбор пользовательского журнала 

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
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1", 
    "description": "Example custom log datasource", 
    "inputs": [
        { 
            "location": { 
            "fileSystemLocations": { 
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ], 
                "linuxFileTypeLogPaths": [ "/var/logs" ] 
                } 
            }, 
        "recordDelimiter": { 
            "regexDelimiter": { 
                "pattern": "\\n", 
                "matchIndex": 0, 
                "matchIndexSpecified": true, 
                "numberedGroup": null 
                } 
            } 
        }
    ], 
    "extractions": [
        { 
            "extractionName": "TimeGenerated", 
            "extractionType": "DateTime", 
            "extractionProperties": { 
                "dateTimeExtraction": { 
                    "regex": null, 
                    "joinStringRegex": null 
                    } 
                } 
            }
        ] 
    }
"@

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
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

# Create Computer Group based on a query
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Create a computer group based on names (up to 5000)
$computerGroup = """servername1.contoso.com"",""servername2.contoso.com"",""servername3.contoso.com"",""servername4.contoso.com"""
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Named Servers" -DisplayName "Named Servers" -Category "My Saved Searches" -Query $computerGroup -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Настройка Log Analytics для индексации системы диагностики Azure
Для отслеживания ресурсов Azure без использования агента необходимо включить для этих ресурсов систему диагностики Azure и настроить ее для записи данных в рабочую область Log Analytics. Это позволяет отправлять данные непосредственно в Log Analytics, не записывая их в учетную запись хранения. Ниже перечислены поддерживаемые ресурсы.

| Тип ресурса | Журналы | Метрики |
| --- | --- | --- |
| Шлюзы приложений    | Yes | Yes |
| Учетные записи службы автоматизации     | Yes | |
| Учетные записи пакетной службы          | Yes | Yes |
| Data Lake Analytics     | Yes | | 
| Data Lake Store         | Yes | |
| пул эластичных баз данных SQL;        |     | Yes |
| пространство имен концентратора событий;     |     | Yes |
| Центры Интернета вещей;                |     | Yes |
| хранилище ключей;               | Yes | |
| Балансировщики нагрузки          | Yes | |
| приложения логики;              | Yes | Yes |
| группы сетевой безопасности; | Yes | |
| кэш Redis;             |     | Yes |
| Службы поиска         | Yes | Yes |
| Пространство имен служебной шины   |     | Yes |
| SQL (версия 12)               |     | Yes |
| Веб-сайты               |     | Yes |
| Фермы веб-серверов        |     | Yes |

Дополнительные сведения о доступных метриках см. в разделе [Метрики, поддерживаемые Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md).

Дополнительные сведения о доступных журналах см. в разделе [Поддерживаемые службы и схемы для журналов диагностики](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO" 

Set-AzureRmDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Кроме того, вы можете использовать предыдущий командлет для сбора журналов из ресурсов, которые находятся в разных подписках. Этот командлет может работать в нескольких подписках, так как вы предоставляете идентификаторы ресурса, для которого создаются журналы, и рабочей области, в которую они отправляются.


## <a name="configuring-log-analytics-to-index-azure-diagnostics-from-storage"></a>Настройка Log Analytics для индексации системы диагностики Azure из хранилища
Для сбора данных журнала из работающего экземпляра классической облачной службы или кластера Service Fabric необходимо сначала записать данные в службу хранилища Azure. После этого следует настроить Log Analytics для сбора журналов из учетной записи хранения. Ниже перечислены поддерживаемые ресурсы:

* классические облачные службы (рабочие и веб-роли);
* кластеры Service Fabric;

В приведенном ниже примере показано, как выполнить следующие задачи.

1. Вывод списка существующих учетных записей хранения и расположений, откуда Log Analytics будет индексировать данные.
2. Создание конфигурации для чтения из учетной записи хранения.
3. Обновление созданной конфигурации для индексирования данных из дополнительных расположений
4. Удаление созданной конфигурации.

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

Кроме того, вы можете использовать предыдущий сценарий для сбора журналов из учетных записей хранения, которые находятся в разных подписках. Этот сценарий может работать в нескольких подписках, так как вы предоставляете идентификатор ресурса учетной записи хранения и соответствующий ключ доступа. При изменении ключа доступа необходимо обновить данные хранилища с учетом нового ключа.


## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения об использовании PowerShell для настройки Log Analytics см. в [описании командлетов PowerShell Log Analytics](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx).

