<properties
   pageTitle="Расширенные операции с шаблонами в диспетчере ресурсов Azure"
   description="В этом разделе описано использование вложенных шаблонов и операции копирования в шаблоне диспетчера ресурсов Azure при развертывании приложений в среде Azure."
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz;ilygre"/>

# Расширенные операции с шаблонами

В этом разделе рассматривается операция копирования, а также вложенные шаблоны, с помощью которых можно выполнять расширенные задачи при развертывании ресурсов в среде Azure.

## копирование

Позволяет пройти по элементам массива и использовать каждый из них при развертывании ресурса.
   
В примере ниже развертываются три веб-сайта под названиями examplecopy-Contoso, examplecopy-Fabrikam и examplecopy-Coho.

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      },
      "count": { 
         "type": "int", 
         "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {} 
      } 
    ]

## Вложенный шаблон

Иногда бывает нужно объединить два шаблона или запустить дочерний шаблон из родительского. Для этого в главном шаблоне можно воспользоваться ресурсом развертывания, с помощью которого можно развернуть дочерний шаблон. Необходимо указать URI вложенного шаблона, как показано ниже.

    "variables": {"templatelink":"https://www.contoso.com/ArmTemplates/newStorageAccount.json"}, 
    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {"uri":"[variables('templatelink')]","contentVersion":"1.0.0.0"}, 
           "parameters": { 
              "StorageAccountName":{"value":"[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

## Дальнейшие действия
- [Создание шаблонов диспетчера ресурсов Azure](./resource-group-authoring-templates.md)
- [Функции шаблонов в диспетчере ресурсов Azure](./resource-group-template-functions.md)
- [Развертывание приложения с использованием шаблона диспетчера ресурсов Azure](./resouce-group-template-deploy.md)
- [Общие сведения о диспетчере ресурсов Azure](./resource-group-overview.md)

<!--HONumber=52-->
