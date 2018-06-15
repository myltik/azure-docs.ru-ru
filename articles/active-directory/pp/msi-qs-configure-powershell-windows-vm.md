---
title: Настройка MSI на виртуальной машине Azure с помощью PowerShell
description: Пошаговые инструкции по настройке управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9a466a5c695277a7b5833f997e2ad7281c962f3f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31588257"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью PowerShell

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Управляемое удостоверение службы предоставляет службы Azure с автоматически управляемыми удостоверениями в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

Из этой статьи вы узнаете, как включить и удалить MSI для виртуальных машин Azure с помощью PowerShell.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Кроме того, установите [последнюю версию Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) (версию 4.3.1 или более позднюю), если это еще не сделано.

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Включение MSI во время создания виртуальной машины Azure

Чтобы создать виртуальную машину с поддержкой MSI:

1. Используя одно из кратких руководств ниже выполните действия только в нужных разделах ("Вход в Azure", "Создание группы ресурсов", "Создание группы сети", "Создание виртуальной машины"). 

   > [!IMPORTANT] 
   > При выполнении действий, описанных в разделе о создании виртуальной машины, не забудьте внести небольшие изменения в синтаксис командлета [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm). Добавьте параметр `-IdentityType "SystemAssigned"`, чтобы подготовить виртуальную машину для MSI. Например:
   >  
   > `$vmConfig = New-AzureRmVMConfig -VMName myVM -IdentityType "SystemAssigned" ...`

   - [Создание виртуальной машины Windows с помощью PowerShell](~/articles/virtual-machines/windows/quick-create-powershell.md)
   - [Создание виртуальной машины Linux с помощью PowerShell](~/articles/virtual-machines/linux/quick-create-powershell.md)



2. Добавьте расширение MSI для виртуальной машины, используя параметр `-Type` в командлете [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Вы можете передать значения ManagedIdentityExtensionForWindows или ManagedIdentityExtensionForLinux (в зависимости от типа виртуальной машины) и задать имя с помощью параметра `-Name`. В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Включение MSI на имеющейся виртуальной машине Azure

Если нужно включить MSI на имеющейся виртуальной машине, сделайте следующее:

1. Войдите в Azure, используя команду `Connect-AzureRmAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину. Учетная запись должна принадлежать роли, которая предоставляет разрешения на запись в виртуальной машине, например "Участник виртуальных машин":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Сначала извлеките свойства виртуальной машины с помощью командлета `Get-AzureRmVM`. Затем, чтобы включить MSI, используйте параметр `-IdentityType` в командлете [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm).

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. Добавьте расширение MSI для виртуальной машины, используя параметр `-Type` в командлете [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Вы можете передать значения ManagedIdentityExtensionForWindows или ManagedIdentityExtensionForLinux (в зависимости от типа виртуальной машины) и задать имя с помощью параметра `-Name`. В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена. Убедитесь, что указан правильный параметр `-Location`, совпадающий с расположением имеющейся виртуальной машины:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Удаление MSI с виртуальной машины Azure

Если виртуальной машине больше не нужен MSI, можно использовать командлет `RemoveAzureRmVMExtension`, чтобы удалить его с виртуальной машины:

1. Войдите в Azure, используя команду `Connect-AzureRmAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину. Учетная запись должна принадлежать роли, которая предоставляет разрешения на запись в виртуальной машине, например "Участник виртуальных машин":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Используйте параметр `-Name` для командлета [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension), указав то же имя, которое вы использовали при добавлении расширения:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>Связанная информация

- [Управляемое удостоверение службы (MSI) для Azure Active Directory](msi-overview.md)
- Ниже приведены комплексные краткие руководства по созданию виртуальных машин Azure:
  
  - [Создание виртуальной машины Windows с помощью PowerShell](~/articles/virtual-machines/windows/quick-create-powershell.md) 
  - [Создание виртуальной машины Linux с помощью PowerShell](~/articles/virtual-machines/linux/quick-create-powershell.md) 

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.
















