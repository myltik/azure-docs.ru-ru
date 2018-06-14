---
title: Подготовка кэша Redis с помощью Azure Resource Manager | Документация Майкрософт
description: Используйте шаблон диспетчера ресурсов Azure для развертывания кэша Redis для Azure.
services: app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ce6f5372-7038-4655-b1c5-108f7c148282
ms.service: cache
ms.workload: web
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: wesmc
ms.openlocfilehash: b26116b974abbfe410b0a6ebc0186d73f4eea1bf
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
ms.locfileid: "27910747"
---
# <a name="create-a-redis-cache-using-a-template"></a>Создание кэша Redis с помощью шаблона
В этом разделе вы узнаете, как создать шаблон Azure Resource Manager, который развертывает кэш Redis для Azure. Кэш можно использовать с существующей учетной записи хранения для размещения данных диагностики. Вы узнаете, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями.

В настоящее время параметры диагностики являются общими для всех кэшей в одном регионе подписки. Обновление одного кэша в регионе влияет на все кэши в нем.

Дополнительные сведения о создании шаблонов см. в статье [Создание шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Полный шаблон доступен в разделе [Шаблон кэша Redis](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Доступны шаблоны Resource Manager для нового [уровня "Премиум"](cache-premium-tier-intro.md) . 
> 
> * [Создание кэша Premium Redis с кластеризацией](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
> * [Создание кэша Premium Redis с сохранением данных](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
> * [Создание кэша Premium Redis с использованием VNet и дополнительной кластеризации](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
> 
> Чтобы узнать о новых шаблонах, обратитесь к статье [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/) и выполните поиск по критерию `Redis Cache`.
> 
> 

## <a name="what-you-will-deploy"></a>Что именно развертывается
В этом шаблоне вы развернете кэш Azure Redis, который использует существующую учетную запись хранения для диагностических данных.

Чтобы выполнить развертывание автоматически, нажмите следующую кнопку.

[![Развертывание в Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Параметры
С помощью диспетчера ресурсов Azure можно определить параметры значений, которые должны указываться на этапе развертывания шаблона. В шаблоне есть раздел "Параметры", содержащий все значения параметров.
Для этих значений необходимо определить параметры, которые будут зависеть от развертываемого проекта либо от среды, в которой выполняется развертывание. Не определяйте параметры для значений, которые не меняются. Значение каждого параметра в шаблоне определяет развертываемые ресурсы. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
Расположение кэша Redis. Для наилучшей производительности используйте то же расположение, где находится приложение, которое будет пользоваться кэшем.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
Имя существующей учетной записи хранения, которую вы хотите использовать для диагностики. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort
Логическое значение, указывающее, следует ли разрешить доступ к портам, отличным от SSL.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus
Значение, указывающее, включена ли диагностика. Используйте значение ON или OFF.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }

## <a name="resources-to-deploy"></a>Развертываемые ресурсы
### <a name="redis-cache"></a>кэш Redis;
Создает кэш Redis для Azure.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-07-01",
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
          "location": "[parameters('redisCacheLocation')]",
          "dependsOn": [
            "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
          ],
          "properties": {
            "status": "[parameters('diagnosticsStatus')]",
            "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
          }
        }
      ]
    }



## <a name="commands-to-run-deployment"></a>Команды для выполнения развертывания
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache

### <a name="azure-cli"></a>Инфраструктура CLI Azure
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup


