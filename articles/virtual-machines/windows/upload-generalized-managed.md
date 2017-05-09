---
title: "Создание управляемой виртуальной машины Azure на основе универсальных локальных дисков VHD | Документация Майкрософт"
description: "Создание управляемой виртуальной машины в Azure с помощью дисков VHD, переданных из локальной среды, и использование Управляемых дисков в модели развертывания Resource Manager."
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
ms.date: 02/08/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: d5233e71863a47988ab3458ba15dba7b53025db9
ms.contentlocale: ru-ru
ms.lasthandoff: 04/27/2017


---

# <a name="create-a-new-vm-from-a-generalized-vhd-uploaded-to-azure-using-managed-disks"></a>Создание виртуальной машины на основе универсального диска VHD, переданного в Azure, с использованием Управляемых дисков

Вы можете создать виртуальную машину в Azure, передав диск VHD, экспортированный из локального инструмента виртуализации или из другого облака. При использовании [Управляемых дисков](../../storage/storage-managed-disks-overview.md) для новой виртуальной машины упрощается управление виртуальными машинами и обеспечивается более высокий уровень доступности виртуальной машины, размещенной в группе доступности. При отправке диска VHD, который будет использоваться для создания нескольких виртуальных машин Azure, необходимо сначала сделать его универсальным с помощью программы [Sysprep](generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Служебная программа Sysprep удаляет все сведения о компьютере и учетной записи с VHD. 

Управляемые диски Azure избавляют от необходимости управлять [учетными записями хранения](../../storage/storage-introduction.md) для виртуальных машин Azure. Вам достаточно выбрать уровень [Премиум](../../storage/storage-premium-storage-performance.md) или [Стандартный](../../storage/storage-standard-storage.md) и размер диска. Azure самостоятельно создаст диск и будет управлять им. 


> [!IMPORTANT]
> Прежде чем начать миграцию на [Управляемые диски](../../storage/storage-managed-disks-overview.md), внимательно изучите [этот раздел](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).
>
> Прежде чем передавать в Azure диск VHD любого типа, следует выполнить инструкции из статьи [Подготовка диска VHD или VHDX для Windows к отправке в Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

## <a name="before-you-begin"></a>Перед началом работы
Если вы используете PowerShell, убедитесь, что у вас установлена последняя версия модуля PowerShell AzureRM.Compute. Выполните следующую команду, чтобы установить ее.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Дополнительные сведения см. в разделе [об управлении версиями Azure PowerShell](/powershell/azure/overview).


## <a name="generalize-the-windows-vm-using-sysprep"></a>Подготовка виртуальной машины Windows к использованию с помощью Sysprep

Помимо прочих действий Sysprep удаляет все сведения о вашей учетной записи и подготавливает машину к использованию в качестве образа. Сведения о Sysprep см. в статье [Использование программы Sysprep: введение](http://technet.microsoft.com/library/bb457073.aspx).

Убедитесь, что Sysprep поддерживает роли сервера, запущенные на компьютере. Дополнительные сведения см. в статье [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Поддержка серверных ролей в Sysprep).

> [!IMPORTANT]
> Если вы еще не отправили виртуальный жесткий диск в Azure и собираетесь запустить Sysprep первый раз, прежде чем это делать, [подготовьте виртуальную машину](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Выполните вход на виртуальную машину Windows.
2. Откройте окно командной строки с правами администратора. Измените каталог на **%windir%\system32\sysprep** и запустите файл `sysprep.exe`.
3. В диалоговом окне **Программа подготовки системы** выберите **Переход в окно приветствия системы (OOBE)** и убедитесь, что установлен флажок **Подготовка к использованию**.
4. В разделе **Параметры завершения работы** выберите **Завершение работы**.
5. Нажмите кнопку **ОК**.
   
    ![Запуск Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. После выполнения всех необходимых действий Sysprep завершает работу виртуальной машины. Не перезапускайте виртуальную машину.



## <a name="log-in-to-azure"></a>Вход в Azure
Если вы еще не установили PowerShell версии 1.4 или выше, то см. статью [How to install and configure Azure PowerShell](/powershell/azure/overview) (Установка и настройка Azure PowerShell).

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

2. С помощью командлета [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) создайте в этой группе ресурсов учетную запись хранения с именем **mystorageaccount**:
   
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

## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Создание управляемого образа на основе переданного виртуального жесткого диска 

Создайте управляемый образ с помощью универсального виртуального жесткого диска ОС.


1.  Сначала задайте общие параметры.

    ```powershell
    $rgName = "myResourceGroupName"
    $vmName = "myVM"
    $location = "West Central US" 
    $imageName = "yourImageName"
    $osVhdUri = "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```

4.  Создайте образ с помощью универсального диска VHD операционной системы.

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

## <a name="setup-some-variables-for-the-image"></a>Задание переменных для образа

Сначала необходимо собрать основные сведения об образе и создать для него переменную. В этом примере используется управляемый образ виртуальной машины **myImage**, находящийся в группе ресурсов **myResourceGroup** в расположении **Западно-центральная часть США**. 

```powershell
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

## <a name="create-a-virtual-network"></a>Создать виртуальную сеть
Создайте виртуальную сеть и подсеть [виртуальной сети](../../virtual-network/virtual-networks-overview.md).

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

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Создание группы безопасности сети и правила RDP

Чтобы войти на виртуальную машину с помощью RDP, необходимо настроить группу безопасности сети, которая разрешает доступ по протоколу RDP через порт 3389. 

В этом примере создается группа безопасности сети с именем **myNsg**, которая содержит правило с именем **myRdpRule**, разрешающее трафик RDP через порт 3389. Дополнительные сведения о группах безопасности сети см. в статье [Открытие портов для виртуальной машины в Azure с помощью PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

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
$vm = Set-AzureRmVMOSDisk -VM $vm  -ManagedDiskStorageAccountType PremiumLRS -DiskSizeInGB 128 `
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

Чтобы войти на новую виртуальную машину, найдите ее на [портале](https://portal.azure.com), нажмите кнопку **Подключение**и откройте RDP-файл "Удаленный рабочий стол". Для входа на новую виртуальную машину используйте учетные данные для входа в новую виртуальную машину. Дополнительные сведения см. в статье [Как подключиться к виртуальной машине Azure под управлением Windows и войти на нее](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 


