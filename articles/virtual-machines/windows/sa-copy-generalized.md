---
title: Создание неуправляемого образа универсальной виртуальной машины в Azure | Документы Майкрософт
description: Создание неуправляемого образа универсальной виртуальной машины Windows, который будет использоваться для создания нескольких копий виртуальной машины в Azure.
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
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: bacf59cd7be637110125a51b64c708e755a8958d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Создание неуправляемого образа виртуальной машины на основе виртуальной машины Azure

В этой статье описывается использование учетных записей хранения. Мы рекомендуем использовать управляемые диски и управляемые образы вместо учетной записи хранения. Дополнительные сведения см. в статье [Запись управляемого образа универсальной виртуальной машины в Azure](capture-image-resource.md).

В этой статье показано, как использовать Azure PowerShell для создания образа универсальной виртуальной машины Azure с помощью учетной записи хранения. Такой образ можно затем использовать для создания другой виртуальной машины. Этот образ включает в себя диск операционной системы и другие диски данных, присоединенные к виртуальной машине. Образ не включает ресурсы виртуальной сети, поэтому их придется настроить при создании виртуальной машины. 

## <a name="prerequisites"></a>предварительным требованиям
У вас должна быть установлена среда Azure PowerShell 1.0 или более поздней версии. Если вы еще не установили PowerShell, см. статью [Установка и настройка Azure PowerShell](/powershell/azure/overview).

## <a name="generalize-the-vm"></a>Подготовка виртуальной машины к использованию 
В этом разделе содержатся сведения о том, как обобщить виртуальную машину Windows для ее дальнейшего использования в качестве образа. При подготовке виртуальной машины удаляются все сведения о вашей учетной записи и выполняется настройка ВМ для использования в качестве образа. в статье [Использование программы Sysprep: введение](http://technet.microsoft.com/library/bb457073.aspx).

Убедитесь, что Sysprep поддерживает роли сервера, запущенные на компьютере. Дополнительные сведения см. в статье [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Поддержка серверных ролей в Sysprep).

> [!IMPORTANT]
> Если вы впервые отправляете виртуальный жесткий диск в Azure, перед запуском Sysprep [подготовьте виртуальную машину](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

Можно также подготовить виртуальную машину Linux к использованию с помощью `sudo waagent -deprovision+user`, а затем использовать PowerShell для ее записи. Сведения об использовании интерфейса командной строки для записи виртуальной машины см. в разделе [Как подготовить к работе и записать образ виртуальной машины Linux с помощью Azure CLI](../linux/capture-image.md).


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

## <a name="log-in-to-azure-powershell"></a>Вход в Azure PowerShell
1. Откройте Azure PowerShell и войдите в свою учетную запись Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
   
    Откроется всплывающее окно для ввода данных учетной записи Azure.
2. Получите идентификаторы доступных вам подписок.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Укажите соответствующую подписку с помощью идентификатора.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Отмена распределения для виртуальной машины и установка состояния "универсальный"
1. Отмените распределение ресурсов для виртуальной машины.
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    *Состояние* виртуальной машины на портале Azure изменится с **Остановлено** на **Остановлено (освобождено)**.
2. Теперь задайте для виртуальной машины состояние **Generalized**(Универсальная). 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Проверьте состояние виртуальной машины. В разделе **OSState/generalized** для этой виртуальной машины параметр **DisplayStatus** должен иметь значение **VM generalized**.  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Создание образа

Создайте неуправляемый образ виртуальной машины в целевом контейнере хранилища с помощью этой команды. Образ будет создан в той же учетной записи хранения, что и исходная виртуальная машина. Параметр `-Path` сохраняет копию шаблона JSON для исходной виртуальной машины на локальном компьютере. Параметр `-DestinationContainerName` содержит имя контейнера для хранения образов. Если такой контейнер не существует, он будет создан автоматически.
   
```powershell
Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
URL-адрес образа можно получить из шаблона файла JSON. Перейдите к разделу **resources** > **storageProfile** > **osDisk** > **image** > **uri**, чтобы получить полный путь к образу. URL-адрес образа выглядит так: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
Также вы также можете проверить URI на портале. Образ копируется в контейнер **system** в вашей учетной записи хранения. 

## <a name="create-a-vm-from-the-image"></a>Создание виртуальной машины на основе образа

Теперь вы можете создать одну или несколько виртуальных машин на основе неуправляемого образа.

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
Следующий сценарий PowerShell выполняет настройку виртуальных машин и использует неуправляемый образ виртуальной машины в качестве источника новой установки.

</br>

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

### <a name="verify-that-the-vm-was-created"></a>Проверка создания виртуальной машины
После завершения процесса новая виртуальная машина должна отображаться на [портале Azure](https://portal.azure.com) (выберите элементы **Обзор** > **Виртуальные машины**). Ее можно также увидеть, выполнив следующие команды PowerShell.

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Дополнительная информация
Сведения об управлении созданной виртуальной машиной с помощью Azure PowerShell см. в статье [Управление виртуальными машинами Azure с помощью Azure Resource Manager и PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


