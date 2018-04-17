---
title: Автоматизация Azure Application Insights с помощью PowerShell | Документация Майкрософт
description: Автоматизация создания ресурсов, оповещений и тестов доступности в PowerShell с помощью шаблона Azure Resource Manager.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2017
ms.author: mbullwin
ms.openlocfilehash: 46ba4ce992640e8a6d171ab839dd7cdb24e0b404
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
#  <a name="create-application-insights-resources-using-powershell"></a>Создание ресурсов Application Insights с помощью PowerShell
В этой статье показано, как автоматизировать создание и обновление ресурсов [Application Insights](app-insights-overview.md) с помощью управления ресурсами Azure. Эту функцию можно использовать, например, в процессе сборки. Наряду с базовым ресурсом Application Insights можно создавать [веб-тесты доступности](app-insights-monitor-web-app-availability.md) и другие ресурсы Azure, а также настраивать [оповещения](app-insights-alerts.md) и [схему цен](app-insights-pricing.md).

Ключ к созданию этих ресурсов — шаблоны JSON для [диспетчера ресурсов Azure](../azure-resource-manager/powershell-azure-resource-manager.md). Порядок действий выглядит следующим образом: загрузить JSON-определения существующих ресурсов, параметризовать определенные значения как имена и выполнить шаблон, когда возникнет необходимость в создании нового ресурса. Несколько ресурсов можно объединить, чтобы создавать их одновременно, например, объединить монитор приложений с тестами доступности, оповещениями и хранилищем для непрерывного экспорта. С параметризацией некоторых значений связаны определенные тонкости, которые мы рассмотрим позднее.

## <a name="one-time-setup"></a>Однократная настройка
Если вы ранее не использовали PowerShell для подписки Azure:

Установите модуль Azure Powershell на компьютере, где требуется выполнять сценарии.

1. Установите [установщик веб-платформы Майкрософт (версии 5 или более поздней)](http://www.microsoft.com/web/downloads/platform.aspx).
2. Используйте его для установки Microsoft Azure PowerShell.

## <a name="create-an-azure-resource-manager-template"></a>Создание шаблона Azure Resource Manager
Создайте новый файл JSON — в данном примере назовем его `template1.json` . Скопируйте в него следующее содержимое:

```JSON
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the application name."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "HockeyAppBridge",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the application type."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "East US",
                "allowedValues": [
                    "South Central US",
                    "West Europe",
                    "East US",
                    "North Europe"
                ],
                "metadata": {
                    "description": "Enter the application location."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "1 = Basic, 2 = Enterprise"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 24,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```



## <a name="create-application-insights-resources"></a>Создание ресурсов Application Insights
1. В PowerShell войдите в Azure:
   
    `Login-AzureRmAccount`
2. Выполните следующую команду:
   
    ```PS
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` — группа, в которой необходимо создать ресурсы.
   * Параметр `-TemplateFile` должен предшествовать пользовательским параметрам.
   * `-appName` — имя создаваемого ресурса.

Можно добавить другие параметры. Их описание доступно в разделе параметров шаблона.

## <a name="to-get-the-instrumentation-key"></a>Получение ключа инструментирования
После создания ресурса приложения вам понадобится ключ инструментирования. 

```PS
    $resource = Find-AzureRmResource -ResourceNameEquals "<YOUR APP NAME>" -ResourceType "Microsoft.Insights/components"
    $details = Get-AzureRmResource -ResourceId $resource.ResourceId
    $ikey = $details.Properties.InstrumentationKey
```


<a id="price"></a>
## <a name="set-the-price-plan"></a>Настройка тарифного плана

Вы можете настроить [тарифный план](app-insights-pricing.md).

Для создания ресурса приложения с тарифным планом "Корпоративный" с помощью приведенного выше шаблона, выполните следующую команду:

```PS
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -priceCode 2 `
               -appName myNewApp
```

|priceCode|План|
|---|---|
|1|базовая;|
|2|Enterprise|

* Если вы хотите использовать только тарифный план по умолчанию "Базовый", то можете не указывать в шаблоне ресурс CurrentBillingFeatures.
* Если вы хотите изменить ценовой план после создания ресурса компонента, можно использовать шаблон, пропускающий ресурс microsoft.insights/components. Кроме того, опустите узел `dependsOn` из ресурса выставления счетов. 

Чтобы проверить обновленный ценовой план, просмотрите страницу **Usage and estimated costs** (Данные об использовании и предполагаемые расходы) в браузере. **Обновите окно браузера**, чтобы убедиться, что отображается последнее состояние.



## <a name="add-a-metric-alert"></a>Добавление оповещения метрики

Чтобы настроить оповещение метрики одновременно с ресурсом приложения, добавьте следующий код в файл шаблона:

```JSON
{
    parameters: { ... // existing parameters ...
            "responseTime": {
              "type": "int",
              "defaultValue": 3,
              "minValue": 1,
              "metadata": {
                "description": "Enter response time threshold in seconds."
              }
    },
    variables: { ... // existing variables ...
      // Alert names must be unique within resource group.
      "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
    }, 
    resources: { ... // existing resources ...
     {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this resource is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('responseAlertName')]",
        "description": "response time alert",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/components', parameters('appName'))]",
            "metricName": "request.duration"
          },
          "threshold": "[parameters('responseTime')]", //seconds
          "windowSize": "PT15M" // Take action if changed state for 15 minutes
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

При вызове шаблона можно также добавить этот параметр (при необходимости):

    `-responseTime 2`

Вы можете также выполнить параметризацию других полей. 

Чтобы узнать имена типов и сведения о настройке других правил оповещения, создайте правило вручную и проверьте его в [Azure Resource Manager](https://resources.azure.com/). 


## <a name="add-an-availability-test"></a>Добавление теста доступности

Этот пример предназначен для проверки связи (проверки одной страницы).  

Тест доступности **состоит из двух частей**: самого теста и оповещения, сообщающего об ошибках.

Добавьте следующий код в файл шаблона, из которого создается приложение:

```JSON
{
    parameters: { ... // existing parameters here ...
      "pingURL": { "type": "string" },
      "pingText": { "type": "string" , defaultValue: ""}
    },
    variables: { ... // existing variables here ...
      "pingTestName":"[concat('PingTest-', toLower(parameters('appName')))]",
      "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
    },
    resources: { ... // existing resources here ...
    { //
      // Availability test: part 1 configures the test
      //
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
        "Enabled": true,
        "Frequency": 900, // 15 minutes
        "Timeout": 120, // 2 minutes
        "Kind": "ping", // single URL test
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExectuionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },

    {
      //
      // Availability test: part 2, the alert rule
      //
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]", 
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('pingAlertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('pingTestName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M", // Take action if changed state for 15 minutes
          "failedLocationCount": 2
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Чтобы получить коды для других расположений тестирования или автоматизировать создание более сложных веб-тестов, создайте пример вручную, а затем выполните параметризацию кода из [Azure Resource Manager](https://resources.azure.com/).

## <a name="add-more-resources"></a>Добавление дополнительных ресурсов

Чтобы автоматизировать создание любого другого ресурса любого типа, создайте пример вручную, а затем скопируйте его код и выполните его параметризацию из [Azure Resource Manager](https://resources.azure.com/). 

1. Откройте [диспетчер ресурсов Azure](https://resources.azure.com/). Перейдите в `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components` к своему ресурсу приложения. 
   
    ![Навигация в обозревателе ресурсов Azure](./media/app-insights-powershell/01.png)
   
    *Компоненты* — это основные ресурсы Application Insights для отображения приложений. Для соответствующих правил оповещения и веб-тестов доступности имеются отдельные ресурсы.
2. Скопируйте JSON компонента в соответствующее место в файле `template1.json`.
3. Удалите следующие свойства:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Откройте разделы webtests и alertrules и скопируйте JSON для отдельных элементов в шаблон. (Не копируйте содержимое узлов webtests или alertrules, перейдите в элементы под ними.)
   
    С каждым веб-тестом связано правило оповещения, поэтому скопировать необходимо оба элемента.
   
    Можно также добавить оповещения для метрик. [Имена метрик](app-insights-powershell-alerts.md#metric-names).
5. Вставьте в каждый ресурс следующую строку.
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Параметризация шаблона
Теперь отдельные имена необходимо заменить параметрами. Для [параметризации шаблона](../azure-resource-manager/resource-group-authoring-templates.md) необходимо записать выражения, используя [набор вспомогательных функций](../azure-resource-manager/resource-group-template-functions.md). 

Параметризовать только часть строки нельзя, поэтому для формирования строки используйте `concat()` .

Вот примеры возможных замен: Каждая замена используется несколько раз. В вашем шаблоне могут потребоваться другие замены. В данных примерах используются параметры и переменные, определенные в верхней части шаблона.

| поиск | заменить на |
| --- | --- |
| `"hidden-link:/subscriptions/.../components/MyAppName"` |`"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"` |
| `"/subscriptions/.../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (строчная) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>` Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`<br/>Удалите GUID и идентификатор. |

### <a name="set-dependencies-between-the-resources"></a>Установка зависимостей между ресурсами
Служба Azure должна настраивать ресурсы в строгом порядке. Чтобы одна процедура настройки не начиналась прежде, чем завершится предыдущая, добавьте строки зависимости:

* В ресурсе теста доступности:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* В ресурсе оповещения для теста доступности:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Дополнительная информация
Другие статьи об автоматизации:

* [Создание ресурса Application Insights](app-insights-powershell-script-create-resource.md) — быстрый метод без использования шаблона.
* [Настройка оповещений](app-insights-powershell-alerts.md)
* [Creating an Application Insights Web Test and Alert Programmatically](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Отправка данных системы диагностики Azure в Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Развертывание в Azure из GitHub](http://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Создание заметок выпуска](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)

