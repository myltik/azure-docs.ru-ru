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
   ms.date="06/29/2015"
   ms.author="tomfitz"/>

# Расширенные операции с шаблонами

В этом разделе рассматривается операция копирования, а также вложенные шаблоны, с помощью которых можно выполнять расширенные задачи при развертывании ресурсов в среде Azure.

## copy

Позволяет выполнять итерацию заданное число раз при развертывании ресурса.
   
Эта операция копирования особенно удобна при работе с массивами, поскольку можно выполнить итерацию по каждому элементу в массиве. Функция **CopyIndex()** возвращает текущее значение для итерации. Можно развернуть три веб-сайта:

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

с помощью следующего шаблона.

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

Можно также использовать операцию копирования без массива. Например, может потребоваться добавить увеличивающийся номер в конец каждого развертываемого имени ресурса. Можно развернуть три веб-сайта:

- examplecopy-0
- examplecopy-1
- examplecopy-2

с помощью следующего шаблона.

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
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

В предыдущем примере можно заметить, что значение индекса изменяется от нуля до 2. Для смещения значения индекса можно передать значение в функцию **copyIndex()**, например **copyIndex(1)**. Число выполняемых итераций по-прежнему указывается в элементе копирования, но значение copyIndex сдвигается на указанное значение. Таким образом, если использовать шаблон, аналогичный приведенному в предыдущем примере, но указать **copyIndex(1)**, будут развернуты три следующих веб-сайта:

- examplecopy-1
- examplecopy-2
- examplecopy-3

## Вложенный шаблон

Может потребоваться объединить два шаблона или запустить дочерний шаблон из родительского. Для этого в главном шаблоне можно воспользоваться ресурсом развертывания, который указывает на вложенный шаблон. Задайте для свойства **templateLink** URI вложенного шаблона. Значения параметров для вложенного шаблона можно предоставить, указав значения непосредственно в шаблоне или привязав их к файлу параметров. В первом примере используется свойство **parameters** для непосредственного указания значения параметра.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/ArmTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

В следующем примере используется свойство **parametersLink** привязки к файлу параметров.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/ArmTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/ArmTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

## Дальнейшие действия
- [Создание шаблонов диспетчера ресурсов Azure](./resource-group-authoring-templates.md)
- [Функции шаблонов в диспетчере ресурсов Azure](./resource-group-template-functions.md)
- [Развертывание приложения с использованием шаблона диспетчера ресурсов Azure](azure-portal/resource-group-template-deploy.md)
- [Общие сведения о диспетчере ресурсов Azure](./resource-group-overview.md)

<!---HONumber=July15_HO4-->