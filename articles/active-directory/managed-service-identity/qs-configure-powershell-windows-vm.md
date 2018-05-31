---
title: Настройка MSI на виртуальной машине Azure с помощью PowerShell
description: Пошаговые инструкции по настройке управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 6981c0f917fb7175f444ceca8c55c0df186774db
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33932325"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Управляемое удостоверение службы предоставляет службы Azure с автоматически управляемыми удостоверениями в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

<a name="in-this-article-you-learn-how-to-perform-the-following-managed-service-identity-operations-on-an-azure-vm-using-powershell"></a>В этой статье вы узнаете, как выполнять следующие операции с Управляемым удостоверением службы в виртуальной машине Azure VM с помощью PowerShell:
- 

## <a name="prerequisites"></a>предварительным требованиям

- Если вы не работали с Управляемым удостоверением службы, см. [общие сведения](overview.md). **Обратите внимание на [различие между назначенным системой и пользовательским удостоверениями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Установите [последнюю версию Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM), если это еще не сделано.

## <a name="system-assigned-identity"></a>Удостоверение, назначенное системой

В этом разделе вы узнаете, как включить и отключить назначенное системой удостоверение с помощью Azure PowerShell.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Включение назначенного системой удостоверения во время создания виртуальной машины Azure

Чтобы создать виртуальную машину Azure с включенным удостоверением, назначенным системой, выполните указанные ниже действия.

1. Используя одно из кратких руководств ниже выполните действия только в нужных разделах ("Вход в Azure", "Создание группы ресурсов", "Создание группы сети", "Создание виртуальной машины").
    
    При выполнении действий, описанных в разделе о создании виртуальной машины, не забудьте внести небольшие изменения в синтаксис командлета [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm). Чтобы подготовить виртуальную машину с включенным удостоверением, назначенным системой, добавьте параметр `-AssignIdentity "SystemAssigned"`, например:
      
    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity "SystemAssigned" ...
    ```

   - [Создание виртуальной машины Windows с помощью PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Создание виртуальной машины Linux с помощью PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. Добавьте расширение MSI для виртуальной машины, используя параметр `-Type` в командлете [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) (необязательно). Вы можете передать значения ManagedIdentityExtensionForWindows или ManagedIdentityExtensionForLinux (в зависимости от типа виртуальной машины) и задать имя с помощью параметра `-Name`. В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Этот шаг необязателен, так как для получения токенов можно также использовать конечную точку службы метаданных экземпляров Azure (IMDS).

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Включение назначенного системой удостоверения в имеющейся виртуальной машине Azure

Если нужно включить назначенное системой удостоверение в имеющейся виртуальной машине, выполните указанные ниже действия.

1. Войдите в Azure, используя команду `Login-AzureRmAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину. Учетная запись должна принадлежать роли, которая предоставляет разрешения на запись в виртуальной машине, например "Участник виртуальных машин":

   ```powershell
   Login-AzureRmAccount
   ```

2. Сначала извлеките свойства виртуальной машины с помощью командлета `Get-AzureRmVM`. Затем, чтобы включить назначенное системой удостоверение, используйте параметр `-AssignIdentity` в командлете [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm).

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity "SystemAssigned"
   ```

3. Добавьте расширение MSI для виртуальной машины, используя параметр `-Type` в командлете [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) (необязательно). Вы можете передать значения ManagedIdentityExtensionForWindows или ManagedIdentityExtensionForLinux (в зависимости от типа виртуальной машины) и задать имя с помощью параметра `-Name`. В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена. Убедитесь, что указан правильный параметр `-Location`, совпадающий с расположением имеющейся виртуальной машины:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Этот шаг необязателен, так как для получения токенов можно также использовать конечную точку службы метаданных экземпляров Azure (IMDS).

## <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Отключение назначенного системой удостоверения в виртуальной машине Azure

> [!NOTE]
>  Отключение Управляемого удостоверения службы в виртуальной машине в настоящее время не поддерживается. А пока вы можете переключаться между назначенным системой и пользовательским удостоверениями.

Если в виртуальной машине больше не требуется назначенное системой удостоверение, но по-прежнему требуется удостоверение, назначенное пользователем, используйте следующий командлет:

1. Войдите в Azure, используя команду `Login-AzureRmAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину. Учетная запись должна принадлежать роли, которая предоставляет разрешения на запись в виртуальной машине, например "Участник виртуальных машин":

   ```powershell
   Login-AzureRmAccount
   ```

2. Выполните следующий командлет: 
    ```powershell       
    Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -IdentityType "UserAssigned"
    ```
Чтобы удалить расширение MSI для виртуальной машины, используйте параметр -Name в командлете [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension), указав то же имя, которое вы использовали при добавлении расширения:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-identity"></a>Удостоверение, назначенное пользователем

В этом разделе вы узнаете, как добавить и удалить назначенное пользователем удостоверение для виртуальной машины с помощью Azure PowerShell.

### <a name="assign-a-user-assigned-identity-to-a-vm-during-creation"></a>Присвоение назначенного пользователем удостоверения виртуальной машине во время ее создания

Чтобы присвоить назначенное пользователем удостоверение виртуальной машине Azure во время ее создания, выполните указанные ниже действия.

1. Используя одно из кратких руководств ниже выполните действия только в нужных разделах ("Вход в Azure", "Создание группы ресурсов", "Создание группы сети", "Создание виртуальной машины"). 
  
    При выполнении действий, описанных в разделе о создании виртуальной машины, не забудьте внести небольшие изменения в синтаксис командлета [`New-AzureRmVMConfig`](/powershell/module/azurerm.compute/new-azurermvm). Добавьте параметры `-IdentityType UserAssigned` и `-IdentityID `, чтобы подготовить виртуальную машину с назначенным пользователем удостоверением.  Замените `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` и `<MSI NAME>` собственными значениями.  Например: 
    
    ```powershell 
    $vmConfig = New-AzureRmVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>..."
    ```
    
    - [Создание виртуальной машины Windows с помощью PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Создание виртуальной машины Linux с помощью PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. Добавьте расширение MSI для виртуальной машины, используя параметр `-Type` в командлете [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) (необязательно). Вы можете передать значения ManagedIdentityExtensionForWindows или ManagedIdentityExtensionForLinux (в зависимости от типа виртуальной машины) и задать имя с помощью параметра `-Name`. В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена. Убедитесь, что указан правильный параметр `-Location`, совпадающий с расположением имеющейся виртуальной машины:
      > [!NOTE]
    > Этот шаг необязателен, так как для получения токенов можно также использовать конечную точку службы метаданных экземпляров Azure (IMDS).

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-identity-to-an-existing-azure-vm"></a>Присвоение пользовательского удостоверения имеющейся виртуальной машине Azure

Чтобы присвоить назначенное пользователем удостоверение имеющейся виртуальной машине Azure, выполните указанные ниже действия.

1. Войдите в Azure, используя команду `Connect-AzureRmAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину. Учетная запись должна принадлежать роли, которая предоставляет разрешения на запись в виртуальной машине, например "Участник виртуальных машин":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Создайте назначенное пользователем удостоверение с помощью командлета [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity).  Обратите внимание на свойство `Id` в выходных данных. Оно потребуется в следующем шаге.

    > [!IMPORTANT]
    > В назначенных пользователем удостоверениях можно использовать только буквы, цифры и символ дефиса (0–9, a–z, A–Z и -). Кроме того, чтобы назначение виртуальной машине или VMSS производилось правильно, длина имени не должна превышать 24 символа. Загляните сюда позже, чтобы проверить наличие новой информации. Дополнительные сведения см. в разделе [Часто задаваемые вопросы и известные проблемы](known-issues.md).


  ```powershell
  New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
  ```
3. Извлеките свойства виртуальной машины с помощью командлета `Get-AzureRmVM`. Затем, чтобы присвоить назначенное пользователем удостоверение виртуальной машине Azure, используйте параметры `-IdentityType` и `-IdentityID` с командлетом [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm).  Для параметра `-IdentityId` следует использовать значение `Id` из предыдущего шага.  Замените `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` и `<USER ASSIGNED IDENTITY NAME>` собственными значениями.

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. Добавьте расширение MSI для виртуальной машины, используя параметр `-Type` в командлете [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Вы можете передать значения ManagedIdentityExtensionForWindows или ManagedIdentityExtensionForLinux (в зависимости от типа виртуальной машины) и задать имя с помощью параметра `-Name`. В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена. Укажите правильный параметр `-Location`, совпадающий с расположением имеющейся виртуальной машины.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Удаление назначенного пользователем управляемого удостоверения из виртуальной машины Azure

> [!NOTE]
>  В настоящее время удалить все назначенные пользователем удостоверения из виртуальной машины можно только при наличии назначенного системой удостоверения. Загляните сюда позже, чтобы проверить наличие новой информации.

Если у виртуальной машины несколько назначенных пользователем удостоверений, с помощью приведенных ниже команд можно удалить все удостоверения, кроме последнего. Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<VM NAME>` собственными. `<MSI NAME>` — это имя назначенного пользователем удостоверения, которое следует оставить в виртуальной машине. Эти сведения можно найти в разделе удостоверений виртуальной машины с помощью команды `az vm show`:

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm
```

Если у виртуальной машины есть как назначенное системой, так и назначенные пользователем удостоверения, вы можете удалить все назначенные пользователем удостоверения, переключившись на использование только назначенного системой. Используйте следующую команду:

```powershell 
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = $null
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Связанная информация

- [Управляемое удостоверение службы (MSI) для Azure Active Directory](overview.md)
- Ниже приведены комплексные краткие руководства по созданию виртуальных машин Azure:
  
  - [Создание виртуальной машины Windows с помощью PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Создание виртуальной машины Linux с помощью PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 
