---
title: Экспорт шаблона Resource Manager с помощью Azure CLI | Документация Майкрософт
description: Используйте Azure Resource Manager и Azure CLI для экспорта шаблона из группы ресурсов.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/23/2018
ms.author: tomfitz
ms.openlocfilehash: 1d73142931a5cfa84cb24df7a85c799a0f508385
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358835"
---
# <a name="export-azure-resource-manager-templates-with-azure-cli"></a>Экспорт шаблонов Azure Resource Manager с помощью Azure CLI

Resource Manager позволяет экспортировать шаблон из существующих ресурсов в подписке. Используя созданный шаблон, можно изучить синтаксис шаблонов или при необходимости автоматизировать повторное развертывание решения.

Важно отметить, что экспортировать шаблон можно двумя разными способами.

* Вы можете экспортировать **фактический шаблон, который использовался для развертывания**. Он содержит все параметры и переменные, указанные в исходном шаблоне. Этот подход полезен в тех случаях, когда требуется извлечь шаблон.
* Вы можете экспортировать **созданный шаблон, который представляет текущее состояние группы ресурсов**. Он не основан на каком-либо шаблоне, который использовался для развертывания. Вместо этого создается шаблон, который является "моментальным снимком" или "резервной копией" группы ресурсов. Экспортированный шаблон содержит много жестко заданных значений и, скорее всего, меньше параметров, чем вы обычно определяете. Этот вариант подходит для повторного развертывания ресурсов в той же группе ресурсов. Чтобы использовать этот шаблон для другой группы ресурсов, возможно, его потребуется значительно изменить.

В этой статье показаны оба подхода.

## <a name="deploy-a-solution"></a>Развертывание решения

Чтобы продемонстрировать оба подхода для экспорта шаблона, начнем с развертывания решения в подписку. Если у вас уже есть группа ресурсов в подписке, в которую необходимо выполнить экспорт, то не нужно развертывать это решение. В остальной части этой статьи речь идет о шаблоне для этого решения. В примере сценария развертывается учетная запись хранения.

```azurecli
az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name NewStorage \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
```  

## <a name="save-template-from-deployment-history"></a>Сохранение шаблона из журнала развертываний

Шаблон можно извлечь из журнала развертываний с помощью команды [az group deployment export](/cli/azure/group/deployment#az_group_deployment_export). В следующем примере сохраняется шаблон, который вы ранее развернули:

```azurecli
az group deployment export --name NewStorage --resource-group ExampleGroup
```

Эта команда возвращает шаблон. Скопируйте код JSON и сохраните его как файл. Обратите внимание, что это тот же шаблон, который использовался для развертывания. Параметры и переменные соответствуют шаблону из GitHub. Этот шаблон можно развернуть повторно.


## <a name="export-resource-group-as-template"></a>Экспорт группы ресурсов в виде шаблона

Вместо извлечения шаблона из журнала развертываний можно получить шаблон, который представляет текущее состояние группы ресурсов. Для этого используйте команду [az group export](/cli/azure/group#az_group_export). Эта команда используется, когда в группе ресурсов внесено много изменений и ни один из существующих шаблонов не содержит всех изменений. Он будет моментальным снимком группы ресурсов, который можно использовать для повторного развертывания в той же группе ресурсов. Чтобы использовать экспортированный шаблон для других решений, его необходимо значительно изменить.

```azurecli
az group export --name ExampleGroup
```

Эта команда возвращает шаблон. Скопируйте код JSON и сохраните его как файл. Обратите внимание, что он отличается от шаблона в GitHub. Этот шаблон содержит другие параметры, но у него нет переменных. Номер SKU хранилища и расположение жестко заданы в значениях. В следующем примере показан экспортированный шаблон, но в вашем шаблоне имя параметра будет немного отличаться:

```json
{
  "parameters": {
    "storageAccounts_mcyzaljiv7qncstandardsa_name": {
      "type": "String",
      "defaultValue": null
    }
  },
  "variables": {},
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "resources": [
    {
      "tags": {},
      "dependsOn": [],
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "Storage",
      "type": "Microsoft.Storage/storageAccounts",
      "location": "centralus",
      "name": "[parameters('storageAccounts_mcyzaljiv7qncstandardsa_name')]",
      "properties": {}
    }
  ],
  "contentVersion": "1.0.0.0"
}
```

Вы можете повторно развернуть этот шаблон, но в нем требуется подобрать уникальное имя для учетной записи хранения. Имя вашего параметра будет немного отличаться.

```azurecli
az group deployment create --name NewStorage --resource-group ExampleGroup \
  --template-file examplegroup.json \
  --parameters "{\"storageAccounts_mcyzaljiv7qncstandardsa_name\":{\"value\":\"tfstore0501\"}}"
```

## <a name="customize-exported-template"></a>Настройка экспортированного шаблона

Этот шаблон можно изменить, чтобы сделать его более гибким и простым в использовании. Чтобы можно было использовать больше расположений, настройте для свойства location такое же значение расположения, как у группы ресурсов:

```json
"location": "[resourceGroup().location]",
```

Чтобы не было необходимости подбирать уникальное имя учетной записи хранения, удалите параметр имени учетной записи хранения. Добавьте параметр для суффикса имени хранилища и номер SKU хранилища:

```json
"parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
},
```

Добавьте переменную, которая создает имя учетной записи хранения с помощью функции uniqueString:

```json
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
```

Задайте переменной имя учетной записи хранения:

```json
"name": "[variables('storageAccountName')]",
```

Задайте параметру номер SKU:

```json
"sku": {
    "name": "[parameters('storageAccountType')]",
    "tier": "Standard"
},
```

Ваш шаблон теперь выглядит так:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), parameters('storageSuffix'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "[parameters('storageAccountType')]",
        "tier": "Standard"
      },
      "kind": "Storage",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {},
      "dependsOn": []
    }
  ]
}
```

Повторно разверните измененный шаблон.

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения об экспорте шаблона с помощью портала см. в статье [Экспорт шаблона Azure Resource Manager из существующих ресурсов](resource-manager-export-template.md).
* Сведения об определении параметров в шаблоне см. в разделе [Создание шаблонов](resource-group-authoring-templates.md#parameters).
* Советы по устранению распространенных ошибок развертывания см. в разделе [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md).