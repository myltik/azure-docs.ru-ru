---
title: "Отправка специализированных дисков VHD в Azure, используемых для создания виртуальной машины | Документация Майкрософт"
description: "Отправьте специализированные диски VHD в Azure, используемые для создания виртуальной машины."
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
ms.date: 02/05/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 6349aadc17ec974750537223531a3cf4c82479ed
ms.lasthandoff: 03/31/2017


---

# <a name="how-to-upload-a-specialized-vhd-to-create-a-vm-in-azure"></a>Как передать специализированный виртуальный жесткий диск для создания виртуальной машины в Azure

На специализированном VHD сохраняются учетные записи пользователей, приложения и другие данные о состоянии исходной виртуальной машины. Вы можете передать специализированный VHD в Azure и с его помощью создать виртуальную машину, в которой используются Управляемые диски или неуправляемая учетная запись хранения. Мы рекомендуем использовать [Управляемые диски](../../storage/storage-managed-disks-overview.md), чтобы воспользоваться преимуществами, которые они обеспечивают: упрощенное управление и дополнительные функции.


> [!IMPORTANT]
> Прежде чем передавать в Azure диск VHD любого типа, следует выполнить инструкции из статьи [Подготовка диска VHD или VHDX для Windows к отправке в Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>


* Подробнее о ценах на различные размеры виртуальных машин см. в статье [Цены на виртуальные машины Linux](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows).
* Дополнительные сведения о ценах на хранилища Azure см. на [этой странице](https://azure.microsoft.com/pricing/details/storage/blobs/). 
* Доступность размеров виртуальных машин см. в статье [Доступность продуктов по регионам](https://azure.microsoft.com/regions/services/).
* Сведения об общих ограничениях виртуальных машин Azure см. в статье [Подписка Azure, границы, квоты и ограничения службы](../../azure-subscription-service-limits.md).

## <a name="before-you-begin"></a>Перед началом работы
Если вы используете PowerShell, убедитесь, что у вас установлена последняя версия модуля PowerShell AzureRM.Compute. Выполните следующую команду, чтобы установить ее.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Дополнительные сведения см. в разделе [об управлении версиями Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning).


## <a name="prepare-the-vm"></a>Подготовка виртуальной машины
 
Если вы планируете использовать специализированный диск VHD "как есть" для создания виртуальной машины, то необходимо выполнить следующие действия: 
  * Если вы собираетесь использовать Управляемые диски, ознакомьтесь с разделом [Планирование миграции на Управляемые диски](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).
  * [Подготовьте виртуальный жесткий диск Windows к передаче в Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Не выполняйте** подготовку виртуальной машины к использованию с помощью Sysprep.
  * Удалите все гостевые инструменты и агенты виртуализации, которые установлены на виртуальной машине (т. е. инструменты VMware).
  * Убедитесь, что виртуальная машина настроена на получение IP-адреса и параметров DNS через DHCP. Таким образом, сервер будет получать IP-адрес в виртуальной сети при запуске. 
  * Прежде чем продолжить, завершите работу виртуальной машины.

## <a name="log-in-to-azure"></a>Вход в Azure
Если вы еще не установили PowerShell версии 1.4 или выше, то см. статью [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Установка и настройка Azure PowerShell).

1. Откройте Azure PowerShell и войдите в свою учетную запись Azure. Откроется всплывающее окно для ввода данных учетной записи Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
2. Получите идентификаторы доступных вам подписок.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Укажите соответствующую подписку с помощью идентификатора. Замените `<subscriptionID>` идентификатором правильной подписки.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>Получение учетной записи хранения
Вам необходима учетная запись хранения Azure для хранения переданного образа виртуальной машины. Можно использовать существующую учетную запись хранения или создать новую. 

Если для создания управляемого диска для виртуальной машины будет использоваться виртуальный жесткий диск, то расположение учетной записи хранения должно совпадать с расположением, в котором будет создаваться виртуальная машина.

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

2. С помощью командлета [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) создайте в этой группе ресурсов учетную запись хранения с именем **mystorageaccount**:
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    Допустимые значения -SkuName перечислены ниже.
   
   * **Standard_LRS** — локально избыточное хранилище. 
   * **Standard_ZRS** — хранилище, избыточное в пределах зоны.
   * **Standard_GRS** — геоизбыточное хранилище. 
   * **Standard_RAGRS** — геоизбыточное хранилище с доступом только для чтения. 
   * **Premium_LRS** — локально избыточное хранилище уровня "Премиум". 

## <a name="upload-the-vhd-to-your-storage-account"></a>Передача виртуального жесткого диска в учетную запись хранения

Используйте командлет [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx), чтобы передать VHD в контейнер в учетной записи хранения. В этом примере файл **myVHD.vhd** передается из расположения `"C:\Users\Public\Documents\Virtual hard disks\"` в учетную запись хранения **mystorageaccount**, входящую в группу ресурсов **myResourceGroup**. Файл будет помещен в контейнер с именем **mycontainer**; новое имя файла — **myUploadedVHD.vhd**.

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

Сохраните путь **URI назначения** для использования в дальнейшем, если вы собираетесь создать управляемый диск или виртуальную машину с помощью переданного виртуального жесткого диска.

### <a name="other-options-for-uploading-a-vhd"></a>Другие возможности загрузки виртуального жесткого диска

Вы также можете передать виртуальный жесткий диск в учетную запись хранения с помощью одного из следующих инструментов:

-   [API копирования BLOB-объекта хранилища Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx)

-   [Обозреватель хранилищ Azure для передачи больших двоичных объектов](https://azurestorageexplorer.codeplex.com/)

-   [Справочник по API REST служб хранилища импорта и экспорта](https://msdn.microsoft.com/library/dn529096.aspx)

    Мы рекомендуем использовать службу импорта и экспорта, если предполагаемое время передачи больше 7 дней. Чтобы оценить предполагаемое время передачи на основе размера данных и средства передачи, используйте компонент [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html). 

    Импорт и экспорт можно использовать для копирования в учетную запись хранения класса Standard. Необходимо осуществить копирование из учетной записи хранения Standard в учетную запись хранения Premium с помощью схожего с AzCopy средства.

    
## <a name="create-the-subnet-and-vnet"></a>Создание виртуальной сети и подсети

Создайте виртуальную сеть и подсеть [виртуальной сети](../../virtual-network/virtual-networks-overview.md).

1. Создайте подсеть. В этом примере создается подсеть с именем **mySubnet** в группе ресурсов **myResourceGroup** и задается префикс адреса подсети **10.0.0.0/24**.
   
    ```powershell
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
    -SourceUri $urlOfUploadedImageVhd -ResourceGroupName $rgName
    ```

2. Добавьте в конфигурацию диск ОС. В этом примере для диска устанавливается размер **128 ГБ**, а управляемый диск подключается в качестве диска ОС **Windows**.
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -ManagedDiskStorageAccountType StandardLRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

(Необязательно.) Подключите дополнительные управляемые диски в качестве дисков данных. В этом случае предполагается, что управляемые диски данных созданы в соответствии с инструкциями, приведенными в статье [Создание управляемых дисков из неуправляемых дисков в учетной записи хранения](create-managed-disk-ps.md). 

```powershell
$vm = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
```


### <a name="option-2-attach-a-vhd-that-is-in-an-existing-storage-account"></a>Вариант 2. Подключение виртуального жесткого диска, находящегося в имеющейся учетной записи хранения

1. Задайте универсальный код ресурса (URI) виртуального жесткого диска, который необходимо использовать. В этом примере VHD-файл **myOsDisk.vhd** хранится в учетной записи **myStorageAccount** в контейнере **myContainer**.

    ```powershell
    $osDiskUri = $urlOfUploadedImageVhd
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
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Если команда будет выполнена успешно, вы увидите примерно такой результат.

```
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
Чтобы войти на новую виртуальную машину, найдите ее на [портале](https://portal.azure.com), нажмите кнопку **Подключение**и откройте RDP-файл "Удаленный рабочий стол". Для входа на новую виртуальную машину используйте учетные данные для входа в новую виртуальную машину. Дополнительные сведения см. в статье [Как подключиться к виртуальной машине Azure под управлением Windows и войти на нее](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

