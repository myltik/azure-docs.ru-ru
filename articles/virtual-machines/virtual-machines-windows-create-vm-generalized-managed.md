---
title: "Создание виртуальной машины из управляемого образа виртуальной машины в Azure | Документация Майкрософт"
description: "Создание виртуальной машины Windows из универсального управляемого образа виртуальной машины с использованием Azure PowerShell в модели развертывания с помощью Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/7/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 204fa369dd6db618ec5340317188681b0a2988e3
ms.openlocfilehash: f6fdbfcccd0bb44643d79ce866a3fa96410b298e
ms.lasthandoff: 02/11/2017


---
# <a name="create-a-vm-from-a-generalized-managed-vm-image"></a>Создание виртуальной машины из универсального управляемого образа виртуальной машины

Можно создать несколько виртуальных машин из управляемого образа виртуальной машины в Azure. Управляемый образ виртуальной машины содержит сведения, необходимые для создания виртуальной машины, включая диски ОС и диски данных. Виртуальные жесткие диски, входящие в образ, включая диски ОС и диски данных, хранятся в виде управляемых дисков. 

Из универсальной виртуальной машины удалены все данные вашей личной учетной записи с помощью [Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Чтобы создать универсальную виртуальную машину, запустите Sysprep на локальной виртуальной машине, а затем [передайте виртуальный жесткий диск в Azure](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Можно также запустить Sysprep на существующей виртуальной машине Azure, а затем [записать образ виртуальной машины](virtual-machines-windows-capture-image-resource.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="prerequisites"></a>Предварительные требования

Для создания новой виртуальной машины необходим [управляемый образ виртуальной машины](virtual-machines-windows-capture-image-resource.md). 

Убедитесь, что у вас установлена последняя версия модуля PowerShell AzureRM.Compute. Выполните следующую команду, чтобы установить ее.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Дополнительные сведения см. в разделе [об управлении версиями Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning).



## <a name="collect-information-about-the-image"></a>Сбор сведений об образе

Сначала необходимо собрать основные сведения об образе и создать для него переменную. В этом примере используется управляемый образ виртуальной машины **myImage**, находящийся в группе ресурсов **myResourceGroup** в расположении **Западно-центральная часть США**. 

```powershell
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

## <a name="create-a-virtual-network"></a>Создать виртуальную сеть
Создайте виртуальную сеть и подсеть [виртуальной сети](../virtual-network/virtual-networks-overview.md).

1. Создание подсети. В этом примере создается подсеть **mySubnet** с префиксом адреса **10.0.0.0/24**.  
   
    ```powershell
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Создание виртуальной сети. В этом примере создается виртуальная сеть **myVnet** с префиксом адреса **10.0.0.0/16**.  
   
    ```powershell
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>Создание общедоступного IP-адреса и сетевого интерфейса

Чтобы обеспечить обмен данными с виртуальной машиной в виртуальной сети, требуются [общедоступный IP-адрес](../virtual-network/virtual-network-ip-addresses-overview-arm.md) и сетевой интерфейс.

1. Создание общедоступного IP-адреса. В этом примере создается общедоступный IP-адрес с именем **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Создание сетевой карты. В этом примере создается сетевая карта с именем **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Создание группы безопасности сети и правила RDP

Чтобы войти на виртуальную машину с помощью RDP, необходимо настроить группу безопасности сети, которая разрешает доступ по протоколу RDP через порт 3389. 

В этом примере создается группа безопасности сети с именем **myNsg**, которая содержит правило с именем **myRdpRule**, разрешающее трафик RDP через порт 3389. Дополнительные сведения о группах безопасности сети см. в статье [Открытие портов для виртуальной машины в Azure с помощью PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>Создание переменной для виртуальной сети

Создайте переменную для готовой виртуальной сети. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

## <a name="get-the-credentials-for-the-vm"></a>Получение учетных данных для виртуальной машины

Следующий командлет откроет окно, в котором необходимо ввести новое имя пользователя и пароль, используемые в качестве учетной записи локального администратора для удаленного доступа к виртуальной машине. 

```powershell
$cred = Get-Credential
```

## <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>Задание переменных для имени виртуальной машины, имени компьютера и размера виртуальной машины

1. Создайте переменные для имени виртуальной машины и компьютера. В этом примере имя виртуальной машины задается как **myVM**, а имя компьютера — как **myComputer**.

    ```powershell
    $vmName = "myVM"
    $computerName = "myComputer"
    ```
2. Задайте размер виртуальной машины. В этом примере создается виртуальная машина размера **Standard_DS1_v2**. Дополнительные сведения см. в [документации по размерам виртуальных машин](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/).

    ```powershell
    $vmSize = "Standard_DS1_v2"
    ```

3. Добавьте имя и размер виртуальной машины в конфигурацию виртуальной машины.

```powershell
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

## <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>Настройка образа виртуальной машины в качестве исходного образа для новой виртуальной машины

Настройте исходный образ, используя идентификатор управляемого образа виртуальной машины.

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

## <a name="set-the-os-configuration-and-add-the-nic"></a>Настройте конфигурацию операционной системы и добавьте сетевую карту.

Введите тип хранилища (PremiumLRS или StandardLRS) и размер диска операционной системы. В этом примере для типа учетной записи задается значение **PremiumLRS**, для размера диска — **128 ГБ**, а для кэширования диска — **ReadWrite**.

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm  -StorageAccountType PremiumLRS -DiskSizeInGB 128 `
-CreateOption FromImage -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

## <a name="create-the-vm"></a>Создание виртуальной машины

Создайте виртуальную машину с использованием конфигурации, созданной и хранимой в переменной **$vm**.

```powershell
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

## <a name="verify-that-the-vm-was-created"></a>Проверка создания виртуальной машины
После завершения процесса новая виртуальная машина должна отображаться на [портале Azure](https://portal.azure.com) (выберите элементы **Обзор** > **Виртуальные машины**). Ее можно также увидеть, выполнив следующие команды PowerShell.

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Дальнейшие действия
Сведения об управлении созданной виртуальной машиной с помощью Azure PowerShell см. в статье [Управление виртуальными машинами Azure с помощью Azure Resource Manager и PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


