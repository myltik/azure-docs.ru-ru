<properties 
	pageTitle="Развертывание приложения API с помощью существующего шлюза" 
	description="Использование шаблона диспетчера ресурсов Azure для развертывания приложения API с помощью существующего шлюза и плана службы приложений." 
	services="app-service\api" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="tomfitz"/>

# Подготовка приложения API с помощью существующего шлюза

В этом разделе вы узнаете, как создать шаблон диспетчера ресурсов Azure, выполняющий развертывание приложения API Azure и существующего шлюза. Вы узнаете, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями.

Дополнительную информацию о создании шаблонов см. в статье [Создание шаблонов диспетчера ресурсов Azure](../resource-group-authoring-templates.md).

Дополнительную информацию о развертывании приложений см. в статье [Предсказуемое развертывание сложного приложения в Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).

Полный шаблон см. в статье [Приложение API с существующим шаблоном шлюза](https://github.com/Azure/azure-quickstart-templates/blob/master/201-api-app-gateway-existing/azuredeploy.json).

## Что именно развертывается

В этом шаблоне вы развернете приложение API, связанное с планом размещения существующей службы приложений и существующим шлюзом.

Чтобы выполнить развертывание автоматически, нажмите следующую кнопку.

[![Развертывание в Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-app-gateway-existing%2Fazuredeploy.json)

## Параметры

[AZURE.INCLUDE [app-service-api-deploy-parameters](../../includes/app-service-api-deploy-parameters.md)]

### hostingPlanId

Идентификатор существующего плана размещения.

    "hostingPlanId": {
      "type": "string"
    }

### hostingPlanSettings

Параметры существующего плана размещения.

    "hostingPlanSettings": {
      "type": "Object",
      "defaultValue": {
        "hostingEnvironment": ""
      }
    }

## Переменные

Этот шаблон определяет переменную, которая используется при развертывании ресурсов.

    "variables": {
      "packageId": "Microsoft.ApiApp"
    }
    
Значение используется ниже в качестве **variables('packageId')**.

## Развертываемые ресурсы

### Веб-приложение для размещения приложения API

Создает веб-приложение, в котором размещается приложение API.

Обратите внимание, что для параметра **kind** задано значение **apiApp**, что служит уведомлением для портала Azure о том, что это веб-приложение размещает приложение API. Портал скроет веб-приложение в колонке «Обзор» веб-приложения. Приложение содержит расширение для установки пустого пакета приложения API по умолчанию. Между приложением API и размещением веб-приложения определяется связь. Раздел параметров приложения содержит значения, необходимые для размещения приложения API.

    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2015-04-01",
      "name": "[parameters('apiAppName')]",
      "location": "[parameters('location')]",
      "kind": "apiApp",
      "resources": [
        {
          "type": "siteextensions",
          "apiVersion": "2015-02-01",
          "name": "[variables('packageId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          ],
          "properties": {
            "type": "WebRoot",
            "feed_url": "http://apiapps-preview.nuget.org/api/v2/",
            "version": "0.9.4"
          }
        },
        {
          "type": "providers/links",
          "apiVersion": "2015-01-01",
          "name": "Microsoft.Resources/apiApp",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          ],
          "properties": {
            "targetId": "[resourceId('Microsoft.AppService/apiapps', parameters('apiAppName'))]"
          }
        }
      ],
      "properties": {
        "name": "[parameters('apiAppName')]",
        "gatewaySiteName": "[parameters('gatewayName')]",
        "serverFarmId": "[parameters('hostingPlanId')]",
        "hostingEnvironment": "[parameters('hostingPlanSettings').hostingEnvironment]",
        "siteConfig": {
          "appSettings": [
            {
              "name": "EMA_MicroserviceId",
              "value": "[parameters('apiAppName')]"
            },
            {
              "name": "EMA_Secret",
              "value": "[parameters('apiAppSecret')]"
            },
            {
              "name": "EMA_RuntimeUrl",
              "value": "[concat('https://', parameters('gatewayName'), '.azurewebsites.net')]"
            },
            {
              "name": "WEBSITE_START_SCM_ON_SITE_CREATION",
              "value": "1"
            }
          ]
        }
      }
    }

### Приложение API

Создайте приложение API.

Обратите внимание, что имена веб-приложения и шлюза для размещения определяются в качестве свойств в приложении API.

    {
      "type": "Microsoft.AppService/apiapps",
      "apiVersion": "2015-03-01-preview",
      "name": "[parameters('apiAppName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "providers/links",
          "apiVersion": "2015-01-01",
          "name": "Microsoft.Resources/apiAppSite",
          "dependsOn": [
            "[resourceId('Microsoft.AppService/apiapps', parameters('apiAppName'))]"
          ],
          "properties": {
            "targetId": "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          }
        }
      ],
      "dependsOn": [
        "[resourceId('Microsoft.Web/sites/siteextensions', parameters('apiAppName'), variables('packageId'))]"
      ],
      "properties": {
        "package": {
          "id": "[variables('packageId')]"
        },
        "host": {
          "resourceName": "[parameters('apiAppName')]"
        },
        "gateway": {
          "resourceName": "[parameters('gatewayName')]"
        }
      }
    }


## Команды для выполнения развертывания

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-api-app-gateway-existing/azuredeploy.json

### Инфраструктура CLI Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-api-app-gateway-existing/azuredeploy.json


 

<!---HONumber=July15_HO3-->