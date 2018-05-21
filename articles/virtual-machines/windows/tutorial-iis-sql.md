---
title: Руководство. Создание виртуальных машин с выполняемым стеком SQL, IIS и .NET в Azure | Документация Майкрософт
description: В этом руководстве описано, как установить стек SQL, IIS и .NET Azure на виртуальной машине Windows в Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f9fea933dd664955a0bc6f47db775fbc469fd684
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
---
# <a name="tutorial-install-the-sql47iis47net-stack-in-a-windows-vm-with-azure-powershell"></a>Руководство. Установка стека SQL, IIS и .NET на виртуальной машине Windows с помощью Azure PowerShell.

Используя это руководство, вы установите стек SQL, IIS и .NET с помощью Azure PowerShell. Этот стек состоит из двух виртуальных машин на базе Windows Server 2016: одной с IIS и .NET, а другой с SQL Server.

> [!div class="checklist"]
> * Создание виртуальной машины 
> * Установка пакетов SDK для IIS и .NET Core на виртуальной машине
> * создание виртуальной машины с SQL Server;
> * установка расширения SQL Server.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально, для работы с этим руководством вам понадобится модуль AzureRM.Compute 5.7.0 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM.Compute`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-iis-vm"></a>Создание виртуальной машины с IIS 

В этом примере мы воспользуемся командлетом [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm) в Cloud Shell для PowerShell, чтобы быстро создать виртуальную машину под управлением Windows Server 2016, а затем установим IIS и .NET Framework. Виртуальные машины с IIS и SQL используют одну группу ресурсов и виртуальную сеть, поэтому мы создадим переменные для этих имен.


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

Установите IIS и .NET Framework, используя расширение пользовательского скрипта.

```azurepowershell-interactive
Set-AzureRmVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>Создание другой подсети

Создайте вторую подсеть для виртуальной машины SQL. Получите виртуальную сеть, выполнив команду [Get-AzureRmVirtualNetwork]{/powershell/module/azurerm.network/get-azurermvirtualnetwork}.

```azurepowershell-interactive
$vNet = Get-AzureRmVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

Создайте конфигурацию подсети, выполнив командлет [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig).


```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

Задайте для виртуальной сети сведения о новой подсети, выполнив командлет [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)
   
```azurepowershell-interactive   
$vNet | Set-AzureRmVirtualNetwork
```

## <a name="azure-sql-vm"></a>Виртуальная машина с SQL Azure

Для создания виртуальной машины c SQL используйте предварительно настроенный образ SQL Server из Azure Marketplace. Сначала мы создадим виртуальную машину, а затем установим на нее расширение SQL Server. 


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

Чтобы добавить [расширение SQL Server](/sql/virtual-machines-windows-sql-server-agent-extension.md) на виртуальную машину SQL, используйте командлет [Set-AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension).

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы установили стек для SQL, IIS и .NET с помощью Azure PowerShell. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Создание виртуальной машины 
> * Установка пакетов SDK для IIS и .NET Core на виртуальной машине
> * создание виртуальной машины с SQL Server;
> * установка расширения SQL Server.

Перейдите к следующему руководству, чтобы узнать, как защитить веб-сервер IIS с помощью SSL-сертификатов.

> [!div class="nextstepaction"]
> [Защита веб-сервера IIS с помощью SSL-сертификатов](tutorial-secure-web-server.md)

