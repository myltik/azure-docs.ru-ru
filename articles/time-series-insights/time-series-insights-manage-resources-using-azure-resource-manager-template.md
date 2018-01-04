---
title: "Как для управления средой Azure Insights ряда времени с помощью шаблонов диспетчера ресурсов Azure | Документы Microsoft"
description: "В этой статье описывается управления средой Azure Insights ряда времени программно с помощью диспетчера ресурсов Azure."
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
ms.openlocfilehash: b09d4a1aea56a4e306f80a1b43d519d313fd73ab
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2017
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Создание ресурсов аналитики ряда времени с помощью шаблонов диспетчера ресурсов Azure

В этой статье описывается создание и развертывание ресурсов аналитики ряда времени, с помощью шаблонов диспетчера ресурсов Azure, PowerShell и аналитики ряда времени поставщика ресурсов.

Время ряда аналитики поддерживает следующие ресурсы:
   | Ресурс | ОПИСАНИЕ |
   | --- | --- |
   | Среда | Среда времени серии аналитики — логическое группирование событий, которые считываются брокеров событий, хранимых и становятся доступными для запросов. Дополнительные сведения см. [планировать сетевую среду Azure Insights ряда времени](time-series-insights-environment-planning.md) |
   | Источник события | Источник события — это соединение для брокера событий времени серии аналитики считывает и откуда принимает события в среду. Источники событий, поддерживаемых в настоящее время являются центр IoT и концентратора событий. |
   | Эталонного набора данных | Наборы данных ссылки предоставляют метаданные о событиях в среде. Метаданные в наборах данных ссылка будет присоединен с событиями во время входящих сообщений. Ссылка наборы данных определяются как ресурсы с их ключевых свойств событий. Фактические метаданные, содержащийся в эталонном наборе данных отправки или изменены с помощью информационной панели API-интерфейсы. |
   | Политика доступа | Политики доступа разрешение на выдавать запросы данных, управлять ссылочных данных в среде и совместно использовать сохраненные запросы и перспективам, связанным со средой. Дополнительные сведения см. [предоставления доступа к данным в среде времени серии аналитики с помощью портала Azure](time-series-insights-data-access.md) |

Шаблон диспетчера ресурсов — JSON-файл, определяющий инфраструктуры и настраивать ресурсы в группе ресурсов. Дополнительные сведения см. в следующих документах:

- [Обзор диспетчера ресурсов Azure - шаблона-развертывания](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)

[201-timeseriesinsights среды с eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) шаблоном краткого руководства публикуется на сайте GitHub. Этот шаблон создает среду времени серии аналитики, дочерний источник событий должен использовать события из концентратора событий и получить доступ к политикам, предоставляющих доступ к данным среды. Если не указан существующий концентратор событий, один создается с помощью развертывания.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Развертывание шаблона краткое руководство, локально с помощью PowerShell

Следующая процедура описывает, как использовать PowerShell для развертывания шаблона диспетчера ресурсов Azure, создает среду времени серии аналитики, дочерний источник событий должен использовать события из концентратора событий и получить доступ к политикам, предоставляющими доступ к данные среды. Если не указан существующий концентратор событий, один создается с помощью развертывания.

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

Клон или копирования [201-timeseriesinsights среды с eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) шаблона из GitHub.

### <a name="create-a-parameters-file"></a>Создание файла параметров

Чтобы создать файл параметров, скопируйте [201-timeseriesinsights среды с eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) файл.

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

#### <a name="required-parameters"></a>Обязательные параметры

   | Параметр | ОПИСАНИЕ |
   | --- | --- |
   | eventHubNamespaceName | Пространство имен концентратора событий источника. |
   | eventHubName | Имя источника концентратора событий. |
   | consumerGroupName | Имя группы потребителей, который будет использовать службу аналитики ряда времени для чтения данных из концентратора событий. **Примечание:** во избежание конфликта за ресурсы этой группе потребителей должен быть выделен в службу аналитики ряда времени и не совместно с другие средства чтения. |
   | EnvironmentName | Имя среды. Имя не может содержать: "<", ">", «%», «&», ': ','\\','?', '/' и любые управляющие символы. Все остальные символы разрешены.|
   | eventSourceName | Имя ресурса дочернего источника событий. Имя не может содержать: "<", ">", «%», «&», ': ','\\','?', '/' и любые управляющие символы. Все остальные символы разрешены. |

#### <a name="optional-parameters"></a>Необязательные параметры

   | Параметр | ОПИСАНИЕ |
   | --- | --- |
   | existingEventHubResourceId | Идентификатор ресурса необязательно концентратор событий, будет подключена к среде аналитики ряда времени по источнику события. **Примечание:** развертывание шаблона пользователь должен иметь разрешения для выполнения операции listkeys в концентраторе событий. Если значение не передается, новый концентратор событий создается с помощью шаблона. |
   | environmentDisplayName | Необязательное понятное имя для отображения в оборудование или пользователя интерфейсами, а не имя среды. |
   | environmentSkuName | Имя sku. Дополнительные сведения см. в разделе [цены аналитики ряда времени](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
   | environmentSkuCapacity | Емкость единицу складского учета. Дополнительные сведения см. в разделе [цены аналитики ряда времени](https://azure.microsoft.com/pricing/details/time-series-insights/).|
   | environmentDataRetentionTime | Минимальный интервал времени события среды будут доступны для запроса. Значение указывается в формате ISO 8601, например «P30D» для политики хранения 30 дней. |
   | eventSourceDisplayName | Необязательное понятное имя для отображения в оборудование или пользователя интерфейсами, а не имя источника события. |
   | eventSourceTimestampPropertyName | Свойства события, который будет использоваться как источник события timestamp. Если не указано значение timestampPropertyName или если указано значение null или пустой строкой, будет использоваться время создания события. |
   | eventSourceKeyName | Имя ключа общего доступа, которое служба аналитики ряда времени будет использовать для подключения к концентратору событий. |
   | accessPolicyReaderObjectIds | Список идентификаторов пользователей или приложений в Azure AD, который должен иметь доступ к среде для чтения объектов. ObjectId основной службы можно получить, вызвав **Get AzureRMADUser** или **Get AzureRMADServicePrincipal** командлетов. Создание политики доступа для группы Azure AD еще не поддерживается. |
   | accessPolicyContributorObjectIds | Список идентификаторов пользователей или приложений в Azure AD, который должен иметь участника доступ к среде объектов. ObjectId основной службы можно получить, вызвав **Get AzureRMADUser** или **Get AzureRMADServicePrincipal** командлетов. Создание политики доступа для группы Azure AD еще не поддерживается. |

Например следующий файл параметров будет использоваться для создания среды и источник событий, который считывает события из существующих концентратора событий. Он также создает две политики доступа, предоставленные участнику доступ к среде.

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
Login-AzureRmAccount
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

Если нет существующего ресурса группы, создайте новую группу ресурсов с **New AzureRmResourceGroup** команды. Введите имя группы ресурсов и нужное расположение. Например: 

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

## <a name="deploy-the-quickstart-template-through-the-azure-portal"></a>Развертывание шаблона быстрый запуск через портал Azure

Домашняя страница шаблона краткое руководство по GitHub также содержит **развертыванию в Azure** кнопки. Щелкнув по нему открывает страницу развертывания пользовательского портала Azure. На этой странице можно ввести или выбрать значения для этих параметров из [обязательные параметры](time-series-insights-manage-resources-using-azure-resource-manager-template.md#required-parameters) или [необязательные параметры](time-series-insights-manage-resources-using-azure-resource-manager-template.md#optional-parameters) таблиц. После заполнения параметры, нажав кнопку **покупки** кнопка будет инициировать развертывания шаблона.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank"> <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>Дальнейшие действия

- Сведения о программном управлении ресурсов аналитики ряда времени с помощью API REST см. в разделе [управления аналитики ряда времени](https://docs.microsoft.com/rest/api/time-series-insights-management/).
