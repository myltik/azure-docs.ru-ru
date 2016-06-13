<properties
   pageTitle="Льгота на гибридное использование Azure для Windows Server | Microsoft Azure"
   description="Узнайте, как воспользоваться преимуществами программы Software Assurance для Windows Server, чтобы перенести свои локальные лицензии в Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="05/03/2016"
   ms.author="georgem"/>

# Льгота на гибридное использование Azure для Windows Server

Льгота на гибридное использование Azure позволяет пользователям Windows Server с лицензией Software Assurance перенести свои локальные лицензии и развернуть виртуальные машины Windows Server в Azure с меньшими затратами. Кроме того, эта возможность позволяет платить за развернутые виртуальные машины Windows Server в Azure только по базовой ставке вычислений. Дополнительные сведения см. на странице [Льгота гибридного использования Microsoft Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). В этой статье описывается, как развернуть виртуальные машины Windows Server в Azure, чтобы воспользоваться льготой лицензирования.

> [AZURE.NOTE] Льгота на гибридное использование Azure не распространяется на образы Azure Marketplace. Чтобы получить ее, необходимо развернуть виртуальные машины с помощью PowerShell или шаблонов Resource Manager, соответствующим образом зарегистрировав их для тарифицирования по базовой ставке вычислений.

## Предварительные требования
Чтобы воспользоваться льготой на гибридное использование Azure для виртуальных машин Windows Server, нужно выполнить определенные предварительные требования:

- установить модуль Azure PowerShell;
- передать виртуальный жесткий диск Windows Server в службу хранилища Azure.

### Установка Azure PowerShell
Сведения об установке последней версии Azure PowerShell, выборе нужной подписки и входе в учетную запись Azure см. в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md). Если вы планируете развернуть виртуальные машины с помощью шаблонов Resource Manager, по-прежнему требуется установить Azure PowerShell, чтобы отправить виртуальный жесткий диск Windows Server в Azure.

### Отправка виртуального жесткого диска Windows Server

Чтобы развернуть виртуальную машину Windows Server в Azure, сначала необходимо создать виртуальный жесткий диск, в котором содержится базовая сборка Windows Server. Перед отправкой виртуального жесткого диска в Azure его необходимо соответствующим образом подготовить с помощью программы Sysprep. См. дополнительные сведения о [требованиях к виртуальному жесткому диску и использовании программы Sysprep](./virtual-machines-windows-upload-image.md). Чтобы отправить подготовленный виртуальный жесткий диск в учетную запись службы хранилища Azure, запустите командлет `Add-AzureRmVhd` со следующими параметрами:

```
Add-AzureRmVhd -ResourceGroupName MyResourceGroup -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd" -LocalFilePath 'C:\Path\To\myvhd.vhd'
```

Вы также можете узнать об [отправке виртуального жесткого диска в Azure](./virtual-machines-windows-upload-image.md#upload-the-vm-image-to-your-storage-account).

> [AZURE.TIP] Несмотря на то, что эта статья посвящена развертыванию виртуальных машин Windows Server, действия, описанные в ней, можно использовать для развертывания виртуальных машин клиента Windows. Для этого в следующих примерах необходимо соответствующим образом заменить `Server` на `Client`.

## Краткое руководство по развертыванию виртуальной машины с помощью PowerShell
При развертывании виртуальной машины Windows Server с помощью PowerShell вам предоставляется дополнительный параметр для `-LicenseType`. После загрузки виртуального жесткого диска в Azure необходимо создать новую виртуальную машину с помощью комадлета `New-AzureRmVM` и указать тип лицензирования следующим образом:

```
New-AzureRmVM -ResourceGroupName MyResourceGroup -Location "West US" -VM $vm
    -LicenseType Windows_Server
```

Вы можете подробнее [прочитать о развертывании виртуальной машины в Azure с помощью PowerShell](./virtual-machines-windows-hybrid-use-benefit-licensing.md#deploy-windows-server-vm-via-powershell-detailed-walkthrough) ниже или просмотреть сведения в статье [Создание виртуальной машины Windows с помощью Resource Manager и PowerShell](./virtual-machines-windows-ps-create.md).

## Развертывание виртуальной машины с помощью Resource Manager
В шаблоне Resource Manager можно указать дополнительный параметр для `licenseType`. Дополнительные сведения см. в статье [Создание шаблонов диспетчера ресурсов Azure](../resource-group-authoring-templates.md). После загрузки виртуального жесткого диска в Azure необходимо изменить шаблон Resource Manager, чтобы включить в него тип лицензирования как часть поставщика вычислительных ресурсов и развернуть шаблон в обычном режиме.

```
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   },
```
 
## Проверка наличия льготы на гибридное использование Azure для Windows Server
После развертывания виртуальной машины с помощью Resource Manager или PowerShell проверьте тип лицензии с помощью командлета `Get-AzureRmVM` следующим образом:
 
```
Get-AzureRmVM -ResourceGroup MyResourceGroup -Name MyVM
```

Должен появиться результат, аналогичный приведенному ниже.

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Эти выходные данные отличаются от полученных при развертывании виртуальной машины без льготы на гибридное использование Azure (например, при развертывании виртуальной машины непосредственно из коллекции Azure).

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : 
```
 
## Подробное пошаговое руководство по использованию PowerShell

В этом разделе показано, как выполнить полное развертывание виртуальной машины с помощью PowerShell. Дополнительные сведения о необходимых командлетах и создаваемых компонентах см. в статье [Создание виртуальной машины Windows с помощью Resource Manager и PowerShell](./virtual-machines-windows-ps-create.md). В этом разделе описано, как создать группу ресурсов, учетную запись хранения, виртуальную сеть, а также, как определить и создать виртуальную машину.
 
Сначала выполните следующую команду, чтобы получить учетные данные, задать расположение и имя группы ресурсов.

```
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "TestLicensing"
```

Создайте общедоступный IP-адрес.

```
$publicIPName = "testlicensingpublicip"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
```

Определите подсеть, сетевой адаптер и виртуальную сеть.

```
$subnetName = "testlicensingsubnet"
$nicName = "testlicensingnic"
$vnetName = "testlicensingvnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

Присвойте имя виртуальной машине и настройте ее конфигурацию.

```
$vmName = "testlicensing"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

Определите операционную систему.

```
$computerName = "testlicensing"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
```

Добавьте сетевой адаптер в виртуальную машину.

```
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Выберите учетную запись хранения, которая будет использоваться.

```
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName testlicensing
```

Отправьте подготовленный виртуальный жесткий диск в Azure и подключите его к виртуальной машине.

```
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://testlicensing.blob.core.windows.net/vhd/licensing.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
```

Наконец, создайте виртуальную машину и определите тип лицензии для использования льготы на гибридное использование Azure.

```
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType Windows_Server
```

## Дальнейшие действия

Узнайте больше о [льготе на гибридное использование Microsoft Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Узнайте больше об [использовании шаблонов Resource Manager](../resource-group-overview.md).

<!---HONumber=AcomDC_0601_2016-->