---
title: Ограничение установки расширений для виртуальной машины с помощью службы "Политика Azure" | Документация Майкрософт
description: Служба "Политика Azure" позволяет ограничить развертывание расширений.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: danis;cynthn
ms.openlocfilehash: da5b0db997ba1aa0e998f6fe2645e955b490951d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "33944786"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Ограничение установки расширений на виртуальных машинах Windows с помощью службы "Политика Azure"

Если нужно заблокировать использование или установку определенных расширений на виртуальные машины Windows, можно создать через PowerShell политику Azure, которая ограничивает расширения для виртуальных машин в определенной группе ресурсов. 

В этом руководстве используется интерфейс Azure PowerShell в Cloud Shell, который постоянно обновляется до последней версии. Чтобы установить и использовать PowerShell локально, для работы с этим руководством вам понадобится модуль Azure PowerShell 3.6 или более поздней версии. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). 

## <a name="create-a-rules-file"></a>Создание файла правил

Чтобы ограничить допустимые для установки расширения, вам потребуется [правило](/azure/azure-policy/policy-definition#policy-rule) с логикой определения расширений.

В этом примере показано, как можно запретить расширения, опубликованные издателем Microsoft.Compute, создав файл правил в Azure Cloud Shell. Если вы используете PowerShell локально, можете создать локальный файл и заменить путь $home/clouddrive фактическим расположением файла на компьютере.

В [Cloud Shell](https://shell.azure.com/powershell) введите такой текст:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Скопируйте следующий код JSON и вставьте его в файл.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Закончив, нажмите клавиши **Ctrl+O** и подтвердите сохранение файла клавишей **ВВОД**. Затем нажмите **Ctrl+X**, чтобы закрыть файл и редактор.

## <a name="create-a-parameters-file"></a>Создание файла параметров

Вам понадобится также файл [параметров](/azure/azure-policy/policy-definition#parameters), который позволяет создать структуру для передачи списка блокируемых расширений. 

В этом примере показано, как создать в Cloud Shell файл параметров для виртуальных машин Windows. Если вы используете PowerShell локально, можете создать локальный файл и заменить путь $home/clouddrive фактическим расположением файла на компьютере.

В [Cloud Shell](https://shell.azure.com/powershell) введите такой текст:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Скопируйте следующий код JSON и вставьте его в файл.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

Закончив, нажмите клавиши **Ctrl+O** и подтвердите сохранение файла клавишей **ВВОД**. Затем нажмите **Ctrl+X**, чтобы закрыть файл и редактор.

## <a name="create-the-policy"></a>Создание политики

Определение политики — это объект для хранения конфигурации, которую нужно использовать. Для определения политики используются файлы правил и параметров. Создайте определение политики с помощью командлета [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition).

 Правила и параметры этой политики представлены в виде файлов, которые вы создали в формате JSON и сохранили с помощью Cloud Shell.


```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Назначение политики

В этом примере назначается политика для группы ресурсов с помощью командлета [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment). На всех виртуальных машинах, созданных в группе ресурсов **myResourceGroup**, невозможно будет установить агент доступа к виртуальной машине и пользовательские расширения. 

Командлет [Get-AzureRMSubscription | Format-Table](/powershell/module/azurerm.profile/get-azurermsubscription) предоставит вам идентификатор подписки, который следует указать вместо образца в примере.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzureRMPolicyAssignment `
   -Name "not-allowed-vmextension-windows" `
   -Scope $scope `
   -PolicyDefinition $definition `
   -PolicyParameter '{
    "notAllowedExtensions": {
        "value": [
            "VMAccessAgent",
            "CustomScriptExtension"
        ]
    }
}'
$assignment
```

## <a name="test-the-policy"></a>Проверка политики

Чтобы проверить политику, попробуйте применить расширение для доступа к виртуальной машине. Следующий пример должен завершиться ошибкой с сообщением "Set-AzureRmVMAccessExtension : Resource 'myVMAccess' was disallowed by policy" (Ресурс myVMAccess запрещен политикой).

```azurepowershell-interactive
Set-AzureRmVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

Попытка сменить пароль на портале должна завершиться ошибкой с сообщением "Не удалось развернуть шаблон, нарушена политика" .

## <a name="remove-the-assignment"></a>Удаление назначения

```azurepowershell-interactive
Remove-AzureRMPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Удаление политики

```azurepowershell-interactive
Remove-AzureRmPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения см. в статье [Что такое служба "Политика Azure"?](../../azure-policy/azure-policy-introduction.md)
