---
title: "Преимущества гибридного использования Azure для Windows Server | Документация Майкрософт"
description: "Узнайте, как воспользоваться преимуществами программы Software Assurance для Windows Server, чтобы перенести свои локальные лицензии в Azure."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: georgem
translationtype: Human Translation
ms.sourcegitcommit: 7167048a287bee7c26cfc08775dcb84f9e7c2eed
ms.openlocfilehash: df86e73814ceb0c5137c654bce84c8d42ae41820


---
# <a name="azure-hybrid-use-benefit-for-windows-server"></a>Льгота на гибридное использование Azure для Windows Server
Льгота на гибридное использование Azure позволяет пользователям Windows Server с лицензией Software Assurance перенести свои локальные лицензии и развернуть виртуальные машины Windows Server в Azure с меньшими затратами. Кроме того, эта возможность позволяет платить за развернутые виртуальные машины Windows Server в Azure только по базовой ставке вычислений. Дополнительные сведения см. на странице [Льгота гибридного использования Microsoft Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). В этой статье описывается, как развернуть виртуальные машины Windows Server в Azure, чтобы воспользоваться льготой лицензирования.


## <a name="ways-to-use-azure-hybrid-use-benefit"></a>Применение преимуществ гибридного использования Azure
Существует несколько различных способов развертывания виртуальных машин Windows с преимуществами использования гибридного Azure.

1. Если у вас есть подписка по Соглашению Enterprise, то вы можете [развернуть специальные образы виртуальных машин из Marketplace](#deploy-a-vm-using-the-azure-marketplace), предварительно настроенные для применения преимуществ гибридного использования Azure.
2. Если вы на заключали Соглашение Enterprise, то вы можете [передать настраиваемую виртуальную машину](#upload-a-windows-server-vhd) и [развертывать ее с помощью шаблона Resource Manager](#deploy-a-vm-via-resource-manager) или [Azure PowerShell](#detailed-powershell-deployment-walkthrough).

## <a name="deploy-a-vm-using-the-azure-marketplace"></a>Развертывание виртуальной машины с помощью Azure Marketplace
Клиентам с [подписками по Соглашению Enterprise](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx) доступны образы из Marketplace, предварительно настроенные для применения преимуществ гибридного использования Azure. Эти образы можно развернуть непосредственно с портала Azure, а также с помощью Resource Manager или Azure PowerShell. Образы на сайте Marketplace обозначены меткой `[HUB]`, как показано ниже.

![Образы с преимуществами гибридного использования Azure в Azure Marketplace](./media/virtual-machines-windows-hybrid-use-benefit/ahub-images-portal.png)

Эти образы можно развернуть непосредственно с помощью портала Azure. Чтобы использовать их в шаблонах Resource Manager и Azure PowerShell, ознакомьтесь со списком образов ниже.

```powershell
Get-AzureRMVMImageSku -Location "West US" -Publisher "MicrosoftWindowsServer" `
    -Offer "WindowsServer-HUB"
```

Если у вас нет подписки по Соглашению Enterprise, продолжайте чтение, чтобы ознакомиться с инструкциями по передаче настраиваемой виртуальной машины и ее развертыванию с применением преимуществ гибридного использования Azure.


## <a name="upload-a-windows-server-vhd"></a>Отправка виртуального жесткого диска Windows Server
Чтобы развернуть виртуальную машину Windows Server в Azure, сначала необходимо создать виртуальный жесткий диск, в котором содержится базовая сборка Windows Server. Перед отправкой виртуального жесткого диска в Azure его необходимо соответствующим образом подготовить с помощью программы Sysprep. Вы можете [узнать больше о требованиях к VHD и использованию Sysprep](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) и ознакомиться с разделом [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Поддержка ролей сервера в Sysprep). Перед выполнением программы Sysprep выполните архивацию виртуальной машины. 

Убедитесь, что у вас [установлена и настроена последняя версия Azure PowerShell](/powershell/azureps-cmdlets-docs). Чтобы передать подготовленный виртуальный жесткий диск в учетную запись хранения Azure, выполните командлет `Add-AzureRmVhd` со следующими параметрами:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server, SharePoint Server и Dynamics могут использовать также ваше лицензирование Software Assurance. Еще вам нужно подготовить образ Windows Server. Для этого установите компоненты приложения, предоставьте соответствующие лицензионные ключи и выгрузите образ диска в Azure. Ознакомьтесь с соответствующей документацией, содержащей информацию о запуске Sysprep с помощью вашего приложения, например со статьей [Вопросы по установке SQL Server с помощью SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) или [Build a SharePoint Server 2016 Reference Image (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx) (Создание эталонного образа SharePoint Server 2016 (Sysprep)).
>
>

Кроме того, вы можете ознакомиться с дополнительной информацией о [передаче VHD в Azure](virtual-machines-windows-upload-image.md#upload-the-vhd-to-your-storage-account).


## <a name="deploy-an-uploaded-vm-via-resource-manager"></a>Развертывание переданной виртуальной машины с помощью Resource Manager
В шаблонах Resource Manager можно указывать дополнительный параметр для `licenseType`. Дополнительные сведения см. в статье [Создание шаблонов диспетчера ресурсов Azure](../azure-resource-manager/resource-group-authoring-templates.md). После загрузки виртуального жесткого диска в Azure необходимо изменить шаблон Resource Manager, чтобы включить в него тип лицензирования как часть поставщика вычислительных ресурсов и развернуть шаблон в обычном режиме.

```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   },
```


## <a name="deploy-an-uploaded-vm-via-powershell-quickstart"></a>Развертывание переданной виртуальной машины с помощью PowerShell
При развертывании виртуальной машины Windows Server с помощью PowerShell доступен дополнительный параметр для `-LicenseType`. После передачи виртуального жесткого диска в Azure необходимо создать новую виртуальную машину с помощью командлета `New-AzureRmVM` и указать тип лицензирования следующим образом.

```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

Вы можете [прочитать дополнительные сведения о развертывании виртуальной машины в Azure с помощью PowerShell](virtual-machines-windows-hybrid-use-benefit-licensing.md#detailed-powershell-deployment-walkthrough) ниже или просмотреть описание в руководстве [Создание виртуальной машины Windows с помощью Resource Manager и PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Проверка наличия льготы на гибридное использование Azure для Windows Server
После развертывания виртуальной машины с помощью Resource Manager или PowerShell проверьте тип лицензии с помощью командлета `Get-AzureRmVM` следующим образом.

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Вы должны увидеть результат, аналогичный приведенному ниже.

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Эти выходные данные отличаются от полученных при развертывании виртуальной машины без преимуществ гибридного использования Azure (например, при развертывании виртуальной машины непосредственно из коллекции Azure).

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="detailed-powershell-deployment-walkthrough"></a>Подробное пошаговое руководство по развертыванию с помощью PowerShell
В этом разделе показано, как выполнить полное развертывание виртуальной машины с помощью PowerShell. Дополнительные сведения о необходимых командлетах и создаваемых компонентах см. в статье [Создание виртуальной машины Windows с помощью Resource Manager и PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). В этом разделе описано, как создать группу ресурсов, учетную запись хранения, виртуальную сеть, а также, как определить и создать виртуальную машину.

Сначала выполните следующую команду, чтобы получить учетные данные, задать расположение и имя группы ресурсов.

```powershell
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "myResourceGroup"
```

Создайте общедоступный IP-адрес.

```powershell
$publicIPName = "myPublicIP"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName `
    -Location $location -AllocationMethod "Dynamic"
```

Определите подсеть, сетевой адаптер и виртуальную сеть.

```powershell
$subnetName = "mySubnet"
$nicName = "myNIC"
$vnetName = "myVnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location `
    -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

Присвойте имя виртуальной машине и настройте ее конфигурацию.

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

Определите операционную систему.

```powershell
$computerName = "myVM"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
```

Добавьте сетевой адаптер в виртуальную машину.

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Выберите учетную запись хранения, которая будет использоваться.

```powershell
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName mystorageaccount
```

Отправьте подготовленный виртуальный жесткий диск в Azure и подключите его к виртуальной машине.

```powershell
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/vhd/myvhd.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage `
    -SourceImageUri $urlOfUploadedImageVhd -Windows
```

Наконец, создайте виртуальную машину и определите тип лицензии для использования льготы на гибридное использование Azure.

```powershell
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType "Windows_Server"
```

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше о [льготе на гибридное использование Microsoft Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Узнайте больше об [использовании шаблонов Resource Manager](../azure-resource-manager/resource-group-overview.md).



<!--HONumber=Jan17_HO1-->


