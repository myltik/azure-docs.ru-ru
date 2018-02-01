---
title: "Создание виртуальных машин со стеком для SQL, IIS и .NET в Azure | Документация Майкрософт"
description: "Руководство по установке стека для SQL, IIS и .NET в Azure на виртуальных машинах Windows."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6533ab205e07243e2f757ea0a66028e1d140c52b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="install-a-sql92iis92net-stack-in-azure"></a>Установка стека для SQL, IIS и .NET в Azure

В этом руководстве мы установим стек для SQL, IIS и .NET с помощью Azure PowerShell. Этот стек состоит из двух виртуальных машин на базе Windows Server 2016: одной с IIS и .NET, а другой с SQL Server.

> [!div class="checklist"]
> * Создание виртуальной машины с помощью командлета New-AzVM
> * Установка пакетов SDK для IIS и .NET Core на виртуальной машине
> * создание виртуальной машины с SQL Server;
> * установка расширения SQL Server.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Если вы решили установить и использовать PowerShell локально, то для работы с этим руководством вам понадобится модуль Azure PowerShell версии 5.1.1 или более поздней. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzureRmAccount`, чтобы создать подключение к Azure.

## <a name="create-a-iis-vm"></a>Создание виртуальной машины с IIS 

В этом примере мы воспользуемся командлетом [New-AzVM](https://www.powershellgallery.com/packages/AzureRM.Compute.Experiments) в Cloud Shell для PowerShell, чтобы быстро создать виртуальную машину под управлением Windows Server 2016, а затем установим IIS и .NET Framework. Виртуальные машины с IIS и SQL используют одну группу ресурсов и виртуальную сеть, поэтому мы создадим переменные для этих имен.

Нажмите кнопку **Попробовать** в правом верхнем углу блока кода, чтобы запустить Cloud Shell в этом окне. Вам будет предложено указать учетные данные для виртуальной машины в командной строке.

```azurepowershell-interactive
$vmName = "IISVM$(Get-Random)"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzureRMVm -Name $vmName -ResourceGroupName $resourceGroup -VirtualNetworkName $vNetName 
```

Установите IIS и .NET Framework, используя расширение пользовательского скрипта.

```azurepowershell-interactive

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="azure-sql-vm"></a>Виртуальная машина с SQL Azure

Для создания виртуальной машины c SQL мы будем использовать предварительно настроенный образ SQL Server из Azure Marketplace. Сначала мы создадим виртуальную машину, а затем установим на нее расширение SQL Server. 


```azurepowershell-interactive
# Create user object. You get a pop-up prompting you to enter the credentials for the VM.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$vNet = Get-AzureRmVirtualNetwork -Name $vNetName -ResourceGroupName $resourceGroup
Add-AzureRmVirtualNetworkSubnetConfig -Name mySQLSubnet -VirtualNetwork $vNet -AddressPrefix "192.168.2.0/24"
Set-AzureRmVirtualNetwork -VirtualNetwork $vNet


# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location eastus `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow


# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location eastus `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name mySQLNic -ResourceGroupName $resourceGroup -Location eastus `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName mySQLVM -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName mySQLVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftSQLServer -Offer SQL2014SP2-WS2012R2 -Skus Enterprise -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create the VM
New-AzureRmVM -ResourceGroupName $resourceGroup -Location eastus -VM $vmConfig
```

Чтобы добавить [расширение SQL Server](/sql/virtual-machines-windows-sql-server-agent-extension.md) на виртуальную машину SQL, используйте командлет [Set-AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension).

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension -ResourceGroupName $resourceGroup -VMName mySQLVM -name "SQLExtension"
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы установили стек для SQL, IIS и .NET с помощью Azure PowerShell. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * создание виртуальной машины с помощью командлета New-AzVM;
> * Установка пакетов SDK для IIS и .NET Core на виртуальной машине
> * создание виртуальной машины с SQL Server;
> * установка расширения SQL Server.

Перейдите к следующему руководству, чтобы узнать, как защитить веб-сервер IIS с помощью SSL-сертификатов.

> [!div class="nextstepaction"]
> [Защита веб-сервера IIS с помощью SSL-сертификатов](tutorial-secure-web-server.md)

