---
title: "Пример политики Azure в формате JSON: принудительное применение тега и его значения для групп ресурсов | Документация Майкрософт"
description: "Этот пример политики в формате JSON требует указать тег и его значение для группы ресурсов."
services: azure-policy
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 
ms.service: azure-policy
ms.devlang: 
ms.topic: sample
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 10/30/2017
ms.author: banders
ms.custom: mvc
ms.openlocfilehash: 23967acc86f3640e2af46f4ab5168c0f607ef518
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2017
---
# <a name="enforce-tag-and-its-value-on-resource-groups"></a>Принудительное применение тега и его значения для группы ресурсов

Эта политика требует указать тег и его значение для группы ресурсов. Вы можете указать имя и значение требуемого тега.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Пример шаблона

[!code-json[main](../../../policy-templates/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.json "Enforce tag and its value on resource groups")]


Можно развернуть шаблон с помощью [портала Azure](#deploy-with-the-portal), [PowerShell](#deploy-with-powershell) или [Azure CLI](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Развертывание с помощью портала

[![Развертывание в Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade)

## <a name="deploy-with-powershell"></a>Развертывание с помощью PowerShell

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

````powershell
$definition = New-AzureRmPolicyDefinition -Name "enforce-resourceGroup-tags" -DisplayName "Enforce tag and its value on resource groups" -description "Enforces a required tag and its value on resource groups." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope> -PolicyDefinition $definition
$assignment
````

### <a name="clean-up-powershell-deployment"></a>Очистка развертывания, выполненного с помощью PowerShell

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```


## <a name="deploy-with-azure-cli"></a>Развертывание с помощью интерфейса командной строки Azure

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]


````cli

az policy definition create --name 'enforce-resourceGroup-tags' --display-name 'Enforce tag and its value on resource groups' --description 'Enforces a required tag and its value on resource groups.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/ResourceGroup/enforce-resourceGroup-tags/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "enforce-resourceGroup-tags"

````

### <a name="clean-up-azure-cli-deployment"></a>Очистка развертывания, выполненного с помощью Azure CLI

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные примеры шаблонов политик Azure [доступны здесь](../json-samples.md).
