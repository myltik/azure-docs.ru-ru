---
title: Сценарий PowerShell для создания ресурса Application Insights | Документация Майкрософт
description: Автоматизация создания ресурсов Application Insights.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: f0082c9b-43ad-4576-a417-4ea8e0daf3d9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/19/2016
ms.author: mbullwin
ms.openlocfilehash: d06b44246b694c0d2a83503ecd1ae0cedfadd9ec
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>Сценарий PowerShell для создания ресурса Application Insights


Если вам требуется отслеживать новое приложение или новую версию приложения с помощью [Application Insights](https://azure.microsoft.com/services/application-insights/), настройте новый ресурс в Microsoft Azure. В этом ресурсе будут анализироваться и отображаться данные телеметрии из вашего приложения. 

Вы можете автоматизировать создание нового ресурса с помощью PowerShell.

Например, если вы разрабатываете приложение для мобильных устройств, вполне вероятно, что в одно и то же время клиенты будут использовать несколько опубликованных версий вашего приложения. Вам, наверняка, не нужно получать смешанные результаты телеметрии по разным версиям. Поэтому вам доступен процесс сборки, позволяющий создавать новый ресурс для каждой сборки.

> [!NOTE]
> Если вы хотите сразу создать набор ресурсов, рассмотрите возможность [использования шаблона Azure Resource Manager](app-insights-powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Сценарий для создания ресурса Application Insights
См. спецификацию соответствующего командлета:

* [New-AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*Сценарий PowerShell*  

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Connect-AzureRmAccount / Connect-AzureRmAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 

$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

# Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource


$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


# Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>Что делать с iKey
Каждый ресурс определяется по ключу инструментирования (iKey). Этот ключ iKey выводит сценарий создания ресурса. Сценарий сборки должен предоставлять iKey для пакета SDK для Application Insights, внедренного в приложение.

Есть два способа сделать iKey доступным для пакета SDK:

* В файле [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Или в [коде инициализации](app-insights-api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>См. также
* [Создание ресурсов Application Insights и веб-тестов на основе шаблонов](app-insights-powershell.md)
* [Настройка мониторинга диагностики Azure с помощью PowerShell](app-insights-powershell-azure-diagnostics.md) 
* [Настройка оповещений с помощью PowerShell](app-insights-powershell-alerts.md)

