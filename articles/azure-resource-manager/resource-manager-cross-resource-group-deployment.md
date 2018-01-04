---
title: "Развертывание ресурсов Azure в нескольких подписках и группах ресурсов | Документация Майкрософт"
description: "Сведения о развертывании ресурсов в нескольких подписках и группах ресурсов Azure."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2017
ms.author: tomfitz
ms.openlocfilehash: f7b2a0de82cfd8fd489387876034487beb49cfd4
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/19/2017
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Развертывание ресурсов Azure в нескольких подписках или группах ресурсов

Как правило, развертывание всех ресурсов в шаблоне в один [группы ресурсов](resource-group-overview.md). Тем не менее возможны ситуации, когда необходимо развернуть набор ресурсов одновременно, но при этом разместить их в отдельных подписках или группах ресурсов. Например, вы захотите развернуть резервную копию виртуальной машины для Azure Site Recovery в отдельную группу ресурсов или расположение. Resource Manager позволяет использовать вложенные шаблоны, с помощью которых ресурсы можно развертывать в разных подписках и группах ресурсов, а не только в подписках и группах ресурсов родительского шаблона.

## <a name="specify-a-subscription-and-resource-group"></a>Выбор подписки и группы ресурсов

Чтобы ориентироваться на другой ресурс, используйте вложенные или связанный шаблон. В ресурсах типа `Microsoft.Resources/deployments` содержатся параметры `subscriptionId` и `resourceGroup`. Эти свойства позволяют указывать различные подписки и группы ресурсов для вложенного развертывания. Все группы ресурсов необходимо создать до выполнения развертывания. Если не указать идентификатор подписки или группу ресурсов, будут использованы подписка и группа ресурсов из родительского шаблона.

Чтобы указать другой группе ресурсов и подписки, используйте следующую команду:

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

Если группы ресурсов находятся в той же подписке, можно удалить **subscriptionId** значение.

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

Чтобы развернуть пример шаблона, используйте выпуски Azure PowerShell или Azure CLI начиная с мая 2017 года.

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

Следующие шаблоны демонстрируют несколько развертывания группы ресурсов. Сценарии для развертывания шаблонов отображаются после таблицы.

|Шаблон  |ОПИСАНИЕ  |
|---------|---------|
|[Кросс-шаблон подписки](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Развертывает одну учетную запись хранилища в одну группу ресурсов и одну учетную запись хранения в другую группу ресурсов. Включите значение для идентификатора подписки, если вторая группа ресурсов находится в другой подписке. |
|[Кросс-шаблон свойства группы ресурсов](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |Показано, как `resourceGroup()` функции разрешается. Он не осуществляет развертывание какие-либо ресурсы. |

### <a name="powershell"></a>PowerShell

Для PowerShell для развертывания двух учетных записей хранения на две группы ресурсов в **одной подписке**, используйте:

```powershell
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

Для PowerShell для развертывания двух учетных записей хранилища для **две подписки**, используйте:

```powershell
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

Для PowerShell, чтобы проверить как **объекте группы ресурсов** разрешает для родительского шаблона, встроенного шаблона и связанного шаблона для использования:

```powershell
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

### <a name="azure-cli"></a>Инфраструктура CLI Azure

Для Azure CLI для развертывания двух учетных записей хранения на две группы ресурсов в **одной подписке**, используйте:

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

Для Azure CLI для развертывания двух учетных записей хранилища для **две подписки**, используйте:

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

Для Azure CLI для тестирования как **объекте группы ресурсов** разрешает для родительского шаблона, встроенного шаблона и связанного шаблона для использования:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

## <a name="next-steps"></a>Дальнейшие действия

* Сведения об определении параметров в шаблоне см. в статье [Описание структуры и синтаксиса шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Советы по устранению распространенных ошибок развертывания см. в разделе [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Сведения о развертывании шаблона, которому нужен токен SAS, см. в статье [Развертывание частного шаблона с помощью маркера SAS](resource-manager-powershell-sas-token.md).
