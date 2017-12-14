---
title: "Создание виртуальной машины из управляемого образа в Azure | Документация Майкрософт"
description: "Создание виртуальной машины Windows из универсального управляемого образа с использованием Azure PowerShell или портала Azure в модели развертывания с помощью Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: cynthn
ms.openlocfilehash: d8986c71fd0300af385750af898d620e6d3e1f24
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2017
---
# <a name="create-a-vm-from-a-managed-image"></a>Создание виртуальной машины из управляемого образа

Вы можете создать несколько виртуальных машин из управляемого образа виртуальной машины, используя PowerShell или портал Azure. Управляемый образ виртуальной машины содержит сведения, необходимые для создания виртуальной машины, включая диски ОС и диски данных. Виртуальные жесткие диски, входящие в образ, включая диски ОС и диски данных, хранятся в виде управляемых дисков. 

Для создания новой виртуальной машины необходим [управляемый образ виртуальной машины](capture-image-resource.md). 

## <a name="use-the-portal"></a>Использование портала

1. Откройте [портал Azure](https://portal.azure.com).
2. В меню слева выберите **Все ресурсы**. Вы можете отсортировать ресурсы по **типу**, чтобы легко найти образы.
3. Выберите в списке необходимый образ. Откроется страница **Обзор** для образа.
4. В меню нажмите кнопку **+ Создать виртуальную машину**.
5. Введите сведения о виртуальной машине. Имя пользователя и пароль понадобятся для входа на виртуальную машину. По завершении нажмите кнопку **ОК**. Вы можете создать виртуальную машину в существующей группе ресурсов или выбрать **Создать**, чтобы создать группу ресурсов для хранения данных виртуальной машины.
6. Выберите размер виртуальной машины. Чтобы просмотреть дополнительные размеры, выберите **Просмотреть все** или измените фильтр **Supported disk type** (Поддерживаемые типы диска). 
7. Внесите необходимые изменения в разделе **Параметры** и нажмите кнопку **ОК**. 
8. На странице сводных данных вы увидите имя образа в строке **Частный образ**. Нажмите кнопку **ОК**, чтобы запустить развертывание виртуальной машины.


## <a name="use-powershell"></a>Использование PowerShell

### <a name="prerequisites"></a>Предварительные требования

Убедитесь, что у вас установлены последние версии модулей PowerShell AzureRM.Compute и AzureRM.Network PowerShell. Чтобы установить их, откройте командную строку PowerShell с правами администратора и выполните следующую команду.

```azurepowershell-interactive
Install-Module AzureRM.Compute,AzureRM.Network
```
Дополнительные сведения см. в разделе [об управлении версиями Azure PowerShell](/powershell/azure/overview).



### <a name="collect-information-about-the-image"></a>Сбор сведений об образе

Сначала необходимо собрать основные сведения об образе и создать для него переменную. В этом примере используется управляемый образ виртуальной машины **myImage**, находящийся в группе ресурсов **myResourceGroup** в расположении **Западно-центральная часть США**. 

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

### <a name="create-a-virtual-network"></a>Создать виртуальную сеть
Создайте виртуальную сеть и подсеть [виртуальной сети](../../virtual-network/virtual-networks-overview.md).

Создание подсети. В этом примере создается подсеть **mySubnet** с префиксом адреса **10.0.0.0/24**.  
   
```azurepowershell-interactive
$subnetName = "mySubnet"
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name $subnetName -AddressPrefix 10.0.0.0/24
```

Создание виртуальной сети. В этом примере создается виртуальная сеть **myVnet** с префиксом адреса **10.0.0.0/16**.  
   
```azurepowershell-interactive
$vnetName = "myVnet"
$vnet = New-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $rgName `
    -Location $location `
    -AddressPrefix 10.0.0.0/16 `
    -Subnet $singleSubnet
```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Создание общедоступного IP-адреса и сетевого интерфейса

Чтобы обеспечить обмен данными с виртуальной машиной в виртуальной сети, требуются [общедоступный IP-адрес](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) и сетевой интерфейс.

Создание общедоступного IP-адреса. В этом примере создается общедоступный IP-адрес с именем **myPip**. 
   
```azurepowershell-interactive
$ipName = "myPip"
$pip = New-AzureRmPublicIpAddress `
    -Name $ipName `
    -ResourceGroupName $rgName `
    -Location $location `
    -AllocationMethod Dynamic
```
       
Создание сетевой карты. В этом примере создается сетевая карта с именем **myNic**. 
   
```azurepowershell-interactive
$nicName = "myNic"
$nic = New-AzureRmNetworkInterface `
    -Name $nicName `
    -ResourceGroupName $rgName `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id
```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Создание группы безопасности сети и правила RDP

Чтобы войти на виртуальную машину с помощью RDP, необходимо настроить группу безопасности сети, которая разрешает доступ по протоколу RDP через порт 3389. 

В этом примере создается группа безопасности сети с именем **myNsg**, которая содержит правило с именем **myRdpRule**, разрешающее трафик RDP через порт 3389. Дополнительные сведения о группах безопасности сети см. в статье [Открытие портов для виртуальной машины в Azure с помощью PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```azurepowershell-interactive
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Создание переменной для виртуальной сети

Создайте переменную для готовой виртуальной сети. 

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

### <a name="get-the-credentials-for-the-vm"></a>Получение учетных данных для виртуальной машины

Следующий командлет откроет окно, в котором необходимо ввести новое имя пользователя и пароль, используемые в качестве учетной записи локального администратора для удаленного доступа к виртуальной машине. 

```azurepowershell-interactive
$cred = Get-Credential
```

### <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>Задание переменных для имени виртуальной машины, имени компьютера и размера виртуальной машины

Создайте переменные для имени виртуальной машины и компьютера. В этом примере имя виртуальной машины задается как **myVM**, а имя компьютера — как **myComputer**.

```azurepowershell-interactive
$vmName = "myVM"
$computerName = "myComputer"
```

Задайте размер виртуальной машины. В этом примере создается виртуальная машина размера **Standard_DS1_v2**. Дополнительные сведения см. в [документации по размерам виртуальных машин](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/).

```azurepowershell-interactive
$vmSize = "Standard_DS1_v2"
```

Добавьте имя и размер виртуальной машины в конфигурацию виртуальной машины.

```azurepowershell-interactive
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

### <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>Настройка образа виртуальной машины в качестве исходного образа для новой виртуальной машины

Настройте исходный образ, используя идентификатор управляемого образа виртуальной машины.

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

### <a name="set-the-os-configuration-and-add-the-nic"></a>Настройте конфигурацию операционной системы и добавьте сетевую карту.

Введите тип хранилища (PremiumLRS или StandardLRS) и размер диска операционной системы. В этом примере для типа учетной записи задается значение **PremiumLRS**, для размера диска — **128 ГБ**, а для кэширования диска — **ReadWrite**.

```azurepowershell-interactive
$vm = Set-AzureRmVMOSDisk -VM $vm  `
    -StorageAccountType PremiumLRS `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

### <a name="create-the-vm"></a>Создание виртуальной машины

Создайте виртуальную машину с использованием конфигурации, созданной и хранимой в переменной **$vm**.

```azurepowershell-interactive
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

### <a name="verify-that-the-vm-was-created"></a>Проверка создания виртуальной машины
После завершения процесса новая виртуальная машина должна отображаться на [портале Azure](https://portal.azure.com) (выберите элементы **Обзор** > **Виртуальные машины**). Ее можно также увидеть, выполнив следующие команды PowerShell.

```azurepowershell-interactive
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Дальнейшие действия
Сведения об управлении новыми виртуальными машинами с помощью PowerShell см. в статье [Создание виртуальных машин Windows и управление ими с помощью модуля Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

