---
title: "Настройка мониторинга с помощью OMS Log Analytics в Azure Service Fabric | Документация Майкрософт"
description: "Узнайте, как настроить визуализацию и анализ событий в OMS для мониторинга кластеров Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/31/2017
ms.author: dekapur
ms.openlocfilehash: 32c09f06cea97024437e7e339407d344194a14ae
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2017
---
# <a name="set-up-oms-log-analytics-for-a-cluster"></a>Настройка OMS Log Analytics для кластера

Рабочую область OMS можно настроить в Azure Resource Manager или Azure Marketplace. Первый вариант подходит, если необходимо сохранить шаблон развертывания для будущего использования. Развертывание через Marketplace проще, если вы уже развернули кластер с включенной диагностикой.

> [!NOTE]
> Необходимо включить систему диагностики кластера для просмотра событий уровня кластера или платформы, настройки OMS и успешного мониторинга кластера.

## <a name="deploying-oms-using-a-resource-manager-template"></a>Развертывание OMS с использованием шаблона Resource Manager

При развертывании кластера с помощью шаблона Resource Manager этот шаблон также может создать рабочую область OMS, добавить в нее решение Service Fabric и настроить в ней чтение данных из соответствующих таблиц службы хранилища.

[Вот пример шаблона](https://azure.microsoft.com/resources/templates/service-fabric-oms/), который можно использовать и изменять в соответствии с требованиями. Дополнительные шаблоны, которые предоставляют различные возможности для настройки рабочей области OMS, можно найти на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS).

Ниже перечислены основные изменения:

1. Добавьте `omsWorkspaceName` и `omsRegion` к своим параметрам. Это означает добавление следующего фрагмента кода в параметры, определенные в файле *template.json*. При необходимости вы можете изменить значения по умолчанию. Кроме того, следует добавить два новых параметра в файл *parameters.json* для определения их значений при развертывании ресурсов:
    
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

    Значения `omsRegion` должны соответствовать определенному набору значений. Необходимо выбрать ближайший к развертыванию кластера.

2. При отправке любых журналов приложений в OMS убедитесь, что `applicationDiagnosticsStorageAccountType` и `applicationDiagnosticsStorageAccountName` добавлены в шаблон в качестве параметров. В противном случае добавьте их в раздел переменных следующим образом и при необходимости измените их значения. При желании их также можно включить в качестве параметров, используя формат выше.

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]",
    ```

3. Добавьте решение OMS Service Fabric в переменные шаблона:

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric",
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
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
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

5. Разверните шаблон в качестве обновления кластера Resource Manager. Это можно сделать с помощью API `New-AzureRmResourceGroupDeployment` в модуле AzureRM PowerShell. Пример команды будет выглядеть так:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Azure Resource Manager сможет определить, что это обновление имеющегося ресурса. Он обработает только изменения между шаблоном, управляющим имеющимся развертыванием, и новым предоставленным шаблоном.

## <a name="deploying-oms-using-azure-marketplace"></a>Развертывание OMS с помощью Azure Marketplace

Если вы предпочитаете добавить рабочую область OMS после развертывания кластера, перейдите в Azure Marketplace (на портале) и выполните поиск по запросу *Аналитика Service Fabric*.

1. В области навигации слева щелкните **Создать**. 

2. Найдите *Аналитика Service Fabric*. Щелкните появившийся ресурс.

3. Нажмите кнопку **Создать**

    ![Аналитика Service Fabric OMS в Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. В окне создания Аналитики Service Fabric щелкните **Создать рабочую область**, чтобы появилось поле *Рабочая область OMS*, а затем выберите **Создать новую рабочую область**. Укажите необходимые записи. Единственным требованием является совпадение подписки для кластера Service Fabric и рабочей области OMS. Когда завершится проверка записей, начнется развертывание рабочей области OMS. Это займет не более нескольких минут.

5. Когда процесс завершится, снова щелкните **Создать** в нижней части окна создания Аналитики Service Fabric. Убедитесь, что новая рабочая область отображается в разделе *Рабочая область OMS*. В таком случае решение будет добавлено в только что созданную рабочую область.

6. Рабочая область по прежнему должна быть подключена к данным диагностики, поступающим из кластера. Перейдите к созданной группе ресурсов, в которой вы создали решение "Аналитика Service Fabric". Вы увидите в ней элемент *ServiceFabric (\<имя_рабочей_области_OMS\>)*. Щелкните это решение, чтобы открыть страницу обзора, на которой вы можете изменить параметры решения и (или) рабочей области, а также перейти на портал OMS.

7. В меню навигации слева щелкните **Журналы учетной записи хранения** в разделе *Источники данных рабочей области*.

8. На странице *Журналы учетной записи хранения* щелкните **Добавить** в верхней части, чтобы добавить в рабочую область журналы кластера.

9. Щелкните элемент **Учетная запись хранения** и добавьте учетную запись хранения, созданную в кластере. Если вы использовали имя по умолчанию, она называется *sfdg\<имя_группы_ресурсов\>*. Вы можете проверить, какое значение указано для параметра `applicationDiagnosticsStorageAccountName` в шаблоне Azure Resource Manager, на основе которого вы развертывали кластер. Если имя не отображается, прокрутите страницу до конца и щелкните **Загрузить еще**. Щелчком выберите нужное имя учетной записи хранения.

10. Теперь укажите значение **События Service Fabric** для параметра *Тип данных*.

11. При этом для параметра *Источник* будет автоматически установлено значение *WADServiceFabric\*EventTable*.

12. Щелкните **ОК**, чтобы подключить рабочую область к журналам кластера.

    ![Добавление журналов учетной записи хранения к OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Теперь учетная запись будет отображаться в списке *Журналы учетной записи хранения* в разделе источников данных для рабочей области.

Это означает, что вы успешно добавили решение "Аналитика Service Fabric" в рабочую область OMS Log Analytics и подключили ее к платформе кластера и таблице журналов приложения. Аналогичным образом вы можете добавить в рабочую область дополнительные источники.

## <a name="next-steps"></a>Дальнейшие действия
* [Разверните агент OMS](service-fabric-diagnostics-oms-agent.md) на узлах для сбора данных счетчиков производительности и статистики Docker, а также журналов для контейнера.
* Ознакомьтесь с функциями [поиска по журналам и запросов к журналам](../log-analytics/log-analytics-log-searches.md), которые являются частью решения Log Analytics.
* [Использование конструктора представлений для создания пользовательских представлений Log Analytics](../log-analytics/log-analytics-view-designer.md)
