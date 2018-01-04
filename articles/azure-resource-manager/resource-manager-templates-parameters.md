---
title: "Раздел параметров шаблона Azure Resource Manager | Документы Microsoft"
description: "Описывает раздел параметров шаблонов диспетчера ресурсов Azure с помощью декларативного синтаксиса JSON."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/11/2017
ms.author: tomfitz
ms.openlocfilehash: 7d0f53751bf529d52c156a8b9319b10560eb8997
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2017
---
# <a name="parameters-section-of-azure-resource-manager-templates"></a>Раздел параметров шаблонов диспетчера ресурсов Azure
В разделе параметров шаблона указываются значения, которые вы можете вводить во время развертывания ресурсов. Значения этих параметров позволяют настраивать развертывание путем предоставления значений, предназначенных для конкретной среды (например, для среды разработки, тестирования и рабочей среды). Предоставление параметров в шаблоне не является обязательным требованием, однако без параметров шаблон всегда будет развертывать одни и те же ресурсы с одинаковыми именами, расположениями и свойствами.

Существует ограничение в 255 параметров в шаблоне. С помощью объектов, которые содержат несколько свойств, как показано в данной статье, можно уменьшить число параметров.

## <a name="define-and-use-a-parameter"></a>Определение и использование параметра

Пример определения простого параметра. Он определяет имя параметра, а также указывает, что он принимает строковое значение. Параметр принимает только значения, которые имеют смысл для намеченному назначению. Он указывает значение по умолчанию, если значение не указано во время развертывания. Наконец этот параметр содержит описание использования. 

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }   
}
```

В шаблоне ссылки на значение параметра со следующим синтаксисом:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="available-properties"></a>Доступные свойства

В предыдущем примере показано только некоторые свойства, которые можно использовать в разделе «параметр». Ниже приведены доступные свойства.

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": "<default-value-of-parameter>",
        "allowedValues": [ "<array-of-allowed-values>" ],
        "minValue": <minimum-value-for-int>,
        "maxValue": <maximum-value-for-int>,
        "minLength": <minimum-length-for-string-or-array>,
        "maxLength": <maximum-length-for-string-or-array-parameters>,
        "metadata": {
            "description": "<description-of-the parameter>" 
        }
    }
}
```

| Имя элемента | Требуется | ОПИСАНИЕ |
|:--- |:--- |:--- |
| имя_параметра |Yes |Имя параметра. Должно быть допустимым идентификатором JavaScript. |
| Тип |Yes |Тип значения параметра. Допустимые типы и значения являются **строка**, **secureString**, **int**, **bool**, **объекта**, **secureObject**, и **массива**. |
| defaultValue |Нет  |Значение параметра, используемое по умолчанию, если пользователь не задал иное значение. |
| allowedValues |Нет  |Массив допустимых значений параметра, по которому сверяются правильные значения. |
| minValue |Нет  |Минимальное значение для параметров типа int. Это включающее значение. |
| maxValue |Нет  |Максимальное значение для параметров типа int. Это включающее значение. |
| minLength |Нет  |Минимальная длина параметров типа string, secureString и array. Это включающее значение. |
| maxLength |Нет  |Максимальная длина параметров типа string, secureString и array. Это включающее значение. |
| description |Нет  |Описание параметра, отображаемого для пользователей на портале. |

## <a name="template-functions-with-parameters"></a>Шаблон функции с параметрами

При предоставлении значение по умолчанию для параметра, можно использовать большинство функций шаблонов. Для создания значения по умолчанию, можно использовать другое значение параметра. Следующий шаблон демонстрирует использование функций в значение по умолчанию:

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

Нельзя использовать `reference` функции в разделе "Параметры". Параметры вычисляются перед развертыванием поэтому `reference` функции не удается получить состояние выполнения ресурса. 

## <a name="objects-as-parameters"></a>Объекты в качестве параметров

Может быть проще для организации связанных значений, передав их в виде объекта. Кроме того, этот подход уменьшает число параметров в шаблоне.

Определение параметра в шаблон и укажите объект JSON вместо одного значения во время развертывания. 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Затем можно сослаться вложенные свойства параметра с помощью оператора точки.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location":"[resourceGroup().location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="recommendations"></a>Рекомендации
Ниже приведены некоторые рекомендации по работе с параметрами.

* Используйте как можно меньше параметров. По возможности используйте переменную или литеральное значение. Используйте параметры только для следующих сценариев:
   
   * параметры, значения которых должны отличаться в зависимости от среды (SKU, размер или емкость);
   * имена ресурсов, которые вы хотите задать, чтобы упростить идентификацию;
   * значения, которые часто используются для выполнения других задач (например, имя пользователя администратора);
   * секреты (например, пароли);
   * числа или массивы значений, используемые при создании нескольких экземпляров типа ресурса.
* Имена для параметров необходимо указывать в нижнем регистре.
* Укажите описание для каждого параметра в метаданных.

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Определите значения по умолчанию для параметров (за исключением паролей и ключей SSH). Предоставив значение по умолчанию, параметр становится необязательным во время развертывания. Значение по умолчанию может быть пустой строкой. 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Используйте **SecureString** для всех паролей и секретные данные. При передаче конфиденциальных данных в объекте JSON используйте тип **secureObject**. Параметры шаблона с типами secureString и secureObject невозможно прочитать после развертывания ресурса. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* По возможности не используйте параметр, чтобы указать расположение. Вместо этого используйте свойство **location** группы ресурсов. Чтобы развернуть ресурсы в шаблоне в том же расположении, что и группа ресурсов, используйте выражение **resourceGroup().location** для всех ресурсов.
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         ...
     }
   ]
   ```
   
   Если тип ресурса поддерживается в ограниченном наборе расположений, то допустимое расположение необходимо указать непосредственно в шаблоне. Когда вам необходимо использовать параметр **location**, то указывайте его значение для ресурсов, которые могут находиться в одном расположении, насколько это возможно. Такой подход сводит к минимуму количество раз, когда пользователям будет предложено предоставить сведения о местоположении.
* Избегайте использовать параметр или переменную для версии API для типа ресурса. Свойства и значения ресурса могут отличаться для разных версий. Функции IntelliSense в редакторах кода не могут определить правильную схему, если версия API указана в качестве значения параметра или переменной. Вместо этого жестко задайте версию API в шаблоне.
* Не рекомендуется указывать имя параметра в шаблон, соответствующий параметр в команде развертывания. Диспетчер ресурсов преобразует этот конфликт имен путем добавления постфиксных **FromTemplate** параметру шаблона. Предположим, вы добавили в шаблон параметр **ResourceGroupName**, и он конфликтует с параметром **ResourceGroupName** в командлете [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment). При развертывании вам будет предложено указать значение для параметра **ResourceGroupNameFromTemplate**.

## <a name="example-templates"></a>Образцы шаблонов

Эти шаблоны примере демонстрируются некоторые сценарии использования параметров. Разверните их для тестирования, как обрабатываются параметры в различных сценариях.

|Шаблон  |ОПИСАНИЕ  |
|---------|---------|
|[параметры функции для значения по умолчанию](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Демонстрирует использование функции шаблона, при определении значений по умолчанию для параметров. Шаблон не осуществляет развертывание какие-либо ресурсы. Он создает значения параметров и возвращает эти значения. |
|[объект параметра](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Демонстрируется использование объекта для параметра. Шаблон не осуществляет развертывание какие-либо ресурсы. Он создает значения параметров и возвращает эти значения. |

## <a name="next-steps"></a>Дальнейшие действия

* Полные шаблоны для различных типов решений доступны на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).
* Сведения о том, как вводить значения параметров во время развертывания, см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](resource-group-template-deploy.md). 
* Дополнительные сведения о функциях, которые можно использовать в шаблонах, см. в статье [Функции шаблонов Azure Resource Manager](resource-group-template-functions.md).
* Сведения об использовании параметров см. в разделе [использовать объект в качестве параметра в шаблоне Azure Resource Manager](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).