---
title: Настройка мониторинга с помощью Log Analytics в Azure Service Fabric | Документация Майкрософт
description: Узнайте, как настроить визуализацию и анализ событий в Log Analytics для мониторинга кластеров Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/30/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: af09df52fe733b69cfe4470de2fd6e978f126ca0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="set-up-log-analytics-for-a-cluster"></a>Настройка Log Analytics для кластера

Рабочую область Log Analytics можно настроить в Azure Resource Manager, PowerShell или с помощью Azure Marketplace. Если необходимо сохранить обновленный шаблон развертывания Resource Manager для будущего использования, примените тот же шаблон для настройки среды OMS. Развертывание через Marketplace проще, если вы уже развернули кластер с включенной диагностикой. Если у вас нет доступа на уровне подписки в учетной записи, в которой развертывается OMS, используйте PowerShell или выполните развертывание с помощью шаблона Resource Manager.

> [!NOTE]
> Чтобы настроить Log Analytics для мониторинга кластера, необходимо включить систему диагностики для просмотра событий уровня кластера или платформы.

## <a name="deploy-oms-by-using-azure-marketplace"></a>Развертывание OMS с помощью Azure Marketplace

Если нужно добавить рабочую область OMS после развертывания кластера, перейдите в Azure Marketplace на портале и найдите решение **аналитики Service Fabric**.

1. В области навигации слева щелкните **Создать**. 

2. Найдите **Аналитика Service Fabric**. Выберите появившийся ресурс.

3. Нажмите кнопку **Создать**.

    ![Аналитика Service Fabric OMS в Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. В окне создания аналитики Service Fabric щелкните **Выберите рабочую область** для поля **Рабочая область OMS**, а затем выберите **Создание рабочей области**. Укажите необходимые записи. Единственным требованием является совпадение подписки для кластера Service Fabric и рабочей области OMS. Когда завершится проверка записей, начнется развертывание рабочей области OMS. Это займет всего несколько минут.

5. Когда процесс завершится, снова выберите **Создать** в нижней части окна создания аналитики Service Fabric. Убедитесь, что новая рабочая область отображается в разделе **Рабочая область OMS**. В таком случае решение добавится в созданную рабочую область.

При использовании Windows выполните следующие шаги, чтобы подключить OMS к учетной записи хранения, в которой хранятся события кластера. 

>[!NOTE]
>Для кластеров Linux эта функция подключения еще недоступна. 

### <a name="connect-the-oms-workspace-to-your-cluster"></a>Подключение рабочей области OMS к кластеру 

1. Рабочая область должна быть подключена к данным диагностики, поступающим из кластера. Перейдите в группу ресурсов, в которой вы создали решение "Аналитика Service Fabric". Выберите **ServiceFabric\<имя_рабочей_области_OMS\>** и перейдите на страницу обзора. На этой странице можно изменить параметры решения, рабочей области и получить доступ к порталу OMS.

2. В меню навигации слева в разделе **Источники данных рабочей области** выберите **Журналы учетных записей хранения**.

3. На странице **Журналы учетной записи хранения** выберите **Добавить** в верхней части, чтобы добавить в рабочую область журналы кластера.

4. Выберите **Учетная запись хранения**, чтобы добавить учетную запись хранения, созданную в кластере. Если вы использовали имя по умолчанию, она называется **sfdg\<имя_группы_ресурсов\>**. Вы можете проверить, какое значение указано для параметра **applicationDiagnosticsStorageAccountName** в шаблоне Azure Resource Manager, на основе которого вы развертывали кластер. Если имя не отображается, прокрутите список вниз и выберите **Загрузить еще**. Выберите имя учетной записи хранения.

5. Укажите тип данных. Задайте для параметра типа данных значение **События Service Fabric**.

6. При этом для параметра "Источник" будет автоматически установлено значение **WADServiceFabric\*EventTable**.

7. Нажмите кнопку **ОК**, чтобы подключить рабочую область к журналам кластера.

    ![Добавление журналов учетной записи хранения к OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Теперь учетная запись будет отображаться в списке журналов учетной записи хранения в разделе источников данных для рабочей области.

Вы успешно добавили решение "Аналитика Service Fabric" в рабочую область Log Analytics в OMS, подключенную должным образом к платформе кластера и таблице журналов приложения. Аналогичным образом вы можете добавить в рабочую область дополнительные источники.


## <a name="deploy-oms-by-using-a-resource-manager-template"></a>Развертывание OMS с помощью шаблона Resource Manager

При развертывании кластера с помощью шаблона Resource Manager этот шаблон создает рабочую область OMS, добавляет в нее решение Service Fabric и настраивает в ней чтение данных из соответствующих таблиц службы хранилища.

Вы можете использовать [этот пример шаблона](https://github.com/krnese/azure-quickstart-templates/tree/master/service-fabric-oms), а также изменить его в соответствии со своими потребностями.

Внесите следующие изменения:
1. Добавьте значения `omsWorkspaceName` и `omsRegion` в свои параметры, добавив следующий фрагмент кода в параметры, определенные в файле *template.json*. При необходимости вы можете изменить значения по умолчанию. Также добавьте два новых параметра в файл *parameters.json* для определения их значений при развертывании ресурсов:
    
    ```json
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "sfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
    ```

    Значения `omsRegion` должны соответствовать определенному набору значений. Выберите значение, ближайшее к развертыванию кластера.

2. При отправке любых журналов приложений в OMS убедитесь, что `applicationDiagnosticsStorageAccountType` и `applicationDiagnosticsStorageAccountName` добавлены в шаблон в качестве параметров. В противном случае добавьте их в раздел переменных и при необходимости измените их значения. Их можно также включить как параметры, используя формат выше.

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]"
    ```

3. Добавьте решение OMS Service Fabric в переменные шаблона:

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
    ```

4. Добавьте следующий код в конец раздела ресурсов после объявления кластерного ресурса Service Fabric.

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', parameters('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "id": "[resourceId('Microsoft.OperationsManagement/solutions/', variables('solution'))]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
    ```
    
    > [!NOTE]
    > Если вы добавили `applicationDiagnosticsStorageAccountName` в качестве переменной, замените каждую ссылку на этот объект на `variables('applicationDiagnosticsStorageAccountName')` вместо `parameters('applicationDiagnosticsStorageAccountName')`.

5. Разверните шаблон как обновление Resource Manager для кластера с помощью API `New-AzureRmResourceGroupDeployment` в модуле AzureRM PowerShell. Пример команды будет выглядеть так:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Azure Resource Manager определит, что эта команда — это обновление имеющегося ресурса. Он обработает только изменения между шаблоном, управляющим имеющимся развертыванием, и новым предоставленным шаблоном.

## <a name="deploy-oms-by-using-azure-powershell"></a>Развертывание OMS с помощью Azure PowerShell

Ресурс OMS Log Analytics можно также развернуть с помощью PowerShell. Для этого выполните команду `New-AzureRmOperationalInsightsWorkspace`. Чтобы использовать этот метод, обязательно установите [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1). Чтобы создать рабочую область OMS Log Analytics и добавить в нее решение Service Fabric, используйте следующий скрипт: 

```PowerShell

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<OMS Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

После этого выполните шаги, описанные в разделе выше, чтобы подключить OMS Log Analytics к соответствующей учетной записи хранения.

Вы также можете добавить другие решения или внести другие изменения в рабочую область OMS с помощью PowerShell. Дополнительные сведения см. в статье [Управление Log Analytics с помощью PowerShell](../log-analytics/log-analytics-powershell-workspace-configuration.md).

## <a name="next-steps"></a>Дополнительная информация
* [Разверните агент OMS](service-fabric-diagnostics-oms-agent.md) на узлах для сбора данных счетчиков производительности и статистики Docker, а также журналов для контейнера.
* Ознакомьтесь с функциями [поиска по журналам и запросов к журналам](../log-analytics/log-analytics-log-searches.md), которые являются частью решения Log Analytics.
* [Использование конструктора представлений для создания пользовательских представлений Log Analytics](../log-analytics/log-analytics-view-designer.md)
