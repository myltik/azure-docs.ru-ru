---
title: "Создание ресурсов Application Insights, оповещений и тестов доступности в PowerShell | Документация Майкрософт"
description: "Автоматизация управления ресурсами Application Insights с использованием шаблона Azure Resource Manager."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fc2d3c3fee5abbab0eab16c106c88c8753e703cc
ms.openlocfilehash: f6302d7d11691635c286164f11be74ff3a1ad8dd


---
# <a name="create-application-insights-resources-using-powershell"></a>Создание ресурсов Application Insights с помощью PowerShell
Данная статья демонстрирует автоматическое создание ресурса [Application Insights](app-insights-overview.md) в Azure. Эту функцию можно использовать, например, в процессе сборки. Наряду с основным ресурсом Application Insights можно создавать [веб-тесты доступности](app-insights-monitor-web-app-availability.md) и другие ресурсы Azure, а также [настраивать оповещения](app-insights-alerts.md).

Ключ к созданию этих ресурсов — шаблоны JSON для [диспетчера ресурсов Azure](../azure-resource-manager/powershell-azure-resource-manager.md). Порядок действий выглядит следующим образом: загрузить JSON-определения существующих ресурсов, параметризовать определенные значения как имена и выполнить шаблон, когда возникнет необходимость в создании нового ресурса. Несколько ресурсов можно объединить, чтобы создавать их одновременно, например, объединить монитор приложений с тестами доступности, оповещениями и хранилищем для непрерывного экспорта. С параметризацией некоторых значений связаны определенные тонкости, которые мы рассмотрим позднее.

## <a name="one-time-setup"></a>Однократная настройка
Если вы ранее не использовали PowerShell для подписки Azure:

Установите модуль Azure Powershell на компьютере, где требуется выполнять сценарии.

1. Установите [установщик веб-платформы Майкрософт (версии 5 или более поздней)](http://www.microsoft.com/web/downloads/platform.aspx).
2. Используйте его для установки Microsoft Azure PowerShell.

## <a name="copy-the-json-for-existing-resources"></a>Копирование JSON для существующих ресурсов
1. Настройте [Application Insights](app-insights-overview.md) для проектов, которые должны будут создаваться автоматически. При необходимости добавьте веб-тесты и предупреждения.
2. Создайте новый файл JSON — в данном примере назовем его `template1.json` . Скопируйте в него следующее содержимое:

    ```JSON

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "appName": { "type": "string" },
            "webTestName": { "type": "string" },
            "url": { "type": "string" },
            "text": { "type" : "string" }
          },
          "variables": {
            "testName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')))]"
            "alertRuleName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')), 
               '-', subscription().subscriptionId)]"
          },
          "resources": [
            {
              // component JSON file contents
            },
            {
              //web test JSON file contents
            },
            {
              //alert rule JSON file contents
            }

            // Any other resources go here
          ]
        }

    ```

    Этот шаблон в дополнение к основному ресурсу настроит один тест доступности.


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
   
    У каждого веб-теста имеется соответствующее правило генерации оповещений. Первым указывается веб-тест.
   
    Можно также добавить оповещения для метрик. [Имена метрик](app-insights-powershell-alerts.md#metric-names).
5. Вставьте в каждый ресурс следующую строку.
   
    `"apiVersion": "2015-05-01",`

## <a name="parameterize-the-template"></a>Параметризация шаблона
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

## <a name="set-dependencies-between-the-resources"></a>Установка зависимостей между ресурсами
Служба Azure должна настраивать ресурсы в строгом порядке. Чтобы одна процедура настройки не начиналась прежде, чем завершится предыдущая, добавьте строки зависимости:

* В ресурсе теста доступности:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* В ресурсе оповещения:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`

Обратите внимание, что тест доступности на самом деле состоит из двух частей: самого теста и правила генерации оповещений, которое активируется в зависимости от результатов теста.

## <a name="create-application-insights-resources"></a>Создание ресурсов Application Insights
1. В PowerShell войдите в Azure.
   
    `Login-AzureRmAccount`
2. Выполните следующую команду:
   
    ```PS
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -templateFile .\template1.json `
               -appName myNewApp `
               -webTestName aWebTest `
               -url http://myapp.com `
               -text "Welcome!"
   
    ``` 
   
   * -ResourceGroupName — это группа, в которой нужно создать новые ресурсы.
   * Параметр -templateFile должен предшествовать пользовательским параметрам.
   * -appName — имя ресурса, который нужно создать.
   * -webTestName — имя веб-теста, который нужно создать.
   * -url — это URL-адрес веб-приложения.
   * -text — это текстовая строка, отображаемая на веб-странице.

## <a name="to-get-the-instrumentation-key"></a>Получение ключа инструментирования
После создания ресурса приложения вам понадобится ключ инструментирования iKey. 

```PS

    $resource = Get-AzureRmResource -ResourceId "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<YOUR RESOURCE GROUP>/providers/Microsoft.Insights/components/<YOUR APP NAME>"

    $resource.Properties.InstrumentationKey
```


## <a name="an-example"></a>Пример
Ниже приведен полный шаблон, созданный мной. В нем есть компонент приложения, тест доступности, оповещение для теста доступности и оповещение для метрики времени ответа.

``` JSON

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "webTestName": { "type": "string" },
    "appName": { "type": "string" },
    "URL": { "type": "string" },
    "text": { "type": "string" }
  },
  "variables": {
    "alertRuleName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')), '-', subscription().subscriptionId)]",
    "testName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')))]",
    "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
  },
  "resources": [
    {
      //
      // App resource
      //
      "name": "[parameters('appName')]",
      "type": "Microsoft.Insights/components",
      "apiVersion": "2014-04-01",
      "kind": "web",
      "location": "East US", // Set to preferred location 
      "properties": {
        "Application_Type": "web",
        "Flow_Type": "Brownfield",
        "Request_Source": "VSIX3.3.1.0",
        "Name": "[parameters('appName')]",
        "PackageId": null,
        "ApplicationId": "[parameters('appName')]"
      },
      "tags": { "applicationType": "web" }
    },
    {
      //
      // Availability test
      //
      "name": "[variables('testName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "East US", // Set to preferred location
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[parameters('webTestName')]",
        "Description": "n",
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
          "WebTest": "[concat('<WebTest   Name=\"', parameters('webTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExectuionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('text'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('testName')]"
      }
    },
    {
      //
      // Alert rule for the availability test
      //
      "name": "[variables('alertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "East US", // Must be East US at present
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('testName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('testName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('alertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('testName'))]",
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

    },
    {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "East US", // Must be East US at present
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
        "[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]"
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
          "threshold": 3, //seconds
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
  ]
}


```


## <a name="next-steps"></a>Дальнейшие действия
Другие статьи об автоматизации:

* [Создание ресурса Application Insights](app-insights-powershell-script-create-resource.md) — быстрый метод без использования шаблона.
* [Настройка оповещений](app-insights-powershell-alerts.md)
* [Creating an Application Insights Web Test and Alert Programmatically](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Отправка данных системы диагностики Azure в Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Развертывание в Azure из GitHub](http://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Создание заметок выпуска](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)




<!--HONumber=Nov16_HO4-->


