---
title: Раздел параметров в шаблонах Azure Resource Manager | Документация Майкрософт
description: Здесь описан раздел параметров в шаблонах Azure Resource Manager, использующий декларативный синтаксис JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2018
ms.author: tomfitz
ms.openlocfilehash: 193e74d94017cf0ca8ec0600c7e5a3dc4b7a6dea
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359209"
---
# <a name="parameters-section-of-azure-resource-manager-templates"></a>Раздел параметров в шаблонах Azure Resource Manager
В разделе параметров шаблона указываются значения, которые вы можете вводить во время развертывания ресурсов. Значения этих параметров позволяют настраивать развертывание путем предоставления значений, предназначенных для конкретной среды (например, для среды разработки, тестирования и рабочей среды). Предоставление параметров в шаблоне не является обязательным требованием, однако без параметров шаблон всегда будет развертывать одни и те же ресурсы с одинаковыми именами, расположениями и свойствами.

Существует ограничение в 255 параметров на один шаблон. Вы можете сократить число параметров, создавая объекты с несколькими свойствами. Этот метод описан далее в этой статье.

## <a name="define-and-use-a-parameter"></a>Определение и применение параметра

Следующий пример демонстрирует определение простого параметра. Здесь указывается имя параметра и тип его значения (строковое значение). Параметр принимает только те значения, которые имеют смысл для его назначения. Также указано значение по умолчанию, которое используется, если во время развертывания значение параметра не указано. И наконец, в определении параметра есть описание его назначения. 

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

Ссылка на значение параметра указывается в шаблоне следующим образом:

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

В предыдущем примере представлена только часть свойств, которые можно использовать в разделе параметров. Вот полный список доступных свойств:

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

| Имя элемента | Обязательно | ОПИСАНИЕ |
|:--- |:--- |:--- |
| имя_параметра |Yes |Имя параметра. Должно быть допустимым идентификатором JavaScript. |
| Тип |Yes |Тип значения параметра. Допустимые типы и значения: **string**, **securestring**, **int**, **bool**, **object**, **secureObject** и **array**. |
| defaultValue |Нет  |Значение параметра, используемое по умолчанию, если пользователь не задал иное значение. |
| allowedValues |Нет  |Массив допустимых значений параметра, по которому сверяются правильные значения. |
| minValue |Нет  |Минимальное значение для параметров типа int. Это включающее значение. |
| maxValue |Нет  |Максимальное значение для параметров типа int. Это включающее значение. |
| minLength |Нет  |Минимальная длина параметров типа string, securestring и array. Это включающее значение. |
| maxLength |Нет  |Максимальная длина параметров типа string, securestring и array. Это включающее значение. |
| description |Нет  |Описание параметра, отображаемого для пользователей на портале. |

## <a name="template-functions-with-parameters"></a>Использование функций шаблонов с параметрами

Чтобы определить значение по умолчанию для параметра, вы можете использовать большинство функций шаблонов. Также для создания значения по умолчанию можно использовать значение другого параметра. Следующий шаблон демонстрирует применение функций в значениях по умолчанию:

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

В разделе параметров недоступна функция `reference`. Дело в том, что параметры вычисляются до развертывания, поэтому функция `reference` не может получить состояние выполнения ресурса. 

## <a name="objects-as-parameters"></a>Объекты в качестве параметров

Чтобы структурировать связанные друг с другом значения, их можно передать в виде объекта. Что не менее важно, этот подход сокращает число параметров в шаблоне.

Задайте в шаблоне параметр обычным образом, а во время развертывания передайте для этого параметра не одно значение, а объект в формате JSON. 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
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

Тогда вы сможете ссылаться в шаблоне на вложенные свойства этого параметра с помощью оператора-точки.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
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

* Определите значения по умолчанию для параметров (за исключением паролей и ключей SSH). Если вы предоставите значение по умолчанию, параметр не обязательно указывать во время развертывания. Значение по умолчанию может быть пустой строкой. 
   
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

* Используйте **securestring** для всех паролей и секретов. При передаче конфиденциальных данных в объекте JSON используйте тип **secureObject**. Параметры шаблона с типами securestring и secureObject невозможно считать после развертывания ресурса. 
   
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

* Вы можете определить расположение в параметре и активно применять значение этого параметра во всех ресурсах, которые могут находиться в одном расположении. Такой подход позволит реже обращаться к пользователю за информацией о расположении. Если некоторый тип ресурса поддерживается не во всех расположениях, вы можете указать список допустимых расположений прямо в шаблоне или дополнительном параметре расположения. Если в организации есть ограничения на доступ пользователей из разных регионов, выражение **resourceGroup().location** запретит пользователю развертывать шаблон. Предположим, что один пользователь создает группу ресурсов в определенном регионе. Другому пользователю нужно развернуть ресурс в этой группе, но он не имеет доступа к этому региону. 
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[parameters('location')]",
         ...
     }
   ]
   ```
    
* Избегайте использовать параметр или переменную для версии API для типа ресурса. Свойства и значения ресурса могут отличаться для разных версий. Функции IntelliSense в редакторах кода не могут определить правильную схему, если версия API указана в качестве значения параметра или переменной. Вместо этого жестко задайте версию API в шаблоне.
* Старайтесь не использовать в шаблоне имена параметров, совпадающие с параметрами команды развертывания. Resource Manager разрешает такие конфликты именования, добавляя постфикс **FromTemplate** к параметру шаблона. Предположим, вы добавили в шаблон параметр **ResourceGroupName**, и он конфликтует с параметром **ResourceGroupName** в командлете [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment). При развертывании вам будет предложено указать значение для параметра **ResourceGroupNameFromTemplate**.

## <a name="example-templates"></a>Образцы шаблонов

Эти примеры шаблонов демонстрируют разные сценарии использования параметров. Разверните их и изучите, как обрабатываются параметры в различных сценариях.

|Шаблон  |ОПИСАНИЕ  |
|---------|---------|
|[Использование параметров с функциями для определения значений по умолчанию](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Демонстрирует, как можно применить функции шаблонов при определении значений по умолчанию для параметров. Этот шаблон не развертывает ресурсы. Он только создает значения параметров и возвращает их. |
|[Объект параметров](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Демонстрирует использование объекта в качестве параметра. Этот шаблон не развертывает ресурсы. Он только создает значения параметров и возвращает их. |

## <a name="next-steps"></a>Дополнительная информация

* Полные шаблоны для различных типов решений доступны на странице [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).
* Сведения о том, как вводить значения параметров во время развертывания, см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](resource-group-template-deploy.md). 
* Дополнительные сведения о функциях, которые можно использовать в шаблонах, см. в статье [Функции шаблонов Azure Resource Manager](resource-group-template-functions.md).
* Сведения об использовании объекта в качестве параметра см. в статье [Использование объекта в качестве параметра в шаблоне Azure Resource Manager](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).