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
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 104c43e6fab2c3f18824a00860c30c8d6f82bbc4
ms.contentlocale: ru-ru
ms.lasthandoff: 09/14/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью PowerShell

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Управляемое удостоверение службы предоставляет службы Azure с автоматически управляемыми удостоверениями в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

Из этой статьи вы узнаете, как включить и удалить MSI для виртуальных машин Windows в Azure с помощью PowerShell.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

Установите [Azure PowerShell версии 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1), если еще не сделали этого.

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Включение MSI во время создания виртуальной машины Azure

Ресурс виртуальной машины Windows с поддержкой MSI создается в новой группе ресурсов с помощью указанных параметров конфигурации. Кроме того, обратите внимание, что многие из этих командлетов могут выполняться более 30 секунд до возвращения ответа, при этом окончательное создание виртуальной машины займет несколько минут.

1. Войдите в Azure, используя команду `Login-AzureRmAccount`. Используйте учетную запись, связанную с подпиской Azure, с помощью которой нужно развернуть виртуальную машину.

   ```powershell
   Login-AzureRmAccount
   ```

2. Чтобы сохранить и развернуть виртуальную машину и связанные с ней ресурсы, создайте [группу ресурсов](../azure-resource-manager/resource-group-overview.md#terminology) с помощью командлета `New-AzureRmResourceGroup`. Если вы уже создали группу ресурсов, которую можно использовать, этот шаг можно пропустить:

   ```powershell
   New-AzureRmResourceGroup -Name myResourceGroup -Location WestUS
   ```
3. Создайте сетевые ресурсы для виртуальной машины.

   а. Создайте виртуальную сеть, подсеть и общедоступный IP-адрес. Эти ресурсы используются для того, чтобы установить сетевое подключение к виртуальной машине и подключить ее к Интернету.

   ```powershell
   # Create a subnet configuration
   $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location WestUS -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

   # Create a public IP address and specify a DNS name
   $pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location WestUS -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
   ```

   b. Создайте группу безопасности сети и правило группы безопасности сети. Группа безопасности сети обеспечивает защиту виртуальной машины с помощью правил для входящего и исходящего трафика. В нашем случае создается правило для входящего трафика порта 3389, которое разрешает входящие подключения к удаленному рабочему столу. Мы также создадим правило входящего трафика для порта 80, разрешающее входящий веб-трафик.

   ```powershell
   # Create an inbound network security group rule for port 3389
   $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   # Create an inbound network security group rule for port 80
   $nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80 -Access Allow

   # Create a network security group
   $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location WestUS -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
   ```

   c. Создайте виртуальную сетевую карту для виртуальной машины. Сетевая карта подключает виртуальную машину к подсети, группе безопасности сети и общедоступному IP-адресу.

   ```powershell
   # Create a virtual network card and associate with public IP address and NSG
   $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location WestUS -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
   ```

4. Создайте виртуальную машину.

   а. Создайте настраиваемый объект виртуальной машины. Эти параметры используются при развертывании виртуальной машины, в том числе образ виртуальной машины, ее размер и конфигурации аутентификации. Еcли использовать параметр `-IdentityType "SystemAssigned"` в командлете [New AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm), виртуальная машина подготовится с MSI. Командлет `Get-Credential` запрашивает учетные данные, настроенные в качестве имени пользователя и пароля для виртуальной машины.

   ```powershell
   # Define a credential object (prompts for user/password to be used for VM authentication)
   $cred = Get-Credential

   # Create a configurable VM object with a Managed Service Identity
   $vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 -IdentityType "SystemAssigned" | Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
   ```

   b. Подготовьте новую виртуальную машину:

   ```powershell
   New-AzureRmVM -ResourceGroupName myResourceGroup -Location WestUS -VM $vmConfig
   ```

5. Добавьте расширение MSI для виртуальной машины, используя параметр `-Type "ManagedIdentityExtensionForWindows"` в командлете [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Включение MSI на имеющейся виртуальной машине Azure

Если нужно включить MSI на имеющейся виртуальной машине, сделайте следующее:

1. Войдите в Azure, используя команду `Login-AzureRmAccount`. Используйте учетную запись, которая связана с подпиской Azure, с помощью которой нужно развернуть виртуальную машину.

   ```powershell
   Login-AzureRmAccount
   ```

2. Сначала извлеките свойства виртуальной машины с помощью командлета `Get-AzureRmVM`. Затем, чтобы включить MSI, используйте параметр `-IdentityType` в командлете [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm).

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. Добавьте расширение MSI для виртуальной машины, используя параметр `-Type "ManagedIdentityExtensionForWindows"` в командлете [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена. Убедитесь, что указан правильный параметр `-Location`, совпадающий с расположением имеющейся виртуальной машины:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Удаление MSI с виртуальной машины Azure

Если виртуальной машине больше не нужен MSI, можно использовать командлет `RemoveAzureRmVMExtension`, чтобы удалить его с виртуальной машины:

1. Используйте параметр `-Name "ManagedIdentityExtensionForWindows"` с командлетом [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension):

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>Связанная информация

- [Управляемое удостоверение службы (MSI) для Azure Active Directory](msi-overview.md)
- Информация в этой статье взята из краткого руководства [Создание виртуальной машины Windows с помощью PowerShell](../virtual-machines/windows/quick-create-powershell.md) и содержит инструкции для MSI. 

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.

















