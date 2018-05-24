---
title: Развертывание ресурсов Azure в нескольких подписках и группах ресурсов | Документация Майкрософт
description: Сведения о развертывании ресурсов в нескольких подписках и группах ресурсов Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2018
ms.author: tomfitz
ms.openlocfilehash: c7e9807e7195be47bf7874837ff9428c90abbcee
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Развертывание ресурсов Azure в нескольких подписках или группах ресурсов

Как правило, все ресурсы в шаблоне развертываются в отдельную [группу ресурсов](resource-group-overview.md). Тем не менее возможны ситуации, когда необходимо развернуть набор ресурсов одновременно, но при этом разместить их в отдельных подписках или группах ресурсов. Например, вы захотите развернуть резервную копию виртуальной машины для Azure Site Recovery в отдельную группу ресурсов или расположение. Resource Manager позволяет использовать вложенные шаблоны, с помощью которых ресурсы можно развертывать в разных подписках и группах ресурсов, а не только в подписках и группах ресурсов родительского шаблона.

> [!NOTE]
> Развертывание можно выполнять только в пять групп ресурсов в рамках одного развертывания. Как правило, это ограничение означает, что можно выполнить развертывание в одной группе ресурсов, указанной для родительского шаблона, и максимум в четырех группах ресурсов во вложенных или связанных развертываниях. Но если родительский шаблон содержит только вложенные или связанные шаблоны и сам по себе не развертывает ресурсы, во вложенные или связанные развертывания можно включить до пяти групп ресурсов.

## <a name="specify-a-subscription-and-resource-group"></a>Выбор подписки и группы ресурсов

Чтобы подключиться к другому ресурсу, используйте вложенный или связанный шаблон. В ресурсах типа `Microsoft.Resources/deployments` содержатся параметры `subscriptionId` и `resourceGroup`. Эти свойства позволяют указывать различные подписки и группы ресурсов для вложенного развертывания. Все группы ресурсов необходимо создать до выполнения развертывания. Если не указать идентификатор подписки или группу ресурсов, будут использованы подписка и группа ресурсов из родительского шаблона.

Учетная запись, которая используется для развертывания шаблона, должна иметь разрешения на развертывание с указанным идентификатором подписки. Если указанная подписка существует в другом клиенте Azure Active Directory, необходимо [добавить гостевых пользователей из другого каталога](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Чтобы указать другую группу ресурсов и подписку, используйте следующий код:

```json
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "resourceGroup": "[parameters('secondResourceGroup')]",
        "subscriptionId": "[parameters('secondSubscriptionID')]",
        ...
    }
]
```

Если группы ресурсов находятся в одной подписке, значение **subscriptionId** можно удалить.

В приведенном ниже примере развертываются две учетные записи хранения: одна в группе ресурсов, указанной во время развертывания, другая — в группе ресурсов, указанной в параметре `secondResourceGroup`:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "secondResourceGroup": {
            "type": "string"
        },
        "secondSubscriptionID": {
            "type": "string",
            "defaultValue": ""
        },
        "secondStorageLocation": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "firstStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
        "secondStorageName": "[concat(parameters('storagePrefix'), uniqueString(parameters('secondSubscriptionID'), parameters('secondResourceGroup')))]"
    },
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "[parameters('secondResourceGroup')]",
            "subscriptionId": "[parameters('secondSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "name": "[variables('secondStorageName')]",
                            "apiVersion": "2017-06-01",
                            "location": "[parameters('secondStorageLocation')]",
                            "sku":{
                                "name": "Standard_LRS"
                            },
                            "kind": "Storage",
                            "properties": {
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('firstStorageName')]",
            "apiVersion": "2017-06-01",
            "location": "[resourceGroup().location]",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        }
    ]
}
```

Если в качестве значения параметра `resourceGroup` задать имя несуществующей группы ресурсов, развертывание завершится сбоем.

Чтобы развернуть пример шаблона, используйте Azure PowerShell 4.0.0 или более поздней версии либо Azure CLI 2.0.0 или более поздней версии.

## <a name="use-the-resourcegroup-function"></a>Использование функции resourceGroup()

При развертывании в нескольких группах ресурсов результат выполнения [функции resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) зависит от способа, с помощью которого указан вложенный шаблон. 

При внедрении одного шаблона в другой функция resourceGroup() во вложенном шаблоне разрешается в родительской группе ресурсов. Внедренный шаблон использует следующий формат:

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() refers to parent resource group
    }
}
```

При связывании с отдельным шаблоном функция resourceGroup() в связанном шаблоне разрешается во вложенной группе ресурсов. Связанный шаблон использует следующий формат:

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() in linked template refers to linked resource group
    }
}
```

## <a name="example-templates"></a>Образцы шаблонов

В следующих шаблонах демонстрируется развертывание в нескольких группах ресурсов. Скрипты для развертывания шаблонов приведены после таблицы.

|Шаблон  |ОПИСАНИЕ  |
|---------|---------|
|[Шаблон для развертывания в нескольких подписках](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Развертывание одной учетной записи хранения в одной группе ресурсов и еще одной учетной записи хранения в другой группе ресурсов. Укажите значение идентификатора подписки, если вторая группа ресурсов находится в другой подписке. |
|[Шаблон свойств для развертывания в нескольких группах ресурсов](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |В этом шаблоне показано, как разрешается функция `resourceGroup()`. Он не используется для развертывания ресурсов. |

### <a name="powershell"></a>PowerShell

Чтобы развернуть две учетные записи хранения в двух группах ресурсов в **одной подписке** с помощью PowerShell, используйте следующий код:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzureRmResourceGroup -Name $firstRG -Location southcentralus
New-AzureRmResourceGroup -Name $secondRG -Location eastus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Чтобы развернуть две учетные записи хранения в **двух подписках** с помощью PowerShell, используйте следующий код:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzureRmSubscription -Subscription $secondSub
New-AzureRmResourceGroup -Name $secondRG -Location eastus

Select-AzureRmSubscription -Subscription $firstSub
New-AzureRmResourceGroup -Name $firstRG -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

Чтобы проверить, как разрешается **объект группы ресурсов** для родительского, встроенного и связанного шаблонов с помощью PowerShell, используйте следующий код:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

В приведенном выше примере **parentRG** и **inlineRG** разрешаются в **parentGroup**. **linkedRG** разрешается в **linkedGroup**. Выходные данные из предыдущего примера:

```powershell
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 inlineRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 linkedRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
                                               "name": "linkedGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
```

### <a name="azure-cli"></a>Инфраструктура CLI Azure

Чтобы развернуть две учетные записи хранения в двух группах ресурсов в **одной подписке** с помощью Azure CLI, используйте следующий код:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Чтобы развернуть две учетные записи хранения в **двух подписках** с помощью Azure CLI, используйте следующий код:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

Чтобы проверить, как разрешается **объект группы ресурсов** для родительского, встроенного и связанного шаблонов с помощью Azure CLI, используйте следующий код:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

В приведенном выше примере **parentRG** и **inlineRG** разрешаются в **parentGroup**. **linkedRG** разрешается в **linkedGroup**. Выходные данные из предыдущего примера:

```azurecli
...
"outputs": {
  "inlineRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "linkedRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
      "location": "southcentralus",
      "name": "linkedGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "parentRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  }
},
...
```

## <a name="next-steps"></a>Дополнительная информация

* Сведения об определении параметров в шаблоне см. в статье [Описание структуры и синтаксиса шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Советы по устранению распространенных ошибок развертывания см. в разделе [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Сведения о развертывании шаблона, которому нужен токен SAS, см. в статье [Развертывание частного шаблона с помощью маркера SAS](resource-manager-powershell-sas-token.md).
