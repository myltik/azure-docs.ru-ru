---
title: "Экспорт шаблона Resource Manager с помощью Azure PowerShell | Документация Майкрософт"
description: "Используйте Azure Resource Manager и Azure PowerShell для экспорта шаблона из группы ресурсов."
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
ms.date: 05/01/2017
ms.author: tomfitz
ms.openlocfilehash: 7543811eb9448222b6e7c266756e68debc7d54be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="export-azure-resource-manager-templates-with-powershell"></a>Экспорт шаблонов Azure Resource Manager с помощью PowerShell

Resource Manager позволяет экспортировать шаблон из существующих ресурсов в подписке. Используя созданный шаблон, можно изучить синтаксис шаблонов или при необходимости автоматизировать повторное развертывание решения.

Важно отметить, что экспортировать шаблон можно двумя разными способами.

* Вы можете экспортировать шаблон, который использовался для развертывания. Он содержит все параметры и переменные, указанные в исходном шаблоне. Этот подход полезен в тех случаях, когда требуется извлечь шаблон.
* Вы можете экспортировать шаблон, который представляет текущее состояние группы ресурсов. Он не основан на каком-либо шаблоне, который использовался для развертывания. Напротив, создается шаблон, который является моментальным снимком группы ресурсов. Экспортированный шаблон содержит много жестко заданных значений и, скорее всего, меньше параметров, чем вы обычно определяете. Этот метод подходит, если вы изменили группу ресурсов. и теперь на ее основе необходимо создать шаблон.

В этой статье показаны оба способа.

## <a name="deploy-a-solution"></a>Развертывание решения

Чтобы продемонстрировать оба подхода для экспорта шаблона, начнем с развертывания решения в подписку. Если у вас уже есть группа ресурсов в подписке, в которую необходимо выполнить экспорт, то не нужно развертывать это решение. Оставшаяся часть этой статьи относится к шаблону для этого решения. В примере сценария развертывается учетная запись хранения.

```powershell
New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup `
  -DeploymentName NewStorage
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```  

## <a name="save-template-from-deployment-history"></a>Сохранение шаблона из журнала развертываний

Шаблон можно извлечь из журнала развертываний с помощью команды [Save-AzureRmResourceGroupDeploymentTemplate](/powershell/module/azurerm.resources/save-azurermresourcegroupdeploymenttemplate). В следующем примере сохраняется шаблон, который вы ранее развернули:

```powershell
Save-AzureRmResourceGroupDeploymentTemplate -ResourceGroupName ExampleGroup -DeploymentName NewStorage
```

Эта команда возвращает расположение шаблона.

```powershell
Path
----
C:\Users\exampleuser\NewStorage.json
```

Откройте файл и обратите внимание, что это точно такой же шаблон, как используемый для развертывания. Параметры и переменные соответствуют шаблону из GitHub. Этот шаблон можно развернуть повторно.

## <a name="export-resource-group-as-template"></a>Экспорт группы ресурсов в виде шаблона

Вместо извлечения шаблона из журнала развертываний можно получить шаблон, который представляет текущее состояние группы ресурсов. Для этого используйте команду [Export-AzureRmResourceGroup](/powershell/module/azurerm.resources/export-azurermresourcegroup). Эта команда используется, когда в группе ресурсов внесено много изменений и ни один из существующих шаблонов не содержит всех изменений.

```powershell
Export-AzureRmResourceGroup -ResourceGroupName ExampleGroup
```

Эта команда возвращает расположение шаблона.

```powershell
Path
----
C:\Users\exampleuser\ExampleGroup.json
```

Откройте файл и обратите внимание, что он отличается от шаблона в GitHub. У него другие параметры и нет переменных. Номер SKU хранилища и расположение жестко заданы в значениях. В следующем примере показан экспортированный шаблон, но в вашем шаблоне имя параметра будет немного отличаться:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccounts_nf3mvst4nqb36standardsa_name": {
      "defaultValue": null,
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "Storage",
      "name": "[parameters('storageAccounts_nf3mvst4nqb36standardsa_name')]",
      "apiVersion": "2016-01-01",
      "location": "southcentralus",
      "tags": {},
      "properties": {},
      "dependsOn": []
    }
  ]
}
```

Вы можете повторно развернуть этот шаблон, но в нем требуется подобрать уникальное имя для учетной записи хранения. Имя вашего параметра будет немного отличаться.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup `
  -TemplateFile C:\Users\exampleuser\ExampleGroup.json `
  -storageAccounts_nf3mvst4nqb36standardsa_name tfnewstorage0501
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

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения об экспорте шаблона с помощью портала см. в статье [Экспорт шаблона Azure Resource Manager из существующих ресурсов](resource-manager-export-template.md).
* Сведения об определении параметров в шаблоне см. в разделе [Создание шаблонов](resource-group-authoring-templates.md#parameters).
* Советы по устранению распространенных ошибок развертывания см. в разделе [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md).
