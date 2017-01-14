---
title: "Создание масштабируемого набора виртуальных машин с помощью PowerShell | Документация Майкрософт"
description: "Создание набора масштабирования виртуальных машин с помощью PowerShell"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7bb03323-8bcc-4ee4-9a3e-144ca6d644e2
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/18/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 550db52c2b77ad651b4edad2922faf0f951df617
ms.openlocfilehash: 5abaa31828e624f77b6a9efb4496327977b483e4


---
# <a name="create-a-windows-virtual-machine-scale-set-using-azure-powershell"></a>Создание набора масштабирования виртуальных машин Windows с помощью Azure PowerShell
Материал в этих шагах для создания набора масштабирования виртуальных машин Azure представлен таким образом, что достаточно лишь заполнить пробелы. Дополнительные сведения о наборах масштабирования см. в статье [Обзор наборов для масштабирования виртуальных машин](virtual-machine-scale-sets-overview.md).

Процедура, описанная в этой статье, занимает около 30 минут.

## <a name="step-1-install-azure-powershell"></a>Шаг 1. Установка Azure PowerShell
Сведения об установке последней версии Azure PowerShell, а также о выборе нужной подписки и входе в учетную запись Azure см. в статье [Как установить и настроить службы Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="step-2-create-resources"></a>Шаг 2. Создание ресурсов
Создайте ресурсы, которые необходимы для вашего нового набора масштабирования.

### <a name="resource-group"></a>Группа ресурсов
Набор масштабирования виртуальных машин должен содержаться в группе ресурсов.

1. Получите список доступных расположений, в которых можно размещать ресурсы.
   
        Get-AzureLocation | Sort Name | Select Name
2. Выберите подходящее расположение, замените значение **$locName** именем этого расположения, а затем создайте переменную:
   
        $locName = "location name from the list, such as Central US"
3. Замените значение **$rgName** именем, которое вы хотите использовать для новой группы ресурсов, а затем создайте переменную: 
   
        $rgName = "resource group name"
4. Создайте группу ресурсов:
   
        New-AzureRmResourceGroup -Name $rgName -Location $locName
   
    Вы увидите нечто вроде этого примера:
   
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### <a name="storage-account"></a>Учетная запись хранения
Учетная запись хранения используется виртуальной машиной для хранения диска операционной системы и диагностических данных, используемых для масштабирования. По возможности рекомендуется иметь учетную запись хранения для каждой виртуальной машины, созданной в наборе масштабирования. Если это невозможно, планируйте не более 20 виртуальных машин на учетную запись хранения. Пример в этой статье демонстрирует создание трех учетных записей хранения для трех виртуальных машин.

1. Замените значение **$saName** именем учетной записи хранения. Проверьте имя на уникальность. 
   
        $saName = "storage account name"
        Get-AzureRmStorageAccountNameAvailability $saName
   
    Если команда возвращает значение **True**, предложенное имя является уникальным.
2. Замените значение **$saType** типом учетной записи хранения, а затем создайте переменную:  
   
        $saType = "storage account type"
   
    Возможные значения: Standard_LRS, Standard_GRS, Standard_RAGRS и Premium_LRS.
3. Создайте учетную запись:
   
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName
   
    Вы увидите нечто вроде этого примера:
   
        ResourceGroupName   : myrg1
        StorageAccountName  : myst1
        Id                  : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft
                              .Storage/storageAccounts/myst1
        Location            : centralus
        AccountType         : StandardLRS
        CreationTime        : 3/15/2016 4:51:52 PM
        CustomDomain        :
        LastGeoFailoverTime :
        PrimaryEndpoints    : Microsoft.Azure.Management.Storage.Models.Endpoints
        PrimaryLocation     : centralus
        ProvisioningState   : Succeeded
        SecondaryEndpoints  :
        SecondaryLocation   :
        StatusOfPrimary     : Available
        StatusOfSecondary   :
        Tags                : {}
        Context             : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext
4. Повторите шаги 1–4, чтобы создать три учетных записи хранения, например myst1, myst2 и myst3.

### <a name="virtual-network"></a>виртуальную сеть
Виртуальная сеть обязательна для виртуальных машин в наборе масштабирования.

1. Замените значение **$subnetName** именем, которое вы хотите использовать для подсети в виртуальной сети, а затем создайте переменную. 
   
        $subnetName = "subnet name"
2. Создайте конфигурацию подсети:
   
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   
    Префикс адреса может отличаться в вашей виртуальной сети.
3. Замените значение **$netName** именем, которое вы хотите использовать для виртуальной сети, а затем создайте переменную. 
   
        $netName = "virtual network name"
4. Создайте виртуальную сеть:
   
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="configuration-of-the-scale-set"></a>Конфигурации набора масштабирования
Теперь у нас есть все ресурсы, необходимые для конфигурации набора масштабирования, и мы можем приступить к ее созданию.  

1. Замените значение **$ipName** именем, которое вы хотите использовать для конфигурации IP, а затем создайте переменную. 
   
        $ipName = "IP configuration name"
2. Создайте конфигурацию IP:
   
        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id
3. Замените значение **$vmssConfig** именем, которое вы хотите использовать для конфигурации набора масштабирования, а затем создайте переменную.   
   
        $vmssConfig = "Scale set configuration name"
4. Создайте конфигурацию для набора масштабирования:
   
        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
   
    В этом примере показано создание набора масштабирования с тремя виртуальными машинами. Дополнительные сведения о возможностях наборов масштабирования см. в [обзоре наборов масштабирования виртуальных машин](virtual-machine-scale-sets-overview.md). Этот шаг также включает задание размера (параметр SkuName) виртуальных машин в наборе. Чтобы подобрать размер в соответствии со своими потребностями, обратитесь к статье [Размеры виртуальных машин](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
5. Добавьте конфигурацию сетевого интерфейса в конфигурацию набора масштабирования:
   
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
   
    Вы увидите нечто вроде этого примера:
   
        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     :
        OverProvision         :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags                  :

#### <a name="operating-system-profile"></a>Профиль операционной системы
1. Замените значение **$computerName** префиксом имени компьютера, который вы хотите использовать, а затем создайте переменную. 
   
        $computerName = "computer name prefix"
2. Замените значение **$adminName** именем учетной записи администратора на виртуальных машинах, а затем создайте переменную.
   
        $adminName = "administrator account name"
3. Замените значение **$adminPassword** паролем учетной записи, а затем создайте переменную.
   
        $adminPassword = "password for administrator accounts"
4. Создайте профиль операционной системы:
   
        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### <a name="storage-profile"></a>Профиль хранилища
1. Замените значение **$storageProfile** именем, которое вы хотите использовать для профиля хранилища, а затем создайте переменную.  
   
        $storageProfile = "storage profile name"
2. Создайте переменные, которые определяют используемый образ:  
   
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
   
    Сведения о других образах см. в разделе [Просмотр и выбор образов виртуальных машин Windows в Azure с помощью оболочки PowerShell или интерфейса командной строки](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
3. Замените значение **$vhdContainer** списком путей к расположениям, в которых хранятся виртуальные жесткие диски, например https://mystorage.blob.core.windows.net/vhds, а затем создайте переменную.
   
        $vhdContainers = @("https://myst1.blob.core.windows.net/vhds","https://myst2.blob.core.windows.net/vhds","https://myst3.blob.core.windows.net/vhds")
4. Создайте профиль хранилища:
   
        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storageProfile -VhdContainer $vhdContainers -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### <a name="virtual-machine-scale-set"></a>Набор масштабирования виртуальных машин
Наконец, мы можем создать набор масштабирования.

1. Замените значение **$vmssName** именем набора масштабирования виртуальных машин, а затем создайте переменную.
   
        $vmssName = "scale set name"
2. Создайте набор масштабирования:
   
        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss
   
    Если развертывание прошло успешно, вы увидите нечто вроде этого:
   
        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     : Updating
        OverProvision         :
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microso
                                ft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags                  :

## <a name="step-3-explore-resources"></a>Шаг 3. Изучение ресурсов
Для изучения созданного набора масштабирования виртуальных машин используйте указанные ниже ресурсы.

* Портал Azure — с помощью портала можно получить ограниченный объем сведений.
* [Обозреватель ресурсов Azure](https://resources.azure.com/) — это лучший инструмент для просмотра текущего состояния набора масштабирования.
* Azure PowerShell — используйте следующую команду, чтобы получить сведения:
  
        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
  
        Or 
  
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

## <a name="next-steps"></a>Дальнейшие действия
* Сведения об управлении созданным набором масштабирования можно найти в статье [Управление виртуальными машинами в наборе масштабирования виртуальных машин](virtual-machine-scale-sets-windows-manage.md)
* Вы можете настроить автоматическое масштабирование набора масштабирования с помощью сведений в статье [Автоматическое масштабирование и наборы масштабирования виртуальных машин](virtual-machine-scale-sets-autoscale-overview.md)
* Дополнительные сведения о вертикальном масштабировании см. в статье [Вертикальное автомасштабирование наборов масштабирования виртуальных машин](virtual-machine-scale-sets-vertical-scale-reprovision.md).




<!--HONumber=Dec16_HO1-->


