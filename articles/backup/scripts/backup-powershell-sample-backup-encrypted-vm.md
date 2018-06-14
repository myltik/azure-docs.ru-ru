---
title: Пример сценария Azure PowerShell. Резервное копирование виртуальной машины Azure | Документы Майкрософт
description: Пример сценария Azure PowerShell. Резервное копирование виртуальной машины Azure
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
tags: ''
ms.assetid: ''
ms.service: backup
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/07/2017
ms.author: markgal
ms.custom: mvc
ms.openlocfilehash: 4376add4a2e51806bd5db228ad2fe2afcf2e4f57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "22997529"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Резервное копирование зашифрованной виртуальной машины Azure с помощью PowerShell

Этот сценарий создает хранилище служб восстановления и геоизбыточное хранилище (GRS) для зашифрованной виртуальной машины Azure. Для хранилища применяется политика защиты по умолчанию. Она ежедневно создает резервную копию виртуальной машины и хранит эти копии в течение 30 дней. Сценарий также инициирует начальную точку восстановления для виртуальной машины и сохраняет ее в течение 365 дней. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

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
| [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/New-AzureRmRecoveryServicesVault) | Создает хранилище служб восстановления для хранения резервных копий. | 
| [Set-AzureRmRecoveryServicesBackupProperties](/powershell/module/azurerm.recoveryservices/Set-AzureRmRecoveryServicesBackupProperties) | Задает свойства хранения резервных копий для хранилища служб восстановления. | 
| [New-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)| Создает политику защиты с помощью политик расписания и хранения в хранилище служб восстановления. | 
| [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) | Задает разрешения для Key Vault, предоставляя субъекту-службе доступ к ключам шифрования. | 
| [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection) | Позволяет выполнять резервное копирование элемента с помощью указанной политики защиты Azure Backup. | 
| [Set-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy)| Изменяет существующую политику защиты Azure Backup. | 
| [Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem) | Запускает резервное копирование для защищенного элемента Azure Backup, который не привязан к расписанию резервного копирования. |
| [Wait-AzureRmRecoveryServicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob) | Ожидает завершения задания Azure Backup. | 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Удаляет группу ресурсов и все ресурсы, содержащиеся в ней. | 

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

