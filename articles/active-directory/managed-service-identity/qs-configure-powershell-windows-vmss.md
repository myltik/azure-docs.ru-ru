---
title: Настройка MSI в масштабируемом наборе виртуальных машин Azure с помощью PowerShell
description: Пошаговые инструкции по настройке назначенных системой и пользовательских удостоверений в масштабируемом наборе виртуальных машин Azure с помощью PowerShell.
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
ms.openlocfilehash: 97c5e2dde3faeaad13317597bef4f70455d22102
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930135"
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-powershell"></a>Настройка управляемого удостоверения службы (MSI) в масштабируемом наборе виртуальных машин (VMSS) с помощью PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Управляемое удостоверение службы предоставляет службы Azure с автоматически управляемыми удостоверениями в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

В этой статье вы узнаете, как выполнять следующие операции с управляемым удостоверением службы в масштабируемом наборе виртуальных машин Azure (VMSS) с помощью PowerShell:
- включать и отключать назначенное системой удостоверение в масштабируемом наборе виртуальных машин Azure;
- добавлять и удалять назначенное пользователем удостоверение в масштабируемом наборе виртуальных машин Azure.

## <a name="prerequisites"></a>предварительным требованиям

- Если вы не работали с управляемым удостоверением службы, см. [общие сведения](overview.md). **Обратите внимание на [различие между назначенным системой и пользовательским удостоверениями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Установите [последнюю версию Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM), если это еще не сделано. 

## <a name="system-assigned-managed-identity"></a>Управляемое удостоверение, назначенное системой

В этом разделе вы узнаете, как включать и удалять назначенное системой удостоверение с помощью Azure PowerShell.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Включение назначенного системой удостоверения во время создания масштабируемого набора виртуальных машин Azure

Чтобы создать масштабируемый набор виртуальных машин Azure с включенным удостоверением, назначенным системой, выполните указанные ниже действия.

1. Инструкции по созданию масштабируемого набора виртуальных машин с удостоверением, назначенным системой, см. в *примере 1* в справочной статье по командлету [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig).  Добавьте параметр `-IdentityType SystemAssigned` в командлет `New-AzureRmVmssConfig`:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. Добавьте расширение MSI для VMSS, используя параметр `-Name` и `-Type` в командлете [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) (необязательно). Вы можете передать значения ManagedIdentityExtensionForWindows или ManagedIdentityExtensionForLinux (в зависимости от типа виртуальной машины) и задать имя с помощью параметра `-Name`. В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена:

    > [!NOTE]
    > Этот шаг необязателен, так как для получения токенов можно также использовать конечную точку службы метаданных экземпляров Azure (IMDS).

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-vmss"></a>Включение назначенного системой удостоверения в существующем масштабируемом наборе виртуальных машин Azure

Если нужно включить назначенное системой удостоверение в существующем масштабируемом наборе виртуальных машин Azure, выполните указанные ниже действия.

1. Войдите в Azure, используя команду `Login-AzureRmAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину. Учетная запись должна принадлежать роли, которая предоставляет разрешения на запись в виртуальной машине, например "Участник виртуальных машин":

   ```powershell
   Login-AzureRmAccount
   ```

2. Сначала извлеките свойства масштабируемого набора виртуальных машин с помощью командлета [`Get-AzureRmVmss`](/powershell/module/azurerm.compute/get-azurermvmss). Затем, чтобы включить назначенное системой удостоверение, используйте параметр `-IdentityType` в командлете [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm).

   ```powershell
   $vm = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVM -IdentityType "SystemAssigned"
   ```

3. Добавьте расширение MSI для VMSS, используя параметр `-Name` и `-Type` в командлете [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension). Вы можете передать значения ManagedIdentityExtensionForWindows или ManagedIdentityExtensionForLinux (в зависимости от типа виртуальной машины) и задать имя с помощью параметра `-Name`. В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vmss"></a>Отключение назначенного системой удостоверения в масштабируемом наборе виртуальных машин Azure

> [!NOTE]
> Отключение управляемого удостоверения службы в масштабируемом наборе виртуальных машин сейчас не поддерживается. А пока вы можете переключаться между назначенным системой и пользовательским удостоверениями.

Если в масштабируемом наборе виртуальных машин не требуется назначенное системой удостоверение, но по-прежнему требуется пользовательское удостоверение, используйте следующий командлет:

1. Войдите в Azure, используя команду `Login-AzureRmAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину. Учетная запись должна принадлежать роли, которая предоставляет разрешения на запись в виртуальной машине, например "Участник виртуальных машин":

2. Выполните следующий командлет:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
    ```

## <a name="user-assigned-identity"></a>Удостоверение, назначенное пользователем

В этом разделе вы узнаете, как добавлять и удалять пользовательское удостоверение из масштабируемого набора виртуальных машин с помощью Azure PowerShell.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-vmss"></a>Добавление пользовательского удостоверения во время создания масштабируемого набора виртуальных машин Azure

Сейчас создание масштабируемого набора виртуальных машин с пользовательским удостоверением с помощью PowerShell не поддерживается. Сведения о добавлении пользовательского удостоверение в существующий масштабируемый набор виртуальных машин см. в следующем разделе. Загляните сюда позже, чтобы проверить наличие новой информации.

### <a name="assign-a-user-identity-to-an-existing-azure-vmss"></a>Назначение пользовательского удостоверения существующему масштабируемому набору виртуальных машин Azure

Чтобы назначить пользовательское удостоверение существующему масштабируемому набору виртуальных машин Azure, выполните указанные ниже действия.

1. Войдите в Azure, используя команду `Connect-AzureRmAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину. Учетная запись должна принадлежать роли, которая предоставляет разрешения на запись в виртуальной машине, например "Участник виртуальных машин":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Сначала извлеките свойства виртуальной машины с помощью командлета `Get-AzureRmVM`. Затем, чтобы назначить пользовательское удостоверение масштабируемому набору виртуальных машин Azure, используйте параметры `-IdentityType` и `-IdentityID` с командлетом [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm). Замените `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` собственными значениями.

   > [!IMPORTANT]
   > В назначенных пользователем удостоверениях можно использовать только буквы, цифры и символ дефиса (0–9, a–z, A–Z и -). Кроме того, чтобы назначение виртуальной машине или VMSS производилось правильно, длина имени не должна превышать 24 символа. Загляните сюда позже, чтобы проверить наличие новой информации. Дополнительные сведения см. в разделе [Часто задаваемые вопросы и известные проблемы](known-issues.md).


   ```powershell
   $vmss = Get-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME>
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -VM $vmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Удаление назначенного пользователем удостоверения из масштабируемого набора виртуальных машин Azure

> [!NOTE]
> В настоящее время удалить все назначенные пользователем удостоверения из масштабируемого набора виртуальных машин можно только при наличии назначенного системой удостоверения. Загляните сюда позже, чтобы проверить наличие новой информации.

Если у масштабируемого набора виртуальных машин несколько пользовательских удостоверений, с помощью приведенных ниже команд можно удалить все удостоверения, кроме последнего. Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<VMSS NAME>` собственными. `<MSI NAME>` — это имя пользовательского удостоверения, которое следует оставить в масштабируемом наборе виртуальных машин. Эти сведения можно найти в разделе удостоверений масштабируемого набора виртуальных машин с помощью команды `az vmss show`:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachineScaleSet $vmss
```

Если у масштабируемого набора виртуальных машин есть как назначенное системой, так и пользовательское удостоверения, вы можете удалить все пользовательские удостоверения, переключившись на использование только назначенного системой. Используйте следующую команду:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = $null
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachine $vmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Связанная информация

- [Управляемое удостоверение службы (MSI) для Azure Active Directory](overview.md)
- Ниже приведены комплексные краткие руководства по созданию виртуальных машин Azure:
  
  - [Создание виртуальной машины Windows с помощью PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Создание виртуальной машины Linux с помощью PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















