---
title: "Создание приложения логики с помощью шаблона в Azure | Документация Майкрософт"
description: "Узнайте, как с помощью шаблона Azure Resource Manager развернуть приложение логики для определения рабочих процессов."
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2016
ms.author: LADocs; mandia
ms.translationtype: Human Translation
ms.sourcegitcommit: 2f407a428aa176cc5c2a3b6bb236b522bda5ab64
ms.openlocfilehash: 1305fdc4cf87905eef701ec5471d6329006c2b5f
ms.contentlocale: ru-ru
ms.lasthandoff: 01/31/2017


---
# <a name="create-a-logic-app-using-a-template"></a>Создание приложения логики с помощью шаблона
Шаблоны предоставляют быстрый способ применения различных соединителей в приложении логики. Logic Apps включает в себя шаблоны Azure Resource Manager для создания приложения логики, которое можно использовать для определения бизнес-процессов. При развертывании приложения логики можно указать, какие ресурсы развертывать и как определять параметры. Вы можете использовать этот шаблон для собственных бизнес-сценариев или настроить его в соответствии с вашими требованиями.

Дополнительные сведения о свойствах приложения логики см. в статье [Logic App Workflow Management API](https://msdn.microsoft.com/library/azure/mt643788.aspx) (API управления рабочим процессом приложения логики). 

Примеры определения см. в статье [Создание определений приложений логики](logic-apps-author-definitions.md). 

Дополнительные сведения о создании шаблонов см. в статье [Создание шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Полный шаблон см. [здесь](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json).

## <a name="what-you-deploy"></a>Что будет развернуто?
С помощью этого шаблона развертывается приложение логики.

Чтобы выполнить развертывание автоматически, нажмите следующую кнопку.  

[![Развертывание в Azure](media/logic-apps-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

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




