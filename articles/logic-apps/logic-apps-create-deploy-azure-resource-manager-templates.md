---
title: Создание приложений логики с помощью шаблонов Azure Resource Manager | Документация Майкрософт
description: Создание и развертывание рабочих процессов приложений логики с помощью шаблонов Azure Resource Manager.
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 0772ed0e6cca98c4e59b563a23549909636d55d0
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298228"
---
# <a name="create-and-deploy-logic-apps-with-azure-resource-manager-templates"></a>Создание и развертывание приложений логики с помощью шаблонов Azure Resource Manager

Azure Logic Apps предоставляет шаблоны Azure Resource Manager, которые можно использовать не только для создания приложений логики для автоматизации рабочих процессов, но и для определения ресурсов и параметров, которые применяются при развертывании. Вы можете использовать этот шаблон для собственных бизнес-сценариев или настроить его в соответствии со своими требованиями. Узнайте больше о [шаблоне Resource Manager для приложений логики](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) и [структуре и синтаксисе шаблона Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

## <a name="define-the-logic-app"></a>Определение приложения логики

Этот пример определения приложения логики выполняется один раз в час и проверяет связь с расположением, указанным в параметре `testUri`.
Шаблон использует значения параметров для имени приложения логики (```logicAppName```) и расположения для проверки связи (```testUri```). Узнайте больше об [определении этих параметров в шаблоне](#define-parameters). В шаблоне также задано расположение приложения логики, совпадающее с расположением группы ресурсов. 

``` json
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
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "testURI": {
               "type": "string",
               "defaultValue": "[parameters('testUri')]"
            }
         },
         "triggers": {
            "Recurrence": {
               "type": "Recurrence",
               "recurrence": {
                  "frequency": "Hour",
                  "interval": 1
               }
            }
         },
         "actions": {
            "Http": {
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
``` 

<a name="define-parameters"></a>

### <a name="define-parameters"></a>Определение параметров

[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

Ниже приводится описание параметров в шаблоне.

| Параметр | ОПИСАНИЕ | Пример определения JSON | 
| --------- | ----------- | ----------------------- | 
| `logicAppName` | Определяет имя приложения логики, которое создает этот шаблон. | "logicAppName": { "type": "string", "metadata": { "description": "myExampleLogicAppName" } } |
| `testUri` | Определяет расположение для проверки связи. | "testUri": { "type": "string", "defaultValue": "http://azure.microsoft.com/status/feed/"} | 
||||

Узнайте больше о [REST API для определения и свойств рабочих процессов Logic Apps](https://docs.microsoft.com/rest/api/logic/workflows) и [создании определений приложений логики в формате JSON](logic-apps-author-definitions.md).

## <a name="deploy-logic-apps-automatically"></a>Автоматическое развертывание приложений логики

Чтобы создать и автоматически развернуть приложение логики в Azure, выберите **Развертывание в Azure**.

[![Развертывание в Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Это действие выполняет вход на портал Azure, где можно предоставить сведения о приложении логики и внести изменения в шаблон или параметры. Например, портал Azure предлагает ввести:

* имя подписки Azure;
* группу ресурсов, которую следует использовать;
* расположение приложения логики;
* имя приложения логики;
* тестовый универсальный код ресурса (URI);
* принятие указанных условий.

## <a name="deploy-logic-apps-with-commands"></a>Развертывание приложений логики с помощью команд

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup
``` 

### <a name="azure-cli"></a>Инфраструктура CLI Azure

```
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup
```

## <a name="get-support"></a>Получение поддержки

* Если у вас возникли вопросы, то посетите [форум Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Отправить идею по поводу возможности или проголосовать за нее вы можете на [сайте отзывов пользователей Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Мониторинг приложений логики](../logic-apps/logic-apps-monitor-your-logic-apps.md)