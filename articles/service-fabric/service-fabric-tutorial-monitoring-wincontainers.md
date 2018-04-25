---
title: Мониторинг и диагностика контейнеров Windows в Azure Service Fabric | Документация Майкрософт
description: В этом руководстве вы настроите мониторинг и диагностику контейнера Windows, управляемого в Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 087dafe426b835d447c69a44f6842c41a48cec8c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-monitor-windows-containers-on-service-fabric-using-log-analytics"></a>Руководство. Мониторинг контейнера Windows в Service Fabric с помощью Log Analytics

Это третья часть руководства, в которой описывается настройка Log Analytics для мониторинга контейнеров Windows, управляемых в Service Fabric.

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * настройка Log Analytics для кластера Service Fabric;
> * использование рабочей области Log Analytics для просмотра и запроса журналов из контейнеров и узлов;
> * настройка агента OMS для сбора метрик контейнеров и узлов.

## <a name="prerequisites"></a>предварительным требованиям
Для работы с этим руководством вам потребуется:
- использовать имеющийся кластер в Azure или [создать новый с помощью данного руководства](service-fabric-tutorial-create-vnet-and-windows-cluster.md);
- [развернуть в нем контейнерное приложение](service-fabric-host-app-in-a-container.md).

## <a name="setting-up-log-analytics-with-your-cluster-in-the-resource-manager-template"></a>Настройка Log Analytics для кластера в шаблоне Resource Manager

В случае, если вы использовали [шаблон](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Tutorial), предоставленный в первой части этого руководства, он должен содержать указанные ниже дополнения к универсальному шаблону Azure Resource Manager для Service Fabric. В случае, если вы использовали собственный кластер, для которого необходимо настроить мониторинг контейнеров с помощью Log Analytics, сделайте следующее:
* Внесите приведенные ниже изменения в шаблон Resource Manager.
* [Разверните этот шаблон](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) с помощью PowerShell, чтобы обновить кластер. Azure Resource Manager определит, что ресурс уже существует, поэтому обновит его.

### <a name="adding-log-analytics-to-your-cluster-template"></a>Добавление Log Analytics в шаблон кластера

Внесите следующие изменения в файл *template.json*.

1. Добавьте расположение рабочей области Log Analytics и имя раздела *parameters*:
    
    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
      "metadata": {
        "description": "Name of your Log Analytics Workspace"
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
        "description": "Specify the Azure Region for your Log Analytics workspace"
      }
    }
    ```

    Чтобы изменить значение какого-либо параметра, добавьте такой же параметр в свой файл *template.parameters.json* и измените его значение.

2. Добавьте имя решения и решение в раздел *variables*. 
    
    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. Добавьте OMS Microsoft Monitoring Agent в качестве расширения виртуальной машины. Найдите ресурс масштабируемых наборов виртуальных машин: *resources* > *"apiVersion": "[variables('vmssApiVersion')]"*. В раздел *properties* > *virtualMachineProfile* > *extensionProfile* > *extensions* добавьте следующее описание расширения под расширением *ServiceFabricNode*. 
    
    ```json
    {
        "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
        "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
            },
            "protectedSettings": {
                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
            }
        }
    },
    ```

4. Добавьте рабочую область Log Analytics в качестве отдельного ресурса. Добавьте приведенный ниже код в раздел *resources* после ресурса масштабируемых наборов виртуальных машин.
    
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
            },
            {
                "apiVersion": "2015-11-01-preview",
                "name": "System",
                "type": "datasources",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsEvent",
                "properties": {
                    "eventLogName": "System",
                    "eventTypes": [
                        {
                            "eventType": "Error"
                        },
                        {
                            "eventType": "Warning"
                        },
                        {
                            "eventType": "Information"
                        }
                    ]
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('omsSolutionName')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('omsSolutionName')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('omsSolution'))]",
            "promotionCode": ""
        }
    },
    ```

[Здесь](https://github.com/ChackDan/Service-Fabric/blob/master/ARM%20Templates/Tutorial/azuredeploy.json) доступен пример шаблона (из в первой части данного руководства) со всеми этими изменениями, который можно использовать для справки, если потребуется. Описанные изменения позволяют добавить рабочую область Log Analytics в группу ресурсов. Рабочая область продолжит собирать события платформы Service Fabric из таблиц хранилища, настроенных для агента [системы диагностики Microsoft Azure](service-fabric-diagnostics-event-aggregation-wad.md). Агент OMS (Microsoft Monitoring Agent) также был добавлен на каждый узел кластера как расширение виртуальной машины. Это означает, что при масштабировании кластера агент автоматически настраивается на каждом компьютере и привязывается к той же рабочей области.

Разверните шаблон с новыми изменениями, чтобы обновить текущий кластер. После этого ресурсы Log Analytics должны отобразиться в вашей группе ресурсов. Когда кластер будет готов к работе, разверните в нем свое контейнерное приложение. На следующем шаге мы настроим мониторинг контейнеров.

## <a name="add-the-container-monitoring-solution-to-your-log-analytics-workspace"></a>Добавление решения мониторинга контейнеров в рабочую область Log Analytics

Чтобы настроить решение мониторинга контейнеров в рабочей области, найдите *решение мониторинга контейнеров* и создайте ресурс контейнеров (в категории "Мониторинг и управление").

![Добавление решения "Контейнеры"](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

При появлении запроса о *рабочей области Log Analytics* выберите рабочую область, которая была создана в вашей группе ресурсов, и нажмите кнопку **Создать**. *Решение мониторинга контейнеров* будет добавлено в вашу рабочую область. После этого агент OMS, развернутый с помощью шаблона, автоматически начнет сбор журналов и статистики Docker. 

Вернитесь к своей *группе ресурсов*, в которой должно появиться добавленное решение мониторинга. Если щелкнуть его, должна отобразиться целевая страница с информацией о числе выполняемых образов контейнеров. 

*Обратите внимание на то, что у меня выполняются 5 экземпляров контейнера fabrikam из [второй части](service-fabric-host-app-in-a-container.md) руководства*.

![Целевая страница решения мониторинга контейнеров](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

Если щелкнуть **Решение мониторинга контейнеров**, можно перейти к более подробной панели мониторинга. В ней можно прокручивать несколько панелей, а также выполнять запросы к Log Analytics. 

*Примите к сведению, что начиная с сентября 2017 года решение будет обновляться. Игнорируйте любые ошибки из-за событий Kubernetes, так как мы работаем над интеграцией нескольких оркестраторов в одном решении.*

Так как агент собирает журналы Docker, по умолчанию он отображает *stdout* и *stderr*. Если прокрутить страницу вправо, вы увидите перечень образов контейнеров, состояние, метрики и примеры запросов, которые можно выполнить, чтобы получить более полезные данные. 

![Панель мониторинга решения мониторинга контейнеров](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Щелкните любую из этих панелей, чтобы перейти к запросу Log Analytics, создающему отображаемое значение. Измените запрос на *\**, чтобы просмотреть различные виды журналов, сбор которых выполняется. Здесь можно выполнить запрос или применить фильтр по производительности контейнеров или журналам. Можно также просмотреть события платформы Service Fabric. Агенты также постоянно передают пульс с каждого узла, который можно просмотреть, чтобы в случае изменения конфигурации кластера проверить, собираются по-прежнему ли данные со всех компьютеров.   

![Запрос контейнера](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-oms-agent-to-pick-up-performance-counters"></a>Настройка агента OMS для сбора счетчиков производительности

Еще одним преимуществом агента OMS является то, что но дает возможность изменить собираемые счетчики производительности, воспользовавшись пользовательским интерфейсом OMS, а не настраивая агент системы диагностики Azure и каждый раз выполняя обновление с помощью шаблона Resource Manager. Чтобы сделать это, щелкните **Портал OMS** на целевой странице решения мониторинга контейнеров (или Service Fabric).

![Портал OMS](./media/service-fabric-tutorial-monitoring-wincontainers/oms-portal.png)

Вы перейдете к своей рабочей области на портале OMS, где сможете просмотреть решения, создать настраиваемые панели мониторинга, а также настроить агент OMS. 
* Щелкните **шестеренку** в правом верхнем углу экрана, чтобы открыть меню *Параметры*.
* Щелкните **Подключенные источники** > **Серверы с Windows**, чтобы убедиться в наличии *5 подключенных компьютеров Windows*.
* Щелкните **Данные** > **Счетчики производительности Windows**, чтобы найти и добавить новые счетчики производительности. Вы увидите список рекомендаций Log Analytics по счетчикам производительности, которые можно собирать, а также параметр для поиска других счетчиков. Щелкните **Добавить выбранные счетчики производительности**, чтобы начать сбор предлагаемых метрик.

    ![Счетчики производительности](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters.png)

Вернувшись на портал Azure, через несколько минут **обновите** решение мониторинга контейнеров. В нем должны начать появляться данные о *производительность компьютеров*. Это поможет вам понять, как используются ресурсы. Кроме того, эти метрики можно использовать для принятия правильных решений о масштабировании кластера или для проверки соответствия балансировки нагрузки кластера ожидаемым показателям.

*Примечание. Убедитесь, что фильтры времени правильно настроены для использования этих метрик.* 

![Счетчики производительности 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)


## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * настройка Log Analytics для кластера Service Fabric;
> * использование рабочей области Log Analytics для просмотра и запроса журналов из контейнеров и узлов;
> * настройка агента Log Analytics для сбора метрик контейнеров и узлов.

Теперь, когда вы настроили мониторинг для контейнерного приложения, попробуйте выполнить следующее.

* Настройте Log Analytics для кластера Linux, выполнив действия, аналогичные приведенным выше. Используйте в качестве образца [этот шаблон](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux), чтобы внести изменения в шаблон Resource Manager.
* Настройте в Log Analytics [автоматические оповещения](../log-analytics/log-analytics-alerts.md), которые помогают выполнять обнаружение и диагностику.
* Просмотрите список [рекомендуемых счетчиков производительности](service-fabric-diagnostics-event-generation-perf.md) Service Fabric, которые можно настроить для кластеров.
* Ознакомьтесь с функциями [поиска по журналам и запросов к журналам](../log-analytics/log-analytics-log-searches.md), которые являются частью Log Analytics.