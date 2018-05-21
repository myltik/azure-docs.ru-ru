---
title: Создание виртуальной машины Linux с помощью PowerShell в Azure Stack | Документация Майкрософт
description: Создание виртуальной машины Linux с использованием PowerShell в Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 03EE5929-4F05-47D7-B246-EA93D6FC47CD
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 1e2dbc6020dd317e96c4116811f8e3bf87680bfb
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-powershell-in-azure-stack"></a>Краткое руководство. Создание виртуальной машины с сервером Linux с помощью PowerShell в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Вы можете создать виртуальную машину Ubuntu Server 16.04 LTS с помощью Azure Stack PowerShell. Выполните описанные в этой статье действия, чтобы создать и использовать виртуальную машину.  В этой статье приведены инструкции, которые помогут вам:

* подключиться к виртуальной машине через удаленный клиент;
* установить веб-сервер NGINX и открыть его стандартную домашнюю страницу;
* очистить неиспользуемые ресурсы.

## <a name="prerequisites"></a>предварительным требованиям

* **Образ Linux в Azure Stack Marketplace**.

   По умолчанию Azure Stack Marketplace не содержит образ Linux. Обратитесь к оператору Azure Stack, чтобы он предоставил нужный образ **Ubuntu Server 16.04 LTS**. Оператор может добавить его, выполнив действия из статьи [Скачивание элементов Marketplace из Azure в Azure Stack](../azure-stack-download-azure-marketplace-item.md).

* Для создания ресурсов в Azure Stack и управления ими требуется определенная версия Azure PowerShell. Если модуль PowerShell не настроен для Azure Stack, войдите в [пакет средств разработки](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) (или внешний клиент на базе Windows в случае [подключения через VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)) и выполните действия по [установке](azure-stack-powershell-install.md) и [настройке](azure-stack-powershell-configure-user.md) PowerShell.

* В каталоге формата SSH вашего профиля пользователя Windows сохраняется открытый ключ SSH с именем id_rsa.pub. Дополнительные сведения о создании ключей SSH см. в статье [Как использовать ключи SSH с Windows в Azure](../../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов — это логический контейнер, в котором вы можете развертывать ресурсы Azure Stack и управлять ими. Из пакета средств разработки или интегрированной системы Azure Stack выполните следующий блок кода, чтобы создать группу ресурсов. В этом документе мы присвоили значения всем переменным. Вы можете использовать эти значения или присвоить другие.

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Создание ресурсов хранилища

Создайте учетную запись хранения, а затем контейнер хранилища для образа Ubuntu Server 16.04 LTS.

```powershell
# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

# Create a storage container to store the virtual machine image
$containerName = 'osdisks'
$container = New-AzureStorageContainer `
  -Name $containerName `
  -Permission Blob
```

## <a name="create-networking-resources"></a>Создание сетевых ресурсов

Создайте виртуальную сеть, подсеть и общедоступный IP-адрес. С помощью этих ресурсов вы сможете установить сетевое подключение к виртуальной машине.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"

```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Создайте группу безопасности сети и правило группы безопасности сети.

Группа безопасности сети обеспечивает защиту виртуальной машины с помощью правил для входящего и исходящего трафика. Создайте правило входящего трафика для порта 3389, чтобы разрешить входящие подключения к удаленному рабочему столу, и правило входящего трафика для порта 80, чтобы разрешить входящий веб-трафик.

```powershell
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleSSH  -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location local `
-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Создание сетевой карты для виртуальной машины

Сетевая карта подключает виртуальную машину к подсети, группе безопасности сети и общедоступному IP-адресу.

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

Создайте конфигурацию виртуальной машины. Эта конфигурация содержит параметры, которые используются при развертывании виртуальной машины, например образ виртуальной машины, ее размер и учетные данные пользователя.

```powershell
# Define a credential object.
$UserName='demouser'
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ($UserName, $securePassword)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_D1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
  -Version "latest"

$osDiskName = "OsDisk"
$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f `
  $StorageAccount.PrimaryEndpoints.Blob.ToString(),`
  $vmName.ToLower(), `
  $osDiskName

# Sets the operating system disk properties on a virtual machine.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -Name $osDiskName `
  -VhdUri $OsDiskUri `
  -CreateOption FromImage | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"

# Adds the SSH Key to the virtual machine
Add-AzureRmVMSshPublicKey -VM $VirtualMachine `
 -KeyData $sshPublicKey `
 -Path "/home/azureuser/.ssh/authorized_keys"

# Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-virtual-machine"></a>Подключение к виртуальной машине

Развернув виртуальную машину, настройте подключение по протоколу SSH к этой виртуальной машине. Получите общедоступный IP-адрес виртуальной машины, выполнив команду [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?view=azurermps-4.3.1).

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Чтобы подключиться к виртуальной машине, в клиентской системе, в которой установлен протокол SSH, выполните приведенную ниже команду. Если вы работаете в Windows, можно использовать [Putty](http://www.putty.org/) для создания подключения.

```
ssh <Public IP Address>
```

При появлении запроса введите имя пользователя azureuser. Если при создании ключей SSH вы указали парольную фразу, введите ее здесь.

## <a name="install-the-nginx-web-server"></a>Установка веб-сервера NGINX

Чтобы обновить источники пакетов и установить последнюю версию пакета NGINX, выполните следующий скрипт:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Просмотр страницы приветствия nginx

Теперь на виртуальной машине установлен NGINX и открыт порт 80, и вы можете открыть веб-сервер через общедоступный IP-адрес этой виртуальной машины. Откройте окно браузера и перейдите по адресу ```http://<public IP address>```.

![Страница приветствия веб-сервера NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Очистите ресурсы, которые вам больше не нужны. Используйте команду [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup?view=azurermps-4.3.1), чтобы удалить эти ресурсы. Чтобы удалить группу ресурсов и все ресурсы в ней, выполните следующую команду:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

С помощью этого краткого руководства вы развернули простую виртуальную машину Linux. Дополнительные сведения о виртуальных машинах Azure Stack вы найдете в [этих рекомендациях](azure-stack-vm-considerations.md).
