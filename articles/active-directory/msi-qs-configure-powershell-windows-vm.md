---
title: "Настройка MSI на виртуальной машине Azure с помощью PowerShell"
description: "Пошаговые инструкции по настройке управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью PowerShell."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: 612311f1c4e081e87dde76ce4a1d8efd46428c06
ms.contentlocale: ru-ru
ms.lasthandoff: 09/22/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью PowerShell

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Управляемое удостоверение службы предоставляет службы Azure с автоматически управляемыми удостоверениями в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

Из этой статьи вы узнаете, как включить и удалить MSI для виртуальных машин Azure с помощью PowerShell.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

Установите [Azure PowerShell версии 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1), если еще не сделали этого.

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Включение MSI во время создания виртуальной машины Azure

Чтобы создать виртуальную машину с поддержкой MSI:

1. Используя одно из кратких руководств ниже выполните действия только в нужных разделах ("Вход в Azure", "Создание группы ресурсов", "Создание группы сети", "Создание виртуальной машины"). 

   > [!IMPORTANT] 
   > При выполнении действий, описанных в разделе о создании виртуальной машины, не забудьте внести небольшие изменения в синтаксис командлета [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm). Добавьте параметр `-IdentityType "SystemAssigned"`, чтобы подготовить виртуальную машину для MSI. Например:
   >  
   > `$vmConfig = New-AzureRmVMConfig -VMName myVM -IdentityType "SystemAssigned" ...`

   - [Создание виртуальной машины Windows с помощью PowerShell](../virtual-machines/windows/quick-create-powershell.md)
   - [Создание виртуальной машины Linux с помощью PowerShell](../virtual-machines/linux/quick-create-powershell.md)



2. Добавьте расширение MSI для виртуальной машины, используя параметр `-Type` в командлете [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Вы можете передать значения ManagedIdentityExtensionForWindows или ManagedIdentityExtensionForLinux (в зависимости от типа виртуальной машины) и задать имя с помощью параметра `-Name`. В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Включение MSI на имеющейся виртуальной машине Azure

Если нужно включить MSI на имеющейся виртуальной машине, сделайте следующее:

1. Войдите в Azure, используя команду `Login-AzureRmAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину. Учетная запись должна принадлежать роли, которая предоставляет разрешения на запись в виртуальной машине, например "Участник виртуальных машин":

   ```powershell
   Login-AzureRmAccount
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

1. Войдите в Azure, используя команду `Login-AzureRmAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину. Учетная запись должна принадлежать роли, которая предоставляет разрешения на запись в виртуальной машине, например "Участник виртуальных машин":

   ```powershell
   Login-AzureRmAccount
   ```

2. Используйте параметр `-Name` для командлета [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension), указав то же имя, которое вы использовали при добавлении расширения:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>Связанная информация

- [Управляемое удостоверение службы (MSI) для Azure Active Directory](msi-overview.md)
- Ниже приведены комплексные краткие руководства по созданию виртуальных машин Azure:
  
  - [Создание виртуальной машины Windows с помощью PowerShell](../virtual-machines/windows/quick-create-powershell.md) 
  - [Создание виртуальной машины Linux с помощью PowerShell](../virtual-machines/linux/quick-create-powershell.md) 

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.

















