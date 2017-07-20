---
title: "Развертывание ресурсов Azure в несколько групп ресурсов | Документация Майкрософт"
description: "Сведения о развертывании ресурсов в несколько групп ресурсов Azure."
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
ms.date: 06/15/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: d8b041213b269775175a810e585103d3c538557f
ms.contentlocale: ru-ru
ms.lasthandoff: 06/17/2017


---

# <a name="deploy-azure-resources-to-more-than-one-resource-group"></a>Развертывание ресурсов Azure в несколько групп ресурсов

Обычно развертывание всех ресурсов в шаблоне выполняется в отдельную группу ресурсов. Но иногда может потребоваться развернуть ресурсы вместе, но разместить их в разные группы ресурсов. Например, вы захотите развернуть резервную копию виртуальной машины для Azure Site Recovery в отдельную группу ресурсов или расположение. Resource Manager позволяет использовать вложенные шаблоны, с помощью которых ресурсы можно развертывать в разные группы ресурсов, а не только в группу ресурсов родительского шаблона.

Группа ресурсов — это контейнер жизненного цикла приложения, в котором содержится коллекция его ресурсов. Создание группы ресурсов выполняется вне шаблона. Затем вы указываете группу ресурсов, которая должна использоваться в процессе развертывания. Дополнительные сведения о группах ресурсов см. в статье [Общие сведения об Azure Resource Manager](resource-group-overview.md).

## <a name="example-template"></a>Пример шаблона

Чтобы подключиться к другому ресурсу, при развертывании используйте вложенный или связанный шаблон. Тип ресурса `Microsoft.Resources/deployments` предоставляет параметр `resourceGroup`, который позволяет указать другую группу ресурсов для вложенного развертывания. Все группы ресурсов необходимо создать до выполнения развертывания. Код в приведенном ниже примере развертывает две учетные записи хранения: одну в группе ресурсов, указанной во время развертывания, и вторую в группе ресурсов `crossResourceGroupDeployment`:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "StorageAccountName1": {
            "type": "string"
        },
        "StorageAccountName2": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "crossResourceGroupDeployment",
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
                            "name": "[parameters('StorageAccountName2')]",
                            "apiVersion": "2015-06-15",
                            "location": "West US",
                            "properties": {
                                "accountType": "Standard_LRS"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('StorageAccountName1')]",
            "apiVersion": "2015-06-15",
            "location": "West US",
            "properties": {
                "accountType": "Standard_LRS"
            }
        }
    ]
}
```

Если в качестве значения параметра `resourceGroup` задать имя несуществующей группы ресурсов, развертывание завершится сбоем. Но если не указать значение параметра `resourceGroup`, Resource Manager будет использовать родительскую группу ресурсов.  

## <a name="deploy-the-template"></a>Развертывание шаблона

Пример шаблона можно развернуть с помощью портала, Azure PowerShell или Azure CLI. Используйте выпуски этих средств начиная с мая 2017 года. Для работы с этими примерами шаблон необходимо сохранить локально как файл **crossrgdeployment.json**.

При использовании PowerShell выполните следующее:

```powershell
Login-AzureRmAccount

New-AzureRmResourceGroup -Name mainResourceGroup -Location "South Central US"
New-AzureRmResourceGroup -Name crossResourceGroupDeployment -Location "Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName mainResourceGroup `
  -TemplateFile c:\MyTemplates\crossrgdeployment.json
```

При использовании Azure CLI выполните следующее:

```azurecli
az login

az group create --name mainResourceGroup --location "South Central US"
az group create --name crossResourceGroupDeployment --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group mainResourceGroup \
    --template-file crossrgdeployment.json
```

После завершения развертывания вы увидите две группы ресурсов, в каждой из которых содержится учетная запись хранения.

## <a name="use-resourcegroup-function"></a>Использование функции resourceGroup()

Для развертывания в нескольких группах ресурсов результат выполнения [функции resouceGroup()](resource-group-template-functions-resource.md#resourcegroup) зависит от способа указания вложенного шаблона. 

При внедрении одного шаблона в другой функция resouceGroup() во вложенном шаблоне разрешается в родительской группе ресурсов. Внедренный шаблон использует следующий формат:

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

При связывании отдельного шаблона функция resouceGroup() связывает разрешения шаблона с вложенной группой ресурсов. Связанный шаблон использует следующий формат:

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

## <a name="next-steps"></a>Дальнейшие действия

* Сведения об определении параметров в шаблоне см. в статье [Описание структуры и синтаксиса шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
* Советы по устранению распространенных ошибок развертывания см. в разделе [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Сведения о развертывании шаблона, которому нужен токен SAS, см. в статье [Развертывание частного шаблона с помощью маркера SAS](resource-manager-powershell-sas-token.md).

