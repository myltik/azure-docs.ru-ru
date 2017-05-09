---
title: "Развертывание ресурсов в Azure | Документация Майкрософт"
description: "Использование Azure PowerShell или Azure CLI для развертывания ресурсов в Azure. Эти ресурсы определяются в шаблоне Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 3b3162c335be43c9bf2a3d1c14b86cdd9b5d46b9
ms.contentlocale: ru-ru
ms.lasthandoff: 04/27/2017


---
# <a name="deploy-resources-to-azure"></a>Развертывание ресурсов в Azure

В этом разделе показано, как развернуть ресурсы в подписке Azure. Можно использовать Azure PowerShell или Azure CLI, чтобы развернуть шаблон Resource Manager, определяющий инфраструктуру для решения.

Введение в основные понятия Azure Resource Manager см. в статье [Общие сведения о диспетчере ресурсов Azure](resource-group-overview.md).

## <a name="steps-for-deployment"></a>Этапы развертывания

В этом разделе предполагается, что вы развертываете [пример шаблона хранилища](#example-storage-template). Можно использовать другой шаблон, но передаваемые для него параметры будут отличаться от показанных в этом разделе.

Ниже приведены общие действия по развертыванию созданного шаблона.

1. Вход в учетную запись
2. Выберите используемую подписку (только если у вас их несколько и требуется выбрать подписку, не используемую по умолчанию).
3. Создание группы ресурсов
4. Развертывание шаблона
5. Проверка состояния развертывания

В разделах ниже показано, как выполнить эти действия с помощью [PowerShell](#powershell) или [Azure CLI](#azure-cli).

## <a name="powershell"></a>PowerShell

1. Чтобы установить Azure PowerShell, ознакомьтесь с разделом [Get started with Azure PowerShell cmdlets](/powershell/azure/overview) (Приступая к работе с командлетами Azure PowerShell).

2. Чтобы быстро приступить к развертыванию, используйте следующие командлеты.

  ```powershell
  Login-AzureRmAccount
  Set-AzureRmContext -SubscriptionID {subscription-id}

  New-AzureRmResourceGroup -Name ExampleGroup -Location "Central US"
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json 
  ```

  Командлет `Set-AzureRmContext` требуется только в том случае, если вы хотите использовать подписку, отличную от вашей подписки по умолчанию. Чтобы просмотреть все подписки и их идентификаторы, используйте следующий командлет.

  ```powershell
  Get-AzureRmSubscription
  ```

3. Развертывание может занять несколько минут. По завершении появится сообщение следующего вида.

  ```powershell
  DeploymentName          : ExampleDeployment
  CorrelationId           : 07b3b233-8367-4a0b-b9bc-7aa189065665
  ResourceGroupName       : ExampleGroup
  ProvisioningState       : Succeeded
  ...
  ```

4. Чтобы убедиться, что группа ресурсов и учетная запись хранения были развернуты в вашей подписке, введите следующую команду.

  ```powershell
  Get-AzureRmResourceGroup -Name ExampleGroup
  Find-AzureRmResource -ResourceGroupNameEquals ExampleGroup
  ```

5. Параметры шаблона можно указать в качестве параметров PowerShell при развертывании шаблона. Предыдущий пример не содержал каких-либо параметров шаблона, поэтому в нем использовались значения по умолчанию, заданные в шаблоне. Чтобы развернуть еще одну учетную запись хранения и указать значения параметров для префикса имени хранилища и номера SKU учетной записи хранения, используйте приведенный ниже командлет.

  ```powershell
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment2 -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json -storageNamePrefix "contoso" -storageSKU "Standard_GRS"
  ```

  Теперь в группе ресурсов две учетные записи хранения. 

## <a name="azure-cli"></a>Инфраструктура CLI Azure

1. Чтобы установить Azure CLI, ознакомьтесь с разделом [Install Azure CLI 2.0](/cli/azure/install-az-cli2) (Установка Azure CLI 2.0).

2. Чтобы быстро приступить к выполнению развертывания, воспользуйтесь следующими командами:

  ```azurecli
  az login
  az account set --subscription {subscription-id}

  az group create --name ExampleGroup --location "Central US"
  az group deployment create --name ExampleDeployment --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json
  ```

  Выполнять команду `az account set` требуется только в том случае, если вы хотите использовать подписку, отличную от вашей подписки по умолчанию. Чтобы просмотреть все подписки и их идентификаторы, используйте следующий командлет.

  ```azurecli
  az account list
  ```

3. Развертывание может занять несколько минут. По завершении появится сообщение следующего вида.

  ```azurecli
  "provisioningState": "Succeeded",
  ```

4. Чтобы убедиться, что группа ресурсов и учетная запись хранения были развернуты в вашей подписке, введите следующую команду.

  ```azurecli
  az group show --name ExampleGroup
  az resource list --resource-group ExampleGroup
  ```

5. Параметры шаблона можно указать в качестве параметров PowerShell при развертывании шаблона. Предыдущий пример не содержал каких-либо параметров шаблона, поэтому в нем использовались значения по умолчанию, заданные в шаблоне. Чтобы развернуть еще одну учетную запись хранения и указать значения параметров для префикса имени хранилища и номера SKU учетной записи хранения, используйте приведенный ниже командлет.

  ```azurecli
  az group deployment create --name ExampleDeployment2 --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json --parameters '{"storageNamePrefix":{"value":"contoso"},"storageSKU":{"value":"Standard_GRS"}}'
  ```

  Теперь в группе ресурсов две учетные записи хранения. 

## <a name="example-storage-template"></a>Пример шаблона хранилища

Используйте приведенный пример шаблона для развертывания учетной записи хранение в своей подписке.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    },
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
  },
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о развертывании шаблонов с помощью PowerShell см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](/azure/azure-resource-manager/resource-group-template-deploy).
* Дополнительные сведения о развертывании шаблонов с помощью Azure CLI см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](/azure/azure-resource-manager/resource-group-template-deploy-cli).




