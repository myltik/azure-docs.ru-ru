---
title: "Создание виртуальной машины на основе специализированного диска в Azure | Документация Майкрософт"
description: "Создание новой виртуальной машины на основе подключенного специализированного управляемого или неуправляемого диска в модели развертывания с помощью Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: e9c9f440aa6554e30dc0974d1ab777a9bca50762
ms.lasthandoff: 03/31/2017


---
# <a name="create-a-vm-from-a-specialized-disk"></a>Создание виртуальной машины из специализированного диска

Создайте виртуальную машину, подключив специализированный диск в качестве диска ОС с помощью PowerShell. Специализированный диск — это копия виртуального жесткого диска виртуальной машины, в которой сохраняются учетные записи пользователей, приложения и другие данные о состоянии исходной виртуальной машины. Для создания виртуальной машины можно использовать специализированный [управляемый диск](../../storage/storage-managed-disks-overview.md) или специализированный неуправляемый диск.

## <a name="before-you-begin"></a>Перед началом работы
Если вы используете PowerShell, убедитесь, что у вас установлена последняя версия модуля PowerShell AzureRM.Compute. Выполните следующую команду, чтобы установить ее.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Дополнительные сведения см. в разделе [об управлении версиями Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning).


## <a name="create-the-subnet-and-vnet"></a>Создание виртуальной сети и подсети

Создайте виртуальную сеть и подсеть [виртуальной сети](../../virtual-network/virtual-networks-overview.md).

1. Создайте подсеть. В этом примере создается подсеть с именем **mySubnet** в группе ресурсов **myResourceGroup** и задается префикс адреса подсети **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Создайте виртуальную сеть. В этом примере задается имя виртуальной сети **myVnetName**, расположение **западная часть США** и префикс адреса виртуальной сети **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-nic"></a>Создание общедоступного IP-адреса и сетевой карты
Чтобы обеспечить обмен данными с виртуальной машиной в виртуальной сети, требуются [общедоступный IP-адрес](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) и сетевой интерфейс.

1. Создайте общедоступный IP-адрес. В этом примере открытому IP-адресу присвоено имя **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Создание сетевой карты. В этом примере сетевой карте присвоено имя **myNicName**.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Создание группы безопасности сети и правила RDP
Чтобы войти на виртуальную машину по протоколу удаленного рабочего стола, необходимо настроить правило безопасности, которое разрешает доступ по этому протоколу через порт 3389. Так как VHD для новой виртуальной машины создан на основе имеющейся специализированной виртуальной машины, то и существующую учетную запись (у которой есть разрешение на вход по протоколу удаленного рабочего стола) можно использовать для обеих этих виртуальных машин.
В этом примере для NSG задается имя **myNsg**, а для правила протокола удаленного рабочего стола — имя **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Дополнительные сведения о конечных точках и правилах NSG см. в статье [Открытие портов для виртуальной машины в Azure с помощью PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="set-the-vm-name-and-size"></a>Задание имени и размера виртуальной машины

В этом примере для имени виртуальной машины задается значение myVM, а для ее размера — Standard_A2.
```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

## <a name="add-the-nic"></a>Добавление сетевой карты
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
## <a name="configure-the-os-disk"></a>Настройка диска операционной системы

Специализированным диском ОС может быть виртуальный жесткий диск, [переданный в Azure](upload-image.md), или [копия виртуального жесткого диска из имеющейся виртуальной машины Azure](vhd-copy.md). 

Вы можете выбрать один из вариантов:
- **Вариант 1.** Создайте специализированный управляемый диск на основе специализированного виртуального жесткого диска в имеющейся учетной записи хранения, чтобы использовать его в качестве диска ОС.

или 

- **Вариант 2.** Используйте специализированный виртуальный жесткий диск, размещенный в вашей учетной записи хранения (неуправляемый диск). 

### <a name="option-1-create-a-managed-disk-from-an-unmanaged-specialized-disk"></a>Вариант 1. Создание управляемого диска на основе специализированного неуправляемого диска

1. Создайте управляемый диск на основе имеющегося специализированного виртуального жесткого диска в своей учетной записи хранения. В этом примере в качестве имени диска используется **myOSDisk1**, диск переносится в хранилище **StandardLRS**, а в качестве универсального кода ресурса (URI) для исходного виртуального жесткого диска используется **https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd**.

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName "myOSDisk1" -Disk (New-AzureRmDiskConfig `
    -AccountType StandardLRS  -Location $location -CreationDataCreateOption Import `
    -SourceUri https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd) `
    -ResourceGroupName $rgName
    ```

2. Добавьте в конфигурацию диск ОС. В этом примере для диска устанавливается размер **128 ГБ**, а управляемый диск подключается в качестве диска ОС **Windows**.
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType StandardLRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

(Необязательно.) Подключите дополнительные управляемые диски в качестве дисков данных. В этом случае предполагается, что управляемые диски данных созданы в соответствии с инструкциями, приведенными в статье [Создание управляемых дисков из неуправляемых дисков в учетной записи хранения](create-managed-disk-ps.md). 

```powershell
$vm = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
```


### <a name="option-2-attach-a-vhd-that-is-in-an-existing-storage-account"></a>Вариант 2. Подключение виртуального жесткого диска, находящегося в имеющейся учетной записи хранения

1. Задайте универсальный код ресурса (URI) виртуального жесткого диска, который необходимо использовать. В этом примере VHD-файл **myOsDisk.vhd** хранится в учетной записи **myStorageAccount** в контейнере **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Добавьте диск ОС с помощью URL-адреса скопированного виртуального жесткого диска ОС. В этом примере при создании диска ОС к имени виртуальной машины добавляется термин osDisk, формируя таким образом имя диска ОС. В этом примере также указывается, что этот виртуальный жесткий диск на платформе Windows должен быть подключен к виртуальной машине в качестве диска ОС.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

(Необязательно.) При наличии дисков данных, которые необходимо подключить к виртуальной машине, добавьте их, используя URL-адреса виртуальных жестких дисков данных и соответствующий логический номер устройства (LUN).

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

При использовании учетной записи хранения URL-адреса дисков данных и дисков ОС выглядят так: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Их можно узнать на портале. Для этого найдите целевой контейнер хранилища, щелкните скопированный виртуальный жесткий диск операционной системы или данных, а затем скопируйте содержимое URL-адреса.


## <a name="create-the-vm"></a>Создание виртуальной машины

Создайте виртуальную машину, используя созданную конфигурацию.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Если команда будет выполнена успешно, вы увидите примерно такой результат.

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

## <a name="verify-that-the-vm-was-created"></a>Проверка создания виртуальной машины
Созданная виртуальная машина должна отображаться на [портале Azure](https://portal.azure.com) (выберите **Обзор** > **Виртуальные машины**). Ее можно также увидеть, выполнив следующие команды PowerShell:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Дальнейшие действия
Чтобы войти на новую виртуальную машину, найдите ее на [портале](https://portal.azure.com), нажмите кнопку **Подключение**и откройте RDP-файл "Удаленный рабочий стол". Для входа на новую виртуальную машину используйте учетные данные для входа в новую виртуальную машину. Дополнительные сведения см. в статье [Как подключиться к виртуальной машине Azure под управлением Windows и войти на нее](connect-logon.md).


