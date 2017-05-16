---
title: "Краткое руководство по Azure. Создание виртуальной машины Windows с помощью PowerShell | Документация Майкрософт"
description: "Быстро научитесь создавать виртуальные машины Windows с помощью PowerShell."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 7cdbd8d9fbca20d53c8b920b6bc44851b95d2a07
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017


---

# <a name="create-a-windows-virtual-machine-with-powershell"></a>Создание виртуальной машины Windows с помощью PowerShell

Модуль PowerShell используется для создания ресурсов Azure и управления ими с помощью командной строки PowerShell или сценариев. В этом руководстве описывается, как с помощью PowerShell развернуть виртуальную машину Azure под управлением Windows Server 2016. После завершения развертывания мы подключимся к серверу и установим IIS.  

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , прежде чем начинать работу.

Для работы с этим руководством требуется модуль Azure PowerShell версии не ниже 3.6. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью команды `Login-AzureRmAccount` и следуйте инструкциям на экране.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Создать группу ресурсов

Создайте группу ресурсов Azure с помощью командлета [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. 

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-networking-resources"></a>Создание сетевых ресурсов

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>Создайте виртуальную сеть, подсеть и общедоступный IP-адрес. 
Эти ресурсы используются для того, чтобы установить сетевое подключение к виртуальной машине и подключить ее к Интернету.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location EastUS `
    -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location EastUS `
    -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Создайте группу безопасности сети и правило группы безопасности сети. 
Группа безопасности сети обеспечивает защиту виртуальной машины с помощью правил для входящего и исходящего трафика. В нашем случае создается правило для входящего трафика порта 3389, которое разрешает входящие подключения к удаленному рабочему столу. Мы также создадим правило входящего трафика для порта 80, разрешающее входящий веб-трафик.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
    -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location EastUS `
    -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Создайте сетевую карту для виртуальной машины. 
Создайте сетевую карту для виртуальной машины с помощью командлета [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). Сетевая карта подключает виртуальную машину к подсети, группе безопасности сети и общедоступному IP-адресу.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Создание виртуальной машины

Создайте конфигурацию виртуальной машины. Эта конфигурация содержит параметры, которые используются при развертывании виртуальной машины, в том числе образ виртуальной машины, ее размер и настройки аутентификации. При выполнении этого шага будут запрошены учетные данные. В качестве вводимых значений указываются имя пользователя и пароль для виртуальной машины.

```powershell
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 | `
    Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
```

Создайте виртуальную машину с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location EastUS -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>Подключение к виртуальной машине

После завершения развертывания создайте подключение к удаленному рабочему столу виртуальной машины.

Получите общедоступный IP-адрес виртуальной машины, выполнив команду [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Запишите этот IP-адрес. Он потребуется, чтобы подключиться к виртуальной машине в браузере и проверить возможность подключения к Интернету на следующем этапе.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Используйте следующую команду для создания сеанса удаленного рабочего стола с виртуальной машиной. Замените IP-адрес *общедоступным IP-адресом* виртуальной машины. При появлении запроса введите учетные данные, использованные при создании виртуальной машины.

```bash 
mstsc /v:<publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>Установка IIS с помощью PowerShell

После входа на виртуальную машину Azure вы можете установить IIS и включить локальное правило брандмауэра, разрешающее веб-трафик, с помощью одной строки кода PowerShell. Откройте командную строку PowerShell и выполните следующую команду:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Просмотр страницы приветствия IIS

Установив IIS и открыв через Интернет порт 80 на виртуальной машине, вы можете просмотреть страницу приветствия IIS по умолчанию в любом браузере. Чтобы перейти на страницу по умолчанию, используйте значение *publicIpAddress*, записанное ранее. 

![Сайт IIS по умолчанию](./media/quick-create-powershell/default-iis-website.png) 

## <a name="delete-virtual-machine"></a>Удаление виртуальной машины

Вы можете удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, выполнив команду [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали о том, как развернуть простую виртуальную машину, о правилах группы безопасности сети и об установке веб-сервера. Дополнительные сведения о виртуальных машинах Azure см. в руководстве по работе с виртуальными машинами Windows.

> [!div class="nextstepaction"]
> [Создание виртуальных машин Windows и управление ими с помощью модуля Azure PowerShell](./tutorial-manage-vm.md)

