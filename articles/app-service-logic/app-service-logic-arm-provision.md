<properties 
	pageTitle="Создание приложения логики с помощью шаблонов диспетчера ресурсов Azure в службе приложений Azure | Microsoft Azure" 
	description="Использование шаблона диспетчера ресурсов Azure для развертывания пустого приложения логики для определения рабочих процессов." 
	services="app-service\logic" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/16/2015" 
	ms.author="tomfitz"/>

# Создание приложения логики с помощью шаблона

Применение шаблона диспетчера ресурсов Azure для создания пустого приложения логики, которое можно использовать для определения рабочих процессов. Вы определите развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями.

Дополнительную информацию о свойствах приложения логики см. в статье [API управления рабочим процессом с помощью приложения логики](https://msdn.microsoft.com/library/azure/dn948513.aspx).

Примеры собственно определения см. в статье [Создание определений приложений логики](app-service-logic-author-definitions.md).

Дополнительную информацию о создании шаблонов см. в статье [Создание шаблонов диспетчера ресурсов Azure](../resource-group-authoring-templates.md).

Полный шаблон см. в разделе [Шаблон приложения логики](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json).

## Что именно развертывается

С помощью этого шаблона развертывается приложение логики.

Чтобы выполнить развертывание автоматически, нажмите следующую кнопку.

[![Развертывание в Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## Параметры

[AZURE.INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### testUri

     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/status/feed/"
      }
    
## Развертываемые ресурсы

### План службы приложений

Создает план службы приложений.

Используется то же расположение, что и для группы развертываемых ресурсов.

    {
        "apiVersion": "2014-06-01",
        "name": "[parameters('svcPlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "AppServicePlan"
        },
        "properties": {
            "name": "[parameters('svcPlanName')]",
            "sku": "[parameters('sku')]",
            "workerSize": "[parameters('svcPlanSize')]",
            "numberOfWorkers": 1
        }
    }

### Приложение логики

Создает приложение логики.

В шаблонах используется значение параметра для имени приложения логики. Параметр задает в качестве расположения приложения логики то же расположение, что и у группы ресурсов.

Это определение будет выполняться один раз в час, проверяя расположение, указанное в параметре **testUri**.

    {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-02-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "LogicApp"
        },
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "testURI": {
                        "type": "string",
                        "defaultValue": "[parameters('testUri')]"
                    }
                },
                "triggers": {
                    "recurrence": {
                        "type": "recurrence",
                        "recurrence": {
                            "frequency": "Hour",
                            "interval": 1
                        }
                    }
                },
                "actions": {
                    "http": {
                        "type": "Http",
                        "inputs": {
                            "method": "GET",
                            "uri": "@parameters('testUri')"
                        }
                    }
                },
                "outputs": { }
            },
            "parameters": { }
        }
    }

## Команды для выполнения развертывания

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### Инфраструктура CLI Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup


 

<!---HONumber=AcomDC_1217_2015-->