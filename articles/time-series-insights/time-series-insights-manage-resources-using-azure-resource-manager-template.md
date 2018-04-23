---
title: Как управлять средой "Аналитика временных рядов Azure" с помощью шаблонов Azure Resource Manager | Документация Майкрософт
description: В этой статье описано, как программно управлять средой "Аналитика временных рядов Azure" с помощью Azure Resource Manager.
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 12/08/2017
ms.openlocfilehash: 8355248f28a019ef4712f542c8eac731362330ce
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Создание ресурсов службы "Аналитика временных рядов" с помощью шаблонов Resource Manager

В этой статье описывается, как создать и развернуть ресурсы службы "Аналитика временных рядов" с помощью шаблонов Azure Resource Manager, PowerShell и поставщика ресурсов аналитики временных рядов.

"Аналитика временных рядов" поддерживает такие ресурсы:
   | Ресурс | ОПИСАНИЕ |
   | --- | --- |
   | Среда | Среда "Аналитика временных рядов" является логической группой событий, которая считывается из брокеров событий, хранится и предоставляется для запроса. Дополнительные сведения см. в руководстве по [планированию среды "Аналитика временных рядов Azure"](time-series-insights-environment-planning.md). |
   | Источник событий | Источник событий — это подключение к брокеру событий, из которого среда "Аналитика временных рядов" читает и принимает события. Источники событий, поддерживаемые в настоящее время: Центр Интернета вещей и концентратор событий. |
   | Набор эталонных данных | Наборы эталонных данных предоставляют метаданные о событиях в среде. Метаданные в наборах эталонных данных во время приема будут соединены с событиями. Наборы эталонных данных определяются своими свойствами ключей событий как ресурсы. Фактические метаданные, составляющие набор эталонных данных, отправляются или изменяются через API плоскости данных. |
   | Политика доступа | Политики доступа предоставляют разрешения для отправки запросов данных, обработки ссылочных данных в среде, а также предоставляют другим пользователям среды доступ к сохраненным запросам и перспективам. Дополнительные сведения см. в статье [Предоставление доступа к данным для среды Time Series Insights с помощью портала Azure](time-series-insights-data-access.md). |

Шаблоны Resource Manager являются файлами в формате JSON, определяющими инфраструктуру и конфигурацию ресурсов в группе ресурсов. Дополнительные сведения см. в следующих документах:

- [Развертывание шаблона](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)

Шаблон быстрого запуска [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) опубликован на GitHub. Этот шаблон создает среду "Аналитика временных рядов", дочерний источник событий, который должен использовать события из концентратора событий, и политики доступа, которые предоставляют доступ к данным среды. Если не указан имеющийся концентратор событий, он будет создан при развертывании.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Развертывание шаблона быстрого запуска локально с помощью PowerShell

Следующая процедура описывает использование PowerShell для развертывания шаблона Azure Resource Manager, который создает среду "Аналитика временных рядов", дочерний источник событий, который должен использовать события из концентратора событий и политики доступа, которые предоставляют доступ к данным среды. Если не указан имеющийся концентратор событий, он будет создан при развертывании.

Ниже приведена примерная последовательность действий.

1. Установите PowerShell.
1. Создайте шаблон и файл параметров.
1. В PowerShell войдите в свою учетную запись Azure.
1. Создайте группу ресурсов, если ее еще нет.
1. Протестируйте развертывание.
1. Разверните шаблон.

### <a name="install-powershell"></a>Установка PowerShell

Установите Azure PowerShell, следуя указаниям в разделе [Getting started with Azure PowerShell](/powershell/azure/get-started-azureps) (Приступая к работе с Azure PowerShell).

### <a name="create-a-template"></a>Создание шаблона

Клонируйте или скопируйте шаблон [201-timeseriesinsights-environment-with-eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) из GitHub.

### <a name="create-a-parameters-file"></a>Создание файла параметров

Чтобы создать файл параметров, скопируйте файл [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json).

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "eventHubNamespaceName": {
          "value": "GEN-UNIQUE"
      },
      "eventHubName": {
          "value": "GEN-UNIQUE"
      },
      "consumerGroupName": {
          "value": "GEN-UNIQUE"
      },
      "environmentName": {
        "value": "GEN-UNIQUE"
      },
      "eventSourceName": {
        "value": "GEN-UNIQUE"
      }
  }
}
```

#### <a name="required-parameters"></a>Необходимые параметры

   | Параметр | ОПИСАНИЕ |
   | --- | --- |
   | eventHubNamespaceName | Пространство имен концентратора событий источника. |
   | eventHubName | Имя концентратора событий источника. |
   | consumerGroupName | Имя группы потребителей, которую будет использовать служба "Аналитика временных рядов" для чтения данных из концентратора событий. **Примечание.** Во избежание конфликта ресурсов группа потребителей должна быть выделена для службы "Аналитика временных рядов" и не предоставлять доступ другим читателям. |
   | EnvironmentName | Имя среды. Имя не может включать "<", ">", "%", "&", ":", "\\", "?", "/" и любые управляющие символы. Все остальные символы разрешены.|
   | eventSourceName | Имя дочернего ресурса источника событий. Имя не может включать "<", ">", "%", "&", ":", "\\", "?", "/" и любые управляющие символы. Все остальные символы разрешены. |

#### <a name="optional-parameters"></a>Необязательные параметры

   | Параметр | ОПИСАНИЕ |
   | --- | --- |
   | existingEventHubResourceId | Дополнительный идентификатор ресурса имеющегося концентратора событий будет подключен к среде "Аналитика временных рядов" через источник событий. **Примечание.** Пользователь, развертывающий шаблоны, должен иметь привилегии, чтобы выполнять операцию listkeys в концентраторе событий. Если значение не передается, новый концентратор событий будет создан с помощью шаблона. |
   | environmentDisplayName | Дополнительное понятное имя среды, отображаемое в инструменте или пользовательских интерфейсах вместо имени среды. |
   | environmentSkuName | Имя номера SKU. Дополнительные сведения см. на странице [цен на службу "Аналитика временных рядов"](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
   | environmentSkuCapacity | Емкость единиц номера SKU. Дополнительные сведения см. на странице [цен на службу "Аналитика временных рядов"](https://azure.microsoft.com/pricing/details/time-series-insights/).|
   | environmentDataRetentionTime | Минимальный период, в течение которого события среды будут доступны для запроса. Значение указывается в формате ISO 8601, например P30D для политики хранения в течение 30 дней. |
   | eventSourceDisplayName | Дополнительное понятное имя среды, отображаемое в инструменте или пользовательских интерфейсах вместо имени источника события. |
   | eventSourceTimestampPropertyName | Свойство события, которое будет использоваться в качестве метки времени источника события. Если для timestampPropertyName значение не указано, задано значение NULL или указана пустая строка, будет использоваться время создания события. |
   | eventSourceKeyName | Имя общего ключа доступа, которое будет использовать служба "Аналитика временных рядов" для подключения к концентратору событий. |
   | accessPolicyReaderObjectIds | Список идентификаторов объектов пользователей или приложений в Azure AD, которые должны иметь доступ на чтение к среде. Субъект-службу можно получить, вызвав командлеты **Get-AzureRMADUser** или **Get-AzureRMADServicePrincipal**. Создание политики доступа для группы Azure AD еще не поддерживается. |
   | accessPolicyContributorObjectIds | Список идентификаторов объектов пользователей или приложений в Azure AD, которые должны иметь доступ участника к среде. Субъект-службу можно получить, вызвав командлеты **Get-AzureRMADUser** или **Get-AzureRMADServicePrincipal**. Создание политики доступа для группы Azure AD еще не поддерживается. |

Например, следующий файл параметров будет использоваться для создания среды и источника событий, который считывает события из имеющегося концентратора событий. Он также создает две политики доступа, которые предоставляют доступ участника к среде.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventHubNamespaceName": {
            "value": "tsiTemplateTestNamespace"
        },
        "eventHubName": {
            "value": "tsiTemplateTestEventHub"
        },
        "consumerGroupName": {
            "value": "tsiTemplateTestConsumerGroup"
        },
        "environmentName": {
          "value": "tsiTemplateTestEnvironment"
        },
        "eventSourceName": {
          "value": "tsiTemplateTestEventSource"
        },
        "existingEventHubResourceId": {
          "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
        },
        "accessPolicyContributorObjectIds": {
            "value": [
                "AGUID001-0000-0000-0000-000000000000",
                "AGUID002-0000-0000-0000-000000000000"
            ]
        }
    }
  }
```

Дополнительные сведения см. в разделе [Параметры](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Вход в Azure и настройка подписки Azure

В командной строке PowerShell выполните следующую команду:

```powershell
Connect-AzureRmAccount
```

Вам будет предложено войти в учетную запись Azure. Войдя в систему, выполните следующую команду, чтобы просмотреть доступные подписки:

```powershell
Get-AzureRMSubscription
```

Эта команда возвращает список доступных подписок Azure. Выберите подписку для текущего сеанса, выполнив приведенную ниже команду. Замените `<YourSubscriptionId>` идентификатором GUID подписки Azure, которую вы хотите использовать:

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Задание группы ресурсов

Если у вас нет группы ресурсов, создайте ее с помощью команды **New-AzureRmResourceGroup**. Введите имя группы ресурсов и нужное расположение. Например: 

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

После успешного выполнения операции появится сводка по новой группе ресурсов.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>тестирование развертывания

Проверьте развертывание, выполнив командлет `Test-AzureRmResourceGroupDeployment`. При тестировании развернутой службы укажите точно такие же параметры, как и при ее выполнении.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

### <a name="create-the-deployment"></a>Создание развертывания

Чтобы создать развертывание, выполните командлет `New-AzureRmResourceGroupDeployment` и укажите необходимые параметры при появлении запроса. Параметры включают в себя имя развертывания, имя группы ресурсов и путь к файлу шаблона или его URL-адрес. Если параметр **Режим** не указан, используется стандартное значение **Добавочный**. Дополнительные сведения см. в статье [Добавочные и полные развертывания](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments).

Следующая команда запрашивает пять обязательных параметров в окне PowerShell:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

Чтобы использовать вместо этого файл параметров, выполните приведенную ниже команду:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

При выполнении командлета развертывания также можно использовать встроенные параметры. Команда выглядит следующим образом:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Чтобы выполнить [полное](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) развертывание, установите для параметра **Режим** значение **Полный**.

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

## <a name="verify-the-deployment"></a>Проверка развертывания

В случае успешного развертывания ресурсов в окне PowerShell будет приведена сводка по развертыванию.

```powershell
DeploymentName          : azuredeploy
ResourceGroupName       : MyDemoRG
ProvisioningState       : Succeeded
Timestamp               : 12/9/2017 01:06:54
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          eventHubNamespaceName  String               <eventHubNamespaceName>
                          eventHubName     String                     <eventHubName>
                          consumerGroupName  String                   <consumerGroupName>
                          existingEventHubResourceId  String
                          environmentName  String                     <environmentName>
                          environmentDisplayName  String
                          environmentSkuName  String                  S1
                          environmentSkuCapacity  Int                 1
                          environmentDataRetentionTime  String        P30D
                          eventSourceName  String                     <eventSourceName>
                          eventSourceDisplayName  String
                          eventSourceTimestampPropertyName  String
                          eventSourceKeyName  String                  manage
                          accessPolicyReaderObjectIds  Array          []
                          accessPolicyContributorObjectIds  Array     [
                            "AGUID001-0000-0000-0000-000000000000",
                            "AGUID002-0000-0000-0000-000000000000"
                          ]

Outputs                 :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          dataAccessFQDN   String
                          <guid>.env.timeseries.azure.com
```

## <a name="deploy-the-quickstart-template-through-the-azure-portal"></a>Развертывание шаблона быстрого запуска через портал Azure

Домашняя страница шаблона быстрого запуска на GitHub также содержит кнопку **Развертывание в Azure**. При нажатии на нее открывается страница "Настраиваемое развертывание" на портале Azure. На этой странице можно ввести или выбрать значения для каждого параметра из таблиц [обязательных](time-series-insights-manage-resources-using-azure-resource-manager-template.md#required-parameters) или [необязательных параметров](time-series-insights-manage-resources-using-azure-resource-manager-template.md#optional-parameters). Если нажать кнопку **Приобрести** после заполнения параметров, начнется развертывание шаблона.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>Дополнительная информация

- Сведения о программном управлении ресурсами службы "Аналитика временных рядов" с помощью REST API см. в статье [Time Series Insights Management](https://docs.microsoft.com/rest/api/time-series-insights-management/) (Управление службой "Аналитика временных рядов").
