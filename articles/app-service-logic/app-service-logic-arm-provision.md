---
title: "Создание приложения логики с помощью шаблонов Azure Resource Manager в службе приложений Azure | Документация Майкрософт"
description: "Использование шаблона диспетчера ресурсов Azure для развертывания пустого приложения логики для определения рабочих процессов."
services: logic-apps
documentationcenter: 
author: MSFTMan
manager: erikre
editor: 
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 61ab873e77e3f7a691c815ba48c7f29b45269db9


---
# <a name="create-a-logic-app-using-a-template"></a>Создание приложения логики с помощью шаблона
Применение шаблона диспетчера ресурсов Azure для создания пустого приложения логики, которое можно использовать для определения рабочих процессов. Вы определите развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями.

Дополнительные сведения о свойствах приложения логики см. в статье [Logic App Workflow Management API](https://msdn.microsoft.com/library/azure/mt643788.aspx) (API управления рабочим процессом приложения логики). 

Примеры определения см. в статье [Создание определений приложений логики](app-service-logic-author-definitions.md). 

Дополнительные сведения о создании шаблонов см. в статье [Создание шаблонов Azure Resource Manager](../resource-group-authoring-templates.md).

Полный шаблон см. [здесь](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json).

## <a name="what-you-will-deploy"></a>Что именно развертывается
С помощью этого шаблона развертывается приложение логики.

Чтобы выполнить развертывание автоматически, нажмите следующую кнопку.  

[![Развертывание в Azure](media/app-service-logic-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## <a name="parameters"></a>Параметры
[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### <a name="testuri"></a>testUri
     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
      }

## <a name="resources-to-deploy"></a>Развертываемые ресурсы
### <a name="logic-app"></a>Приложение логики
Создает приложение логики.

В шаблонах используется значение параметра для имени приложения логики. Параметр задает в качестве расположения приложения логики то же расположение, что и у группы ресурсов. 

Это определение будет выполняться один раз в час, проверяя расположение, указанное в параметре **testUri** . 

    {
      "type": "Microsoft.Logic/workflows",
      "apiVersion": "2016-06-01",
      "name": "[parameters('logicAppName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "displayName": "LogicApp"
      },
      "properties": {
        "definition": {
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
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
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      }
    }


## <a name="commands-to-run-deployment"></a>Команды для выполнения развертывания
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Инфраструктура CLI Azure
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup






<!--HONumber=Nov16_HO3-->


