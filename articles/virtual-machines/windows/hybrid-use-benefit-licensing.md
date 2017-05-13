---
title: "Преимущества гибридного использования Azure для сервера Windows Server и клиента Windows | Документация Майкрософт"
description: "Узнайте, как воспользоваться преимуществами программы Software Assurance для Windows, чтобы перенести свои локальные лицензии в Azure."
services: virtual-machines-windows
documentationcenter: 
author: kmouss
manager: timlt
editor: 
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/1/2017
ms.author: kmouss
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 0854ceddc473a362221140f32b24138221a6f175
ms.contentlocale: ru-ru
ms.lasthandoff: 04/27/2017


---
# <a name="azure-hybrid-use-benefit-for-windows-server-and-windows-client"></a>Преимущества гибридного использования Azure для сервера Windows Server и клиента Windows.
Благодаря преимуществам гибридного использования Azure, клиенты, участвующие в программе Software Assurance, могут использовать локальные лицензии Windows Server и лицензии клиента Windows для запуска виртуальных машин Windows в Azure с меньшими затратами. Преимущество гибридного использования Azure для Windows Server распространяется на Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 и Windows Server 2016. Преимущество гибридного использования Azure для клиента Windows распространяется на Windows 10. Дополнительные сведения см. на странице [Льгота гибридного использования Microsoft Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

>[!IMPORTANT]
>В настоящее время преимущества гибридного использования Azure для клиента Windows находятся на этапе предварительной версии. Они доступны только клиентам уровня "Корпоративный", использующим Windows 10 Корпоративная E3 или Windows 10 Корпоративная E5 для каждого пользователя либо Windows VDA для каждого пользователя (лицензии на подписку пользователя или дополнительные лицензии на подписку пользователя) ("соответствующие лицензии").
>
>

## <a name="ways-to-use-azure-hybrid-use-benefit"></a>Применение преимуществ гибридного использования Azure
Существует несколько различных способов развертывания виртуальных машин Windows с преимуществами использования гибридного Azure.

1. Можно развернуть виртуальные машины на основе [определенных образов из Marketplace](#deploy-a-vm-using-the-azure-marketplace), для которых предварительно настроены преимущества гибридного использования Azure: Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 и Windows Server 2008 с пакетом обновления 1 (SP1).
2. Вы можете [передать настраиваемую виртуальную машину](#upload-a-windows-vhd) и [развернуть ее с помощью шаблона Resource Manager](#deploy-a-vm-via-resource-manager) или [Azure PowerShell](#detailed-powershell-deployment-walkthrough).

## <a name="deploy-a-vm-using-the-azure-marketplace"></a>Развертывание виртуальной машины с помощью Azure Marketplace
Ниже указаны образы из Marketplace, для которых предварительно настроены преимущества гибридного использования Azure: Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 и Windows Server 2008 с пакетом обновления 1 (SP1). Эти образы можно развернуть непосредственно с портала Azure, а также с помощью шаблонов Resource Manager или Azure PowerShell.

Эти образы можно развернуть непосредственно с помощью портала Azure. Чтобы использовать их в шаблонах Resource Manager и Azure PowerShell, ознакомьтесь со списком образов ниже.

Для Windows Server:
```powershell
Get-AzureRmVMImagesku -Location westus -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```
- 2016-Datacenter версии 2016.127.20170406 или выше;

- 2012-R2-Datacenter версии 4.127.20170406 или выше;

- 2012-Datacenter версии 3.127.20170406 или выше;

- 2008-R2-SP1 версии 2.127.20170406 или выше.

Для клиента Windows:
```powershell
Get-AzureRMVMImageSku -Location "West US" -Publisher "MicrosoftWindowsServer" `
    -Offer "Windows-HUB"
```

## <a name="upload-a-windows-vhd"></a>Передача VHD Windows
Чтобы развернуть виртуальную машину Windows в Azure, сначала необходимо создать виртуальный жесткий диск, содержащий базовую сборку Windows. Перед отправкой виртуального жесткого диска в Azure его необходимо соответствующим образом подготовить с помощью программы Sysprep. Вы можете [узнать больше о требованиях к VHD и использованию Sysprep](upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) и ознакомиться с разделом [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Поддержка ролей сервера в Sysprep). Перед выполнением программы Sysprep выполните архивацию виртуальной машины. 

Убедитесь, что у вас [установлена и настроена последняя версия Azure PowerShell](/powershell/azure/overview). Чтобы передать подготовленный виртуальный жесткий диск в учетную запись хранения Azure, выполните командлет `Add-AzureRmVhd` со следующими параметрами:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server, SharePoint Server и Dynamics могут использовать также ваше лицензирование Software Assurance. Еще вам нужно подготовить образ Windows Server. Для этого установите компоненты приложения, предоставьте соответствующие лицензионные ключи и выгрузите образ диска в Azure. Ознакомьтесь с соответствующей документацией, содержащей информацию о запуске Sysprep с помощью вашего приложения, например со статьей [Вопросы по установке SQL Server с помощью SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) или [Build a SharePoint Server 2016 Reference Image (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx) (Создание эталонного образа SharePoint Server 2016 (Sysprep)).
>
>

Кроме того, вы можете ознакомиться с дополнительной информацией о [передаче VHD в Azure](upload-image.md#upload-the-vhd-to-your-storage-account).


## <a name="deploy-an-uploaded-vm-via-resource-manager"></a>Развертывание переданной виртуальной машины с помощью Resource Manager
В шаблонах Resource Manager можно указывать дополнительный параметр для `licenseType`. Дополнительные сведения см. в статье [Создание шаблонов диспетчера ресурсов Azure](../../resource-group-authoring-templates.md). После загрузки виртуального жесткого диска в Azure необходимо изменить шаблон Resource Manager, чтобы включить в него тип лицензирования как часть поставщика вычислительных ресурсов и развернуть шаблон в обычном режиме.

Для Windows Server:
```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

Для клиента Windows:
```json
"properties": {  
   "licenseType": "Windows_Client",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-an-uploaded-vm-via-powershell-quickstart"></a>Развертывание переданной виртуальной машины с помощью PowerShell
При развертывании виртуальной машины Windows Server с помощью PowerShell доступен дополнительный параметр для `-LicenseType`. После передачи виртуального жесткого диска в Azure необходимо создать новую виртуальную машину с помощью командлета `New-AzureRmVM` и указать тип лицензирования следующим образом.

Для Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

Для клиента Windows:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

Вы можете [прочитать дополнительные сведения о развертывании виртуальной машины в Azure с помощью PowerShell](hybrid-use-benefit-licensing.md#detailed-powershell-deployment-walkthrough) ниже или просмотреть описание в руководстве [Создание виртуальной машины Windows с помощью Resource Manager и PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Проверка наличия льготы на гибридное использование Azure для Windows Server
После развертывания виртуальной машины с помощью Resource Manager или PowerShell проверьте тип лицензии с помощью командлета `Get-AzureRmVM` следующим образом.

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Вы должны увидеть результат, аналогичный приведенному ниже примеру для Windows Server.

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
В этом разделе показано, как выполнить полное развертывание виртуальной машины с помощью PowerShell. Дополнительные сведения о необходимых командлетах и создаваемых компонентах см. в статье [Создание виртуальной машины Windows с помощью Resource Manager и PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). В этом разделе описано, как создать группу ресурсов, учетную запись хранения, виртуальную сеть, а также, как определить и создать виртуальную машину.

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

Для Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType "Windows_Server"
```

Для клиента Windows:
```powershell
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType "Windows_Client"
```

## <a name="deploy-a-virtual-machine-scale-set-via-resource-manager-template"></a>Развертывание масштабируемого набора виртуальных машин с помощью шаблона Resource Manager
В шаблонах Resource Manager для масштабируемого набора виртуальных машин можно указывать дополнительный параметр для `licenseType`. Дополнительные сведения см. в статье [Создание шаблонов диспетчера ресурсов Azure](../../resource-group-authoring-templates.md). Измените шаблон Resource Manager, чтобы включить свойство licenseType как часть virtualMachineProfile для масштабируемого набора. Затем разверните шаблон в обычном режиме (см. пример ниже с использованием образа Windows Server 2016):


```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```

> [!NOTE]
> Поддержка развертывания масштабируемого набора виртуальных машин с преимуществами AHUB с использованием PowerShell и других средств SDK будет реализована в ближайшее время.
>

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше о [льготе на гибридное использование Microsoft Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Узнайте больше об [использовании шаблонов Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Узнайте больше о том, как [преимущества гибридного использования Azure и Azure Site Recovery делают перенос приложений в Azure еще более экономичным](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/).

