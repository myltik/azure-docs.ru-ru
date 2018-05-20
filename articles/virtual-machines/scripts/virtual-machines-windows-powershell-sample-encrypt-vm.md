---
title: Пример сценария Azure PowerShell. Шифрование виртуальной машины Windows | Документация Майкрософт
description: Пример сценария Azure PowerShell для шифрования виртуальной машины Windows.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: iainfou
ms.openlocfilehash: f405cdaf61d6aaafa8568a9d7f21614071285c17
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="encrypt-a-windows-virtual-machine-with-azure-powershell"></a>Шифрование виртуальной машины Windows с помощью Azure PowerShell

Этот сценарий создает безопасное Azure Key Vault, ключи шифрования, субъект-службу Azure Active Directory и виртуальную машину Windows. Затем эта виртуальная машина шифруется с помощью ключа шифрования из Key Vault и учетных данных субъекта-службы.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/virtual-machine/encrypt-vm/encrypt-windows-vm.ps1 "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Чтобы создать развертывание, скрипт использует следующие команды. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Get-Help | Заметки |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) | Создает Azure Key Vault для хранения защищенных данных, таких как ключи шифрования. |
| [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) | Создает ключ шифрования в Key Vault. |
| [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) | Создает субъект-службу Azure Active Directory для безопасной аутентификации и контроля доступа к ключам шифрования. |
| [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) | Задает разрешения для Key Vault, предоставляя субъекту-службе доступ к ключам шифрования. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Создает виртуальную машину и подключает ее к сетевой карте, виртуальной сети, подсети и группе безопасности сети. Эта команда также открывает порт 80 и задает учетные данные администратора. |
| [Get-AzureRmKeyVault](/powershell/module/azurerm.keyvault/get-azurermkeyvault) | Получает необходимые сведения о Key Vault. |
| [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) | Включает шифрование на виртуальной машине с помощью учетных данных субъекта-службы и ключа шифрования. |
| [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) | Отображает состояние шифрования виртуальной машины. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Удаляет группу ресурсов и все ресурсы, содержащиеся в ней. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев PowerShell для виртуальных машин представлены в [документации по виртуальным машинам Azure под управлением Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
