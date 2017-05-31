---
title: "Настройка виртуальной машины Windows в Azure | Документы Майкрософт"
description: "Вы можете узнать, как использовать расширение настраиваемых сценариев и Key Vault, чтобы настроить виртуальные машины Windows в Azure."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 014d282daffdbfc03e7f3495f8e59bfda4cdb396
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017

---

# <a name="how-to-customize-a-windows-virtual-machine-in-azure"></a>Настройка виртуальной машины Windows в Azure
Для быстрой и согласованной настройки виртуальных машин, как правило, применяются средства автоматизации. Часто для настройки виртуальной машины Windows применяется [расширение настраиваемых сценариев для Windows](extensions-customscript.md). Из этого руководства вы узнаете, как выполнить следующие задачи:

> [!div class="checklist"]
> * Использование расширения пользовательских скриптов на установки IIS
> * Создание виртуальной машины, которая использует расширение пользовательских скриптов
> * Просмотр выполнения сайта IIS после применения расширения

Для работы с этим руководством требуется модуль Azure PowerShell версии не ниже 3.6. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="custom-script-extension-overview"></a>Общие сведения о расширении настраиваемых сценариев
Расширение настраиваемых сценариев скачивает и выполняет сценарии на виртуальных машинах Azure. Это расширение можно использовать для настройки после развертывания, установки программного обеспечения и других задач настройки или управления. Сценарии можно скачать из службы хранилища Azure или GitHub или передать на портал Azure во время выполнения расширения.

Расширение пользовательских сценариев интегрируется с шаблонами Azure Resource Manager, а также его можно запустить с помощью интерфейса командной строки Azure, PowerShell, портала Azure или API REST виртуальной машины Azure.

Расширение настраиваемых сценариев можно использовать для виртуальных машин как в Windows, так и в Linux.


## <a name="create-virtual-machine"></a>Создание виртуальной машины
Прежде чем создать виртуальную машину, выполните команду [az group create](/powershell/module/azurerm.resources/new-azurermresourcegroup) для создания группы ресурсов. В следующем примере создается группа ресурсов с именем *myResourceGroupAutomate* в расположении *EastUS*.

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupAutomate -Location EastUS
```

Укажите имя и пароль администратора для виртуальной машины с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Теперь вы можете создать виртуальную машину с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Следующий пример создает необходимые компоненты виртуальной сети, конфигурацию операционной системы, а затем виртуальную машину *myVM*.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -Name myVnet `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$publicIP = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4 `
    -Name "myPublicIP"

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP  `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleWWW  `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1001 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80 `
    -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP,$nsgRuleWeb

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIP.Id `
    -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

New-AzureRmVM -ResourceGroupName myResourceGroupAutomate -Location EastUS -VM $vmConfig
```

Создание этих ресурсов и виртуальной машины может занять несколько минут.


## <a name="automate-iis-install"></a>Автоматизация установки IIS
Воспользуйтесь командлетом [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension), чтобы установить расширение настраиваемых сценариев. Это расширение запускает `powershell Add-WindowsFeature Web-Server` для установки веб-сервера IIS, а затем обновляет страницу *Default.htm* для отображения имени узла виртуальной машины.

```powershell
Set-AzureRmVMExtension -ResourceGroupName myResourceGroupAutomate `
    -ExtensionName IIS `
    -VMName myVM `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
```


## <a name="test-web-site"></a>Проверка веб-сайта
Получите общедоступный IP-адрес своего балансировщика нагрузки с помощью командлета [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Следующий пример позволяет получить IP-адрес для созданного ранее *myPublicIP*.

```powershell
Get-AzureRmPublicIPAddress `
    -ResourceGroupName myResourceGroupAutomate `
    -Name myPublicIP | select IpAddress
```

После этого можно ввести общедоступный IP-адрес в веб-браузер. Отображается веб-сайт, а также имя узла виртуальной машины, на которую балансировщик нагрузки направил трафик, как показано в следующем примере:

![Выполнение веб-сайта IIS](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике вы автоматизировали установку IIS на виртуальной машине. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Использование расширения пользовательских скриптов на установки IIS
> * Создание виртуальной машины, которая использует расширение пользовательских скриптов
> * Просмотр выполнения сайта IIS после применения расширения

Перейдите к следующему руководству, чтобы научиться создавать пользовательские образы виртуальных машин.

> [!div class="nextstepaction"]
> [Создание образа настраиваемой виртуальной машины](./tutorial-custom-images.md)

