<properties 
	pageTitle="Сценарий PowerShell для создания ресурса Application Insights" 
	description="Автоматизация создания ресурсов Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/21/2015" 
	ms.author="awills"/>

#  Сценарий PowerShell для создания ресурса Application Insights

*Доступна только предварительная версия Application Insights.*

Если вам требуется отслеживать новое приложение или новую версию приложения с помощью [Visual Studio Application Insights](https://azure.microsoft.com/services/application-insights/), настройте новый ресурс в Microsoft Azure. В этом ресурсе будут анализироваться и отображаться данные телеметрии из вашего приложения.

Вы можете автоматизировать создание нового ресурса с помощью PowerShell.

Например, если вы разрабатываете приложение для мобильных устройств, вполне вероятно, что в одно и то же время клиенты будут использовать несколько опубликованных версий вашего приложения. Вам, наверняка, не нужно получать смешанные результаты телеметрии по разным версиям. Поэтому вам доступен процесс сборки, позволяющий создавать новый ресурс для каждой сборки.

## Сценарий для создания ресурса Application Insights

*Выходные данные*

* Имя приложения Insights = мое\_тестовое\_приложение
* IKey = 00000000-0000-0000-0000-000000000000

*Сценарий PowerShell*

```PowerShell

cls


##################################################################
# Set Values
##################################################################

#If running manually, comment this out before the first execution to login to the Azure Portal:

#Add-AzureAccount

#Set the name of the Application Insights Resource
$appInsightsName = "TestApp"

#Set the application name used for the value of the Tag "AppInsightsApp" - http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-tags/
$applicationTagName = "MyApp"

#Set the name of the Resource Group to use.  By default will use the application name as a starter
$resourceGroupName = "MyAppResourceGroup"

##################################################################
# Create the Resource and Output the name and iKey
##################################################################
#Set the script to Resource Manager - http://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/
Switch-AzureMode AzureResourceManager

#Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

#Create the App Insights Resource
$resource = New-AzureResource -ResourceName $appInsightsName -ResourceGroupName $resourceGroupName -Tag @{ Name = "AppInsightsApp"; Value = $applicationTagName} -ResourceType "Microsoft.Insights/Components" -Location "Central US" -ApiVersion "2014-08-01" -PropertyObject @{"Type"="ASP.NET"} -Force -OutputObjectFormat New

#Give team owner access - http://azure.microsoft.com/documentation/articles/role-based-access-control-powershell/
New-AzureRoleAssignment -Mail "myTeam@fabrikam.com" -RoleDefinitionName Owner -Scope $resource.ResourceId | Out-Null

#Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## Что делать с iKey

Каждый ресурс определяется по ключу инструментирования (iKey). Этот ключ iKey выводит сценарий создания ресурса. Сценарий сборки должен предоставлять iKey для пакета SDK для Application Insights, внедренного в приложение.

Есть два способа сделать iKey доступным для пакета SDK:
  
* В файле [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md): 
 * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Или в [коде инициализации](app-insights-api-custom-events-metrics.md): 
 * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`



## См. также

* [Создание ресурсов Application Insights и веб-тестов на основе шаблонов](app-insights-powershell.md)
* [Настройка мониторинга диагностики Azure с помощью PowerShell](app-insights-powershell-azure-diagnostics.md) 
* [Настройка оповещений с помощью PowerShell](app-insights-powershell-alerts.md)

 

<!---HONumber=AcomDC_0211_2016-->