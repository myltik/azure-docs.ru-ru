<properties 
	pageTitle="Подготовка кэша Redis | Microsoft Azure" 
	description="Используйте шаблон диспетчера ресурсов Azure для развертывания кэша Redis для Azure." 
	services="app-service" 
	documentationCenter="" 
	authors="steved0x" 
	manager="Erikre" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="web" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2016" 
	ms.author="sdanie"/>

# Создание кэша Redis с помощью шаблона

В этом разделе вы узнаете, как создать шаблон диспетчера ресурсов Azure, который развертывает кэш Redis для Azure. Кэш можно использовать с существующей учетной записи хранения для размещения данных диагностики. Вы узнаете, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями.

В настоящее время параметры диагностики являются общими для всех кэшей в одном регионе подписки. Обновление одного кэша в регионе повлияет на все кэши в нем.

Дополнительную информацию о создании шаблонов см. в статье [Создание шаблонов диспетчера ресурсов Azure](../resource-group-authoring-templates.md).

Полный шаблон см. в разделе [Шаблон кэша Redis](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

>[AZURE.NOTE] Доступны шаблоны ARM для нового [уровня Premium](cache-premium-tier-intro.md).
>
>-    [Создание кэша Premium Redis с кластеризацией](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
>-    [Создание кэша Premium Redis с сохранением данных](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
>-    [Создание кэша Premium Redis с использованием VNet и дополнительной кластеризации](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
>
>Чтобы узнать о новых шаблонах, обратитесь к статье [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/) и выполните поиск по критерию `Redis Cache`.

## Что именно развертывается

В этом шаблоне вы развернете кэш Azure Redis, который использует существующую учетную запись хранения для диагностических данных.

Чтобы выполнить развертывание автоматически, нажмите следующую кнопку.

[![Развертывание в Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## Параметры

С помощью диспетчера ресурсов Azure можно определить параметры значений, которые должны указываться на этапе развертывания шаблона. В шаблоне есть раздел "Параметры", содержащий все значения параметров. Для этих значений необходимо определить параметры, которые будут зависеть от развертываемого проекта либо от среды, в которой выполняется развертывание. Не задавайте параметры для значений, которые не меняются. Значение каждого параметра в шаблоне определяет развертываемые ресурсы.

Далее следует описание каждого параметра в шаблоне.

[AZURE.INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### redisCacheLocation

Расположение кэша Redis. Для наилучшей производительности используйте то же расположение, где находится приложение, которое будет пользоваться кэшем.

    "redisCacheLocation": {
      "type": "string"
    }

### existingDiagnosticsStorageAccountName

Имя существующей учетной записи хранения, которую вы хотите использовать для диагностики.

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### enableNonSslPort

Логическое значение, указывающее, следует ли разрешить доступ к портам, отличным от SSL.

    "enableNonSslPort": {
      "type": "bool"
    }

### diagnosticsStatus

Значение, указывающее, включена ли диагностика. Используйте значение ON или OFF.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }
    
## Развертываемые ресурсы

### Кэш Redis

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


## Команды для выполнения развертывания

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache -redisCacheLocation "West US"

### Инфраструктура CLI Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup

<!---HONumber=AcomDC_0713_2016-->