---
title: "Развертывание и обновление приложений и служб с помощью Azure Resource Manager | Документация Майкрософт"
description: "Узнайте, как развертывать приложения и службы в кластере Service Fabric с помощью шаблона Azure Resource Manager."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/02/2017
ms.author: dekapur
ms.openlocfilehash: 0ffa1f33c41ceb9f8cdd4c8c9e46f06efa4f89a7
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2017
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Управление приложениями и службами как ресурсами Azure Resource Manager

Вы можете развертывать приложения и службы в кластере Service Fabric с помощью Azure Resource Manager. Это означает, что теперь вместо развертывания приложений и управления ими с помощью PowerShell или интерфейса командной строки после ожидания готовности кластера можно определить эти приложения и службы в формате JSON и развертывать их с помощью того же шаблона Resource Manager, что и кластер. Процесс регистрации, подготовки и развертывания приложения происходит за один шаг.

Это рекомендуемый способ развертывания любых приложений установки, систем управления или приложений управления кластером, требуемых в кластере. К ним относятся [приложение для управления исправлениями](service-fabric-patch-orchestration-application.md), модули наблюдения и любые приложения, которые нужно запустить в кластере перед развертыванием других приложений или служб. 

Если это возможно, следует управлять приложениями как ресурсами Resource Manager, чтобы улучшить:
* Журнал аудита: Resource Manager выполняется аудит каждой операции и хранит подробный *журнал действий*, позволяющий отслеживать все изменения, внесенные в эти приложения и кластер.
* Управление доступом на основе ролей (RBAC): управление доступом к кластерам, а также приложениям, развернутым в кластере, можно осуществлять с помощью того же шаблона Resource Manager.
* Azure Resource Manager (посредством портала Azure) становится единым центром управления кластером и развертываниями важных приложений.

В следующем фрагменте кода показаны различные ресурсы, которыми можно управлять с помощью шаблона.

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```


## <a name="add-a-new-application-to-your-resource-manager-template"></a>Добавление нового приложения в шаблон Resource Manager

1. Подготовьте шаблон Resource Manager кластера для развертывания. Дополнительные сведения об этом приведены в разделе [Создание кластера Service Fabric в Azure с помощью Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).
2. Подумайте, какие приложения планируется развернуть в кластере. Возможно, среди них есть приложения, которые будут всегда работать, и другие приложения будут зависеть от них? Планируется ли развертывание систем управления кластером или приложений установки? Такими приложениями лучше всего управлять с помощью шаблона Resource Manager, как описано выше. 
3. Выяснив, какие приложения должны быть развернуты таким способом, эти приложения нужно упаковать, заархивировать в формате ZIP и поместить в файловый ресурс. Этот файловый ресурс должен быть доступен через конечную точку REST, чтобы Azure Resource Manager мог использовать его во время развертывания.
4. В шаблоне Resource Manager под объявлением кластера опишите свойства каждого приложения. Эти свойства включают в себя количество реплик или экземпляров, а также все цепочки зависимостей между ресурсами (другие приложения или службы). Полный список свойств приведен в [спецификации REST API Swagger](https://github.com/Azure/azure-rest-api-specs/blob/current/specification/servicefabric/resource-manager/Microsoft.ServiceFabric/2017-07-01-preview/servicefabric.json). Обратите внимание на то, что это не заменяет манифесты приложений или служб. Просто часть их содержимого указывается в шаблоне Resource Manager кластера. Ниже приведен пример шаблона, который включает в себя развертывание службы без отслеживания состояния *Service1* и службы с отслеживанием состояния *Service2* в качестве части приложения *Application1*.

  ```json
  {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "clusterName": {
        "type": "string",
        "defaultValue": "Cluster",
        "metadata": {
          "description": "Name of your cluster - Between 3 and 23 characters. Letters and numbers only"
        }
      },
      "applicationTypeName": {
        "type": "string",
        "defaultValue": "ApplicationType",
        "metadata": {
          "description": "The application type name"
        }
      },
      "applicationTypeVersion": {
        "type": "string",
        "defaultValue": "1",
        "metadata": {
          "description": "The application type version"
        }
      },
      "appPackageUrl": {
        "type": "string",
        "metadata": {
          "description": "The URL to the application package sfpkg file"
        }
      },
      "applicationName": {
        "type": "string",
        "defaultValue": "Application1",
        "metadata": {
          "description": "The application name"
        }
      },
      "serviceName": {
        "type": "string",
        "defaultValue": "Service1",
        "metadata": {
          "description": "The service name"
        }
      },
      "serviceTypeName": {
        "type": "string",
        "defaultValue": "Service1Type",
        "metadata": {
          "description": "The service type name"
        }
      },
      "serviceName2": {
        "type": "string",
        "defaultValue": "Service2",
        "metadata": {
          "description": "The service name"
        }
      },
      "serviceTypeName2": {
        "type": "string",
        "defaultValue": "Service2Type",
        "metadata": {
          "description": "The service type name"
        }
      }
    },
    "variables": {
      "clusterLocation": "[resourcegroup().location]"
    },
    "resources": [
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "appPackageUrl": "[parameters('appPackageUrl')]"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "typeName": "[parameters('applicationTypeName')]",
          "typeVersion": "[parameters('applicationTypeVersion')]",
          "parameters": {},
          "upgradePolicy": {
            "upgradeReplicaSetCheckTimeout": "01:00:00.0",
            "forceRestart": "false",
            "rollingUpgradeMonitoringPolicy": {
              "healthCheckWaitDuration": "00:02:00.0",
              "healthCheckStableDuration": "00:05:00.0",
              "healthCheckRetryTimeout": "00:10:00.0",
              "upgradeTimeout": "01:00:00.0",
              "upgradeDomainTimeout": "00:20:00.0"
            },
            "applicationHealthPolicy": {
              "considerWarningAsError": "false",
              "maxPercentUnhealthyDeployedApplications": "50",
              "defaultServiceTypeHealthPolicy": {
                "maxPercentUnhealthyServices": "50",
                "maxPercentUnhealthyPartitionsPerService": "50",
                "maxPercentUnhealthyReplicasPerPartition": "50"
              }
            }
          }
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateless",
          "serviceTypeName": "[parameters('serviceTypeName')]",
          "instanceCount": "-1",
          "partitionDescription": {
            "partitionScheme": "Singleton"
          },
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": []
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName2'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateful",
          "serviceTypeName": "[parameters('serviceTypeName2')]",
          "targetReplicaSetSize": "3",
          "minReplicaSetSize": "2",
          "replicaRestartWaitDuration": "00:01:00.0",
          "quorumLossWaitDuration": "00:02:00.0",
          "standByReplicaKeepDuration": "00:00:30.0",
          "partitionDescription": {
            "partitionScheme": "UniformInt64Range",
            "count": "5",
            "lowKey": "1",
            "highKey": "5"
          },
          "hasPersistedState": "true",
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": [],
          "defaultMoveCost": "Low"
        }
      }
    ]
  }
  ```

  > [!NOTE] 
  > Свойству *ApiVersion* нужно присвоить значение `"2017-07-01-preview"`. Этот шаблон также можно развертывать независимо от кластера, если кластер уже развернут.

5. Разверните шаблон. 

## <a name="manage-an-existing-application-via-resource-manager"></a>Управление существующим приложением с помощью Resource Manager

Если кластер уже запущен и в нем развернуто несколько приложений, которыми требуется управлять как ресурсами Resource Manager, то вместо удаления этих приложений и их повторного развертывания можно использовать вызов PUT с использованием тех же интерфейсов API, чтобы эти приложения были подтверждены как ресурсы Resource Manager. 


## <a name="next-steps"></a>Дальнейшие действия

* Используйте [интерфейс командной строки Service Fabric](service-fabric-cli.md) или [PowerShell](service-fabric-deploy-remove-applications.md) для развертывания других приложений в кластере. 
* [Обновите кластер Service Fabric](service-fabric-cluster-upgrade.md).

