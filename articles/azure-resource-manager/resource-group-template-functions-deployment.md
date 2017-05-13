---
title: "Функции развертывания для шаблонов Azure Resource Manager | Документация Майкрософт"
description: "Описывает функции, используемые в шаблоне Azure Resource Manager для получения сведений о развертывании."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: ce888415b6a5f82fb3d49834b055f8afe97442a8
ms.contentlocale: ru-ru
ms.lasthandoff: 04/28/2017


---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Функции развертывания для шаблонов Azure Resource Manager 

Диспетчер ресурсов предоставляет следующие функции для получения значений из разделов шаблонов и значений, связанных с развертыванием:

* [deployment](#deployment)
* [parameters](#parameters)
* [variables](#variables)

Сведения о получении значений из ресурсов, групп ресурсов или подписки см. в разделе [Функции для работы с ресурсами](resource-group-template-functions-resource.md).

<a id="deployment" />

## <a name="deployment"></a>Развертывание
`deployment()`

Возвращает сведения о текущей операции развертывания.

### <a name="examples"></a>Примеры

В следующем примере возвращается объект развертывания:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

В следующем примере показано использование deployment() для ссылки на другой шаблон в зависимости от URI родительского шаблона.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

### <a name="return-value"></a>Возвращаемое значение

Эта функция возвращает объект, который передается во время развертывания. Свойства в возвращаемом объекте зависят от того, передается ли объект развертывания в виде ссылки или встроенного объекта. Когда объект развертывания передается встроенным (например, при использовании параметра **-TemplateFile** в Azure PowerShell для определения локального файла), формат возвращаемого объекта будет таким:

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Когда объект передается в виде ссылки, как, например, при использовании параметра **-TemplateUri** для указания на удаленный объект, объект возвращается в следующем формате. 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```



<a id="parameters" />

## <a name="parameters"></a>parameters
`parameters(parameterName)`

Возвращает значение параметра. Указанное имя параметра должно быть определено в разделе параметров шаблона.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| имя_параметра |Да |string |Имя параметра, который требуется вернуть. |

### <a name="examples"></a>Примеры

В следующем примере показано упрощенное использование функции parameters.

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2014-06-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="return-value"></a>Возвращаемое значение

Тип параметра.

<a id="variables" />

## <a name="variables"></a>variables
`variables(variableName)`

Возвращает значение переменной. Указанное имя переменной должно быть определено в разделе переменных шаблона.

### <a name="parameters"></a>Параметры

| Параметр | Обязательно | Тип | Описание |
|:--- |:--- |:--- |:--- |
| variableName |Да |Строка |Имя переменной, которую необходимо вернуть. |

### <a name="examples"></a>Примеры

В следующем примере используется значение переменной.

```json
"variables": {
  "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
],
```

### <a name="return-value"></a>Возвращаемое значение

Тип переменной.

## <a name="next-steps"></a>Дальнейшие действия
* Описание разделов в шаблоне Azure Resource Manager см. в статье [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Инструкции по объединению нескольких шаблонов см. в статье [Использование связанных шаблонов в Azure Resource Manager](resource-group-linked-templates.md).
* Указания по выполнению заданного количества циклов итерации при создании типа ресурса см. в статье [Создание нескольких экземпляров ресурсов в Azure Resource Manager](resource-group-create-multiple.md).
* Указания по развертыванию созданного шаблона см. в статье, посвященной [развертыванию приложения с помощью шаблона Azure Resource Manager](resource-group-template-deploy.md).


