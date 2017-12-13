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
ms.date: 12/01/2017
ms.author: tomfitz
ms.openlocfilehash: 763f46b9b5be7edf06ee0604bfc51a2482405b60
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Развертывание ресурсов Azure в нескольких подписках или группах ресурсов

Обычно развертывание всех ресурсов в шаблоне выполняется в отдельную группу ресурсов. Тем не менее возможны ситуации, когда необходимо развернуть набор ресурсов одновременно, но при этом разместить их в отдельных подписках или группах ресурсов. Например, вы захотите развернуть резервную копию виртуальной машины для Azure Site Recovery в отдельную группу ресурсов или расположение. Resource Manager позволяет использовать вложенные шаблоны, с помощью которых ресурсы можно развертывать в разных подписках и группах ресурсов, а не только в подписках и группах ресурсов родительского шаблона.

Группа ресурсов — это контейнер жизненного цикла приложения, в котором содержится коллекция его ресурсов. Создание группы ресурсов выполняется вне шаблона. Затем вы указываете группу ресурсов, которая должна использоваться в процессе развертывания. Дополнительные сведения о группах ресурсов см. в статье [Общие сведения об Azure Resource Manager](resource-group-overview.md).

## <a name="specify-a-subscription-and-resource-group"></a>Выбор подписки и группы ресурсов

Чтобы подключиться к другому ресурсу, при развертывании используйте вложенный или связанный шаблон. В ресурсах типа `Microsoft.Resources/deployments` содержатся параметры `subscriptionId` и `resourceGroup`. Эти свойства позволяют указывать различные подписки и группы ресурсов для вложенного развертывания. Все группы ресурсов необходимо создать до выполнения развертывания. Если не указать идентификатор подписки или группу ресурсов, будут использованы подписка и группа ресурсов из родительского шаблона.

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

## <a name="deploy-the-template"></a>Развертывание шаблона

Чтобы развернуть пример шаблона, используйте выпуски Azure PowerShell или Azure CLI начиная с мая 2017 года. Для этих примеров воспользуйтесь [шаблоном для нескольких подписок](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) из GitHub.

### <a name="two-resource-groups-in-the-same-subscription"></a>Две группы ресурсов в одной подписке

Чтобы развернуть две учетные записи хранения в двух группах ресурсов в одной подписке с помощью PowerShell, используйте следующий код:

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

Чтобы развернуть две учетные записи хранения в двух группах ресурсов в одной подписке с помощью Azure CLI, используйте следующий код:

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

После завершения развертывания вы увидите две группы ресурсов, в каждой из которых содержится учетная запись хранения.

### <a name="two-resource-groups-in-different-subscriptions"></a>Две группы ресурсов в разных подписках

Чтобы развернуть две учетные записи хранения в двух подписках с помощью PowerShell, используйте следующий код:

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

Чтобы развернуть две учетные записи хранения в двух подписках с помощью Azure CLI, используйте следующий код:

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

Чтобы проверить варианты разрешения функции `resourceGroup()`, разверните [пример шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json), который возвращает объект группы ресурсов для родительского шаблона, встроенного шаблона и связанного шаблона. И родительский, и встроенный шаблоны разрешаются в одной и той же группе ресурсов. Связанный шаблон разрешается в связанной группе ресурсов.

Для PowerShell используйте команду:

```powershell
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Для интерфейса командной строки Azure:

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
