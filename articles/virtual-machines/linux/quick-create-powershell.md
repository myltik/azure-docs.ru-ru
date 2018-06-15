---
title: Краткое руководство. Создание виртуальной машины Linux с помощью Azure PowerShell | Документация Майкрософт
description: Из этого краткого руководства вы узнаете, как с помощью Azure PowerShell создать виртуальную машину Linux.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e910ced35738fcba27a8a1d7f2f010b2cd42e55d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32188996"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>Краткое руководство. Создание виртуальной машины Linux в Azure с помощью PowerShell

Модуль PowerShell используется для создания ресурсов Azure и управления ими с помощью командной строки PowerShell или сценариев. В этом кратком руководстве показано, как с помощью модуля Azure PowerShell развернуть в Azure виртуальную машину Linux под управлением Ubuntu. Чтобы проверить работу виртуальной машины, вы подключитесь к ней по протоколу SSH и установите веб-сервер NGINX.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально для работы с этим руководством, вам понадобится модуль Azure PowerShell 5.7.0 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzureRmAccount`, чтобы создать подключение к Azure.

Наконец, необходимо сохранить открытый ключ SSH *id_rsa.pub* в каталог *.ssh* профиля пользователя Windows. Дополнительные сведения о создании и использовании ключей SSH см. в статье [Использование ключей SSH с Windows в Azure](ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов Azure с помощью командлета [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>Создание ресурсов виртуальной сети

Создайте виртуальную сеть, подсеть и общедоступный IP-адрес. Эти ресурсы используются для того, чтобы установить сетевое подключение к виртуальной машине и подключить ее к Интернету.

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-Name "myVNET" -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

Создайте группу безопасности сети Azure и правило трафика. Группа безопасности сети защищает виртуальную машину с помощью правил для входящего и исходящего трафика. В следующем примере создается правило входящего подключения для TCP-порта 22, который используется для подключений по протоколу SSH. Чтобы разрешить входящий веб-трафик, также создается правило для входящего трафика по TCP-порту 80.

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRuleSSH"  -Protocol "Tcp" `
-Direction "Inbound" -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRuleWWW"  -Protocol "Tcp" `
-Direction "Inbound" -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access "Allow"

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-Name "myNetworkSecurityGroup" -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

Создайте виртуальный сетевой адаптер с помощью командлета [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface): Виртуальный сетевой адаптер подключает виртуальную машину к подсети, группе безопасности сети и общедоступному IP-адресу.

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name "myNic" -ResourceGroupName "myResourceGroup" -Location "EastUS" `
-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

Конфигурация виртуальной машины содержит параметры, которые используются при развертывании виртуальной машины, в том числе образ виртуальной машины, ее размер и параметры аутентификации. Задайте учетные данные для SSH, сведения об ОС и размер виртуальной машины следующим образом:

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_D1" | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName "myVM" -Credential $cred -DisablePasswordAuthentication | `
Set-AzureRmVMSourceImage -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS" -Version "latest" | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"
```

Теперь объедините описанные выше определения конфигурации в командлете создания [New AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

```azurepowershell-interactive
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location eastus -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>Подключение к виртуальной машине

Когда завершится развертывание, подключитесь к виртуальной машине по протоколу SSH. Чтобы проверить работу виртуальной машины, можно установить веб-сервер NGINX.

Чтобы узнать общедоступный IP-адрес виртуальной машины, выполните командлет [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress):

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Через клиент SSH подключитесь к виртуальной машине. Для этого можно открыть Azure Cloud Shell через веб-браузер или (в ОС Windows) [программу Putty](ssh-from-windows.md) или [подсистему Windows для Linux](/windows/wsl/install-win10). Предоставьте общедоступный IP-адрес своей виртуальной машины.

```bash
ssh azureuser@IpAddress
```

При появлении запроса на имя пользователя для входа введите *azureuser*. Если используется парольная фраза с ключами SSH, предоставьте ее в ответ на соответствующий запрос.

## <a name="install-web-server"></a>Установка веб-сервера

Чтобы проверить работу виртуальной машины, установите веб-сервер NGINX. Чтобы обновить источники пакетов и установить последнюю версию пакета NGINX, выполните следующие команды в сеансе SSH:

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

По завершении выполните команду `exit` в сеансе SSH.

## <a name="view-the-web-server-in-action"></a>Проверка работы веб-сервера

Установив NGINX и открыв через Интернет порт 80 на виртуальной машине, просмотрите страницу приветствия NGINX по умолчанию с помощью любого браузера. Используйте общедоступный IP-адрес виртуальной машины, полученный на предыдущем шаге. В следующем примере показан сайт NGINX по умолчанию:

![Сайт NGINX по умолчанию](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Когда группа ресурсов, виртуальная машина и все связанные с ней ресурсы станут ненужны, их можно удалить с помощью командлета [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Дополнительная информация

При работе с этим кратким руководство вы развернули простую виртуальную машину, создали группу безопасности сети и правило для нее, а также установили простой веб-сервер. Дополнительные сведения о виртуальных машинах Azure см. в руководстве для виртуальных машин Linux.

> [!div class="nextstepaction"]
> [Создание виртуальных машин Linux и управление ими с помощью Azure CLI](./tutorial-manage-vm.md)
