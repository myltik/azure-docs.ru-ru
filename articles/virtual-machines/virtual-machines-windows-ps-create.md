---
title: "Создание виртуальной машины в Azure с помощью PowerShell | Документация Майкрософт"
description: "При помощи Azure PowerShell и Azure Resource Manager можно с легкостью создать виртуальную машину под управлением Windows Server."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 14fe9ca9-e228-4d3b-a5d8-3101e9478f6e
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/21/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: 819b40302f158d1d6224878c164cf7ff71947887


---
# <a name="create-a-windows-vm-using-resource-manager-and-powershell"></a>Создание виртуальной машины Windows с помощью Resource Manager и PowerShell
В этой статье показано, как быстро создать виртуальную машину Azure под управлением Windows Server и связанные с ней ресурсы с помощью [Resource Manager](../azure-resource-manager/resource-group-overview.md) и PowerShell. 

Все действия, описанные в этой статье, необходимы для создания виртуальной машины. Их выполнение займет около 30 минут. Заменяйте значения параметров в командах значениями, соответствующими вашей среде.

## <a name="step-1-install-azure-powershell"></a>Шаг 1. Установка Azure PowerShell
Сведения об установке последней версии Azure PowerShell, а также о выборе нужной подписки и входе в учетную запись Azure см. в статье [Установка и настройка служб Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="step-2-create-a-resource-group"></a>Шаг 2. Создание группы ресурсов
Все ресурсы должны содержаться в группе ресурсов, поэтому ее нужно создать в первую очередь.  

1. Получите список доступных расположений, где можно создавать ресурсы.
   
    ```powershell
    Get-AzureRmLocation | sort Location | Select Location
    ```
2. Задайте расположение для ресурсов. Следующая команда задает расположение **centralus**.
   
    ```powershell
    $location = "centralus"
    ```
3. Создайте группу ресурсов. Следующая команда создает группу ресурсов с именем **myResourceGroup** в заданном расположении.
   
    ```powershell
    $myResourceGroup = "myResourceGroup"
    New-AzureRmResourceGroup -Name $myResourceGroup -Location $location
    ```

## <a name="step-3-create-a-storage-account"></a>Шаг 3. Создание учетной записи хранения
Для хранения файла виртуального жесткого диска, используемого создаваемой виртуальной машиной, необходима [учетная запись хранения](../storage/storage-introduction.md) . Имя учетной записи хранения должно содержать от 3 до 24 символов и состоять только из цифр и строчных букв.

1. Проверьте уникальность имени учетной записи хранения. Следующая команда проверяет имя **myStorageAccount**.
   
    ```powershell
    $myStorageAccountName = "mystorageaccount"
    Get-AzureRmStorageAccountNameAvailability $myStorageAccountName
    ```
   
    Если команда возвращает значение **True**, предложенное имя является уникальным в пределах Azure. 
2. Теперь создайте учетную запись хранения.
   
    ```powershell    
    $myStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $myResourceGroup `
        -Name $myStorageAccountName -SkuName "Standard_LRS" -Kind "Storage" -Location $location
    ```

## <a name="step-4-create-a-virtual-network"></a>Шаг 4. Создание виртуальной сети
Все виртуальные машины входят в [виртуальную сеть](../virtual-network/virtual-networks-overview.md).

1. Создайте подсеть для виртуальной сети. Следующая команда создает подсеть **mySubnet** с префиксом адреса 10.0.0.0/24.
   
    ```powershell
    $mySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 10.0.0.0/24
    ```
2. Теперь создайте виртуальную сеть. Эта команда создает виртуальную сеть с именем **myVnet** с помощью созданной ранее подсети и префикса адреса **10.0.0.0/16**.
   
    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName $myResourceGroup `
        -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $mySubnet
    ```

## <a name="step-5-create-a-public-ip-address-and-network-interface"></a>Шаг 5. Создание общедоступного IP-адреса и сетевого интерфейса
Чтобы обеспечить обмен данными с виртуальной машиной в виртуальной сети, требуются [общедоступный IP-адрес](../virtual-network/virtual-network-ip-addresses-overview-arm.md) и сетевой интерфейс.

1. Создайте общедоступный IP-адрес. Эта команда создает общедоступный IP-адрес с именем **myPublicIp** с **динамическим** методом распределения.
   
    ```powershell
    $myPublicIp = New-AzureRmPublicIpAddress -Name "myPublicIp" -ResourceGroupName $myResourceGroup `
        -Location $location -AllocationMethod Dynamic
    ```
2. Создайте сетевой интерфейс. Эта команда создает сетевой интерфейс с именем **myNIC**.
   
    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup `
        -Location $location -SubnetId $myVnet.Subnets[0].Id -PublicIpAddressId $myPublicIp.Id
    ```

## <a name="step-6-create-a-virtual-machine"></a>Шаг 6. Создание виртуальной машины
Теперь, когда все элементы готовы, можно приступать к созданию виртуальной машины.

1. Выполните следующую команду, чтобы задать имя учетной записи администратора и пароль для виртуальной машины.

    ```powershell
    $cred = Get-Credential -Message "Type the name and password of the local administrator account."
    ```
   
    Пароль должен содержать от 12 до 123 символов и включать по крайней мере одну строчную букву, одну прописную букву, одну цифру и один специальный знак. 
2. Создайте объект конфигурации для виртуальной машины. Следующая команда создает объект конфигурации с именем **myVmConfig**, определяющий имя и размер виртуальной машины.
   
    ```powershell
    $myVm = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
    ```
   
    Список доступных размеров виртуальных машин в Azure см. в [этой статье](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
3. Настройте параметры операционной системы для виртуальной машины. Следующая команда задает имя компьютера, тип операционной системы и учетные данные учетной записи для виртуальной машины.
   
    ```powershell
    $myVM = Set-AzureRmVMOperatingSystem -VM $myVM -Windows -ComputerName "myVM" -Credential $cred `
        -ProvisionVMAgent -EnableAutoUpdate
    ```
4. Определите образ, который будет использоваться для подготовки виртуальной машины. Следующая команда определяет образ Windows Server для виртуальной машины. 
   
    ```powershell
    $myVM = Set-AzureRmVMSourceImage -VM $myVM -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
    ```
   
    Дополнительные сведения о выборе образов, которые можно использовать, см. в статье [Просмотр и выбор образов виртуальных машин Windows в Azure с помощью оболочки PowerShell или интерфейса командной строки](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
5. Добавьте в конфигурацию созданный сетевой интерфейс.
   
    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```
6. Определите имя и расположение жесткого диска виртуальной машины. Файл виртуального жесткого диска сохраняется в контейнере. Следующая команда создает диск в контейнере с именем **vhds/myOsDisk1.vhd** в созданной учетной записи хранения.
   
    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```
7. Добавьте в конфигурацию виртуальной машины сведения о диске операционной системы. Замените значение **$diskName** именем диска операционной системы. Создайте переменную и добавьте в конфигурацию сведения о диске.
   
    ```powershell
    $myVM = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```
8. Наконец создайте виртуальную машину.
   
    ```powershell
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```

## <a name="next-steps"></a>Дальнейшие действия
* При наличии проблем с развертыванием ознакомьтесь с информацией об [устранении неполадок развертываний групп ресурсов с помощью портала Azure](../resource-manager-troubleshoot-deployments-portal.md).
* Узнайте, как управлять созданной виртуальной машиной, прочитав статью [Управление виртуальными машинами Azure с помощью Azure Resource Manager и PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Используйте преимущества шаблонов для создания виртуальной машины, ориентируясь на сведения в статье [Создание виртуальной машины Windows с использованием шаблона Resource Manager](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Dec16_HO2-->


