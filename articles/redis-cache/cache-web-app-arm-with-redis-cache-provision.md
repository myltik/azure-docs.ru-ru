<properties 
	pageTitle="Подготовка веб-приложения с кэшем Redis" 
	description="Используйте шаблон диспетчера ресурсов Azure для развертывания веб-приложения с кэшем Redis." 
	services="app-service" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/16/2015" 
	ms.author="tomfitz"/>

# Создание веб-приложения и кэша Redis с помощью шаблона

В этом разделе вы узнаете, как создать шаблон диспетчера ресурсов Azure для развертывания веб-приложения Azure с кэшем Redis. Вы узнаете, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями.

Дополнительную информацию о создании шаблонов см. в статье [Создание шаблонов диспетчера ресурсов Azure](../resource-group-authoring-templates.md).

Полный шаблон см. в разделе [Шаблон веб-приложения с кэшем Redis](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json).

## Что именно развертывается

В этом шаблоне будут развернуты перечисленные ниже компоненты.

- Веб-приложение Azure
- Кэш Redis для Azure.

Чтобы выполнить развертывание автоматически, нажмите следующую кнопку.

[![Развертывание в Azure](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## Указываемые параметры

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[AZURE.INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]



## Развертываемые ресурсы

[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### Кэш Redis

Создает кэш Redis для Azure, используемый с веб-приложением. Имя кэша указывается в параметре **redisCacheName**.

Этот шаблон создает кэш в той же папке, где находится веб-приложение, что рекомендуется для повышения производительности.

    {
      "apiVersion": "2014-04-01-preview",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('siteLocation')]",
      "properties": {
        "sku": {
          "name": "[parameters('redisCacheSKU')]",
          "family": "[parameters('redisCacheFamily')]",
          "capacity": "[parameters('redisCacheCapacity')]"
        },
        "redisVersion": "[parameters('redisCacheVersion')]",
        "enableNonSslPort": true
      }
    }

### Веб-приложение

Создает веб-приложение с именем, указанным в параметре **siteName**.

Обратите внимание, что веб-приложение настроено со свойствами параметров приложения, которые позволяют ему работать с кэшем Redis. Эти параметры приложения динамически создаются на основании значений, предоставленных во время развертывания.
        
    {
      "apiVersion": "2015-04-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[parameters('siteLocation')]",
      "dependsOn": [
          "[resourceId('Microsoft.Web/serverFarms', parameters('hostingPlanName'))]",
          "[resourceId('Microsoft.Cache/Redis', parameters('redisCacheName'))]"
      ],
      "properties": {
          "serverFarmId": "[parameters('hostingPlanName')]"
      },
      "resources": [
          {
              "apiVersion": "2015-06-01",
              "type": "config",
              "name": "web",
              "dependsOn": [
                  "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
              ],
              "properties": {
                  "appSettings": [
                      {
                          "name": "REDIS_HOST",
                          "value": "[concat(parameters('siteName'), '.redis.cache.windows.net:6379')]"
                      },
                      {
                          "name": "REDIS_KEY",
                          "value": "[listKeys(resourceId('Microsoft.Cache/Redis', parameters('redisCacheName')), '2014-04-01').primaryKey]"
                      }
                  ]
              }
          }
      ]
    }



## Команды для выполнения развертывания

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### Инфраструктура CLI Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup

<!---HONumber=AcomDC_1217_2015-->