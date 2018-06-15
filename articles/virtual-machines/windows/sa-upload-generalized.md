---
title: Отправка универсального виртуального жесткого диска для создания нескольких виртуальных машин в Azure | Документы Майкрософт
description: Отправка универсального виртуального жесткого диска в учетную запись хранения Azure для создания виртуальной машины Windows, которая будет использоваться в модели развертывания Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: be2ec6df33f5756dc080195bfad32e0c9079453c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31603206"
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Отправка универсального виртуального жесткого диска в Azure для создания новой виртуальной машины

В этом разделе рассматривается отправка универсального неуправляемого диска в учетную запись хранения и создание виртуальной машины на основе отправленного диска. Из универсального образа VHD удалены все сведения вашей личной учетной записи с помощью Sysprep. 

Если необходимо создать виртуальную машину из специализированного VHD, расположенного в учетной записи хранения, ознакомьтесь с разделом [Создание виртуальной машины на основе специализированного VHD](sa-create-vm-specialized.md).

В этой статье описывается использование учетных записей хранения, однако мы рекомендуем клиентам использовать вместо них управляемые диски. Полные пошаговые инструкции по подготовке, передаче и созданию виртуальной машины с помощью управляемых дисков приведены в разделе [Создание виртуальной машины на основе универсального диска VHD, переданного в Azure, с использованием управляемых дисков](upload-generalized-managed.md).



## <a name="prepare-the-vm"></a>Подготовка виртуальной машины

Из универсального VHD удалены все данные вашей личной учетной записи с помощью Sysprep. Если вы планируете использовать виртуальный жесткий диск в качестве образа для создания виртуальных машин, то выполните следующие действия:
  
  * [Подготовьте виртуальный жесткий диск Windows к передаче в Azure](prepare-for-upload-vhd-image.md). 
  * Подготовка виртуальной машины к использованию с помощью Sysprep

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Обобщение виртуальной машины Windows с помощью Sysprep
В этом разделе содержатся сведения о том, как обобщить виртуальную машину Windows для ее дальнейшего использования в качестве образа. Помимо прочих действий Sysprep удаляет все сведения о вашей учетной записи и подготавливает машину к использованию в качестве образа. Сведения о Sysprep см. в статье [Использование программы Sysprep: введение](http://technet.microsoft.com/library/bb457073.aspx).

Убедитесь, что Sysprep поддерживает роли сервера, запущенные на компьютере. Дополнительные сведения см. в статье [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Поддержка серверных ролей в Sysprep).

> [!IMPORTANT]
> Если вы еще не отправили виртуальный жесткий диск в Azure и собираетесь запустить Sysprep первый раз, прежде чем это делать, [подготовьте виртуальную машину](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Выполните вход на виртуальную машину Windows.
2. Откройте окно командной строки с правами администратора. Измените каталог на **%windir%\system32\sysprep** и запустите файл `sysprep.exe`.
3. В диалоговом окне **Программа подготовки системы** выберите **Переход в окно приветствия системы (OOBE)** и убедитесь, что установлен флажок **Подготовка к использованию**.
4. В разделе **Параметры завершения работы** выберите **Завершение работы**.
5. Последовательно выберите **ОК**.
   
    ![Запуск Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. После выполнения всех необходимых действий Sysprep завершает работу виртуальной машины. 

> [!IMPORTANT]
> Не перезапускайте виртуальную машину до завершения передачи VHD в Azure или создания образа виртуальной машины. Если виртуальная машина случайно будет перезапущена, запустите инструмент Sysprep, чтобы повторно подготовить ее.
> 
> 


## <a name="upload-the-vhd"></a>Отправка виртуального жесткого диска

Отправьте виртуальный жесткий диск в учетную запись хранения Azure.

### <a name="log-in-to-azure"></a>Вход в Azure
Если вы еще не установили PowerShell версии 1.4 или выше, то см. статью [How to install and configure Azure PowerShell](/powershell/azure/overview) (Установка и настройка Azure PowerShell).

1. Откройте Azure PowerShell и войдите в свою учетную запись Azure. Откроется всплывающее окно для ввода данных учетной записи Azure.
   
    ```powershell
    Connect-AzureRmAccount
    ```
2. Получите идентификаторы доступных вам подписок.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Укажите соответствующую подписку с помощью идентификатора. Замените `<subscriptionID>` идентификатором правильной подписки.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>Получение учетной записи хранения
Вам необходима учетная запись хранения Azure для хранения переданного образа виртуальной машины. Можно использовать существующую учетную запись хранения или создать новую. 

Чтобы отобразить список доступных учетных записей хранения, введите:

```powershell
Get-AzureRmStorageAccount
```

Если вы хотите использовать существующую учетную запись хранения, то перейдите к разделу [Отправка образа виртуальной машины](#upload-the-vm-vhd-to-your-storage-account).

Если требуется создать учетную запись хранения, то выполните описанные ниже действия.

1. Необходимо указать имя группы ресурсов, в которой будет создана учетная запись хранения. Чтобы найти все группы ресурсов, существующие в вашей подписке, введите:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Чтобы создать группу ресурсов с именем **myResourceGroup** в регионе **Западная часть США**, введите:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. С помощью командлета [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) создайте в этой группе ресурсов учетную запись хранения с именем **mystorageaccount**:
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>Запуск отправки 

Используйте командлет [Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd), чтобы передать образ в контейнер в учетной записи хранения. В этом примере файл **myVHD.vhd** передается из расположения `"C:\Users\Public\Documents\Virtual hard disks\"` в учетную запись хранения **mystorageaccount**, входящую в группу ресурсов **myResourceGroup**. Файл будет помещен в контейнер с именем **mycontainer**; новое имя файла — **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


В случае успешного выполнения отобразится ответ, который выглядит следующим образом.

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

В зависимости от сетевого подключения и размера VHD-файла выполнение этой команды может занять некоторое время.


## <a name="create-a-new-vm"></a>Создание виртуальной машины 

Теперь отправленный виртуальный жесткий диск можно использовать для создания новой виртуальной машины. 

### <a name="set-the-uri-of-the-vhd"></a>Указание универсального кода ресурса (URI) диска VHD

Код URI, который должен использовать диск VHD, имеет такой формат: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd. В этом примере диск VHD с именем **myVHD** находится в учетной записи хранения **mystorageaccount** в контейнере **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Создать виртуальную сеть
Создайте виртуальную сеть и подсеть [виртуальной сети](../../virtual-network/virtual-networks-overview.md).

1. Создание подсети. В следующем примере создается подсеть с именем **mySubnet** в группе ресурсов **myResourceGroup** с префикс адреса **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Создание виртуальной сети. В следующем примере создается виртуальная сеть с именем **myVnet** в расположении **Запад США** с префиксом адреса **10.0.0.0/16**.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Создание общедоступного IP-адреса и сетевого интерфейса
Чтобы обеспечить обмен данными с виртуальной машиной в виртуальной сети, требуются [общедоступный IP-адрес](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) и сетевой интерфейс.

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

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Создание группы безопасности сети и правила RDP
Чтобы войти на виртуальную машину с помощью RDP, необходимо настроить правило безопасности, которое разрешает доступ RDP через порт 3389. 

В этом примере создается группа безопасности сети с именем **myNsg**, которая содержит правило с именем **myRdpRule**, разрешающее трафик RDP через порт 3389. Дополнительные сведения о группах безопасности сети см. в статье [Открытие портов для виртуальной машины в Azure с помощью PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Создание переменной для виртуальной сети
Создайте переменную для готовой виртуальной сети. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Создание виртуальной машины
Следующий сценарий PowerShell предназначен для настройки конфигураций виртуальных машин и использования переданного образа виртуальной машины в качестве источника новой установки.



```powershell
# Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Проверка создания виртуальной машины
После завершения процесса новая виртуальная машина должна отображаться на [портале Azure](https://portal.azure.com) (выберите элементы **Обзор** > **Виртуальные машины**). Ее можно также увидеть, выполнив следующие команды PowerShell.

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Дополнительная информация
Сведения об управлении созданной виртуальной машиной с помощью Azure PowerShell см. в статье [Управление виртуальными машинами Azure с помощью Azure Resource Manager и PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


