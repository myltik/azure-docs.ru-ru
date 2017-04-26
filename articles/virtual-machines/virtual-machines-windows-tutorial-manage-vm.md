---
title: "Управление виртуальными машинами Windows с помощью Azure PowerShell | Документация Майкрософт"
description: "Руководство по управлению виртуальными машинами Windows с помощью Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/21/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 8dde92d3bee451ee701508cec98b3581384fd16c
ms.lasthandoff: 04/06/2017

---

# <a name="manage-windows-virtual-machines-with-azure-powershell"></a>Управление виртуальными машинами Windows с помощью Azure PowerShell

Работая с этим руководством, вы создадите виртуальную машину и выполните стандартные задачи по управлению, такие как добавление диска, автоматизация установки программного обеспечения, настройка правил брандмауэра и создание моментального снимка виртуальной машины.

Для работы с этим руководством нужно установить последнюю версию модуля [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

## <a name="step-1--log-in-to-azure"></a>Шаг 1. Вход в Azure

Прежде всего войдите в подписку Azure с помощью командлета Login-AzureRmAccount и следуйте инструкциям на экране.

```powershell
Login-AzureRmAccount
```

## <a name="step-2---create-resource-group"></a>Шаг 2. Создание группы ресурсов

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Группу ресурсов следует создавать до виртуальной машины.

Создайте группу ресурсов с помощью командлета [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Resources/v2.0.3/new-azurermresourcegroup).  В нашем примере создается группа ресурсов с именем `myResourceGroup` в регионе `westeurope`.

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location westeurope
```

## <a name="step-3---create-virtual-machine"></a>Шаг 3. Создание виртуальной машины

Виртуальная машина должна быть подключена к виртуальной сети. Взаимодействие с виртуальной машиной осуществляется через сетевой адаптер, к которому привязан общедоступный IP-адрес.

### <a name="create-virtual-network"></a>Создание виртуальной сети

Создайте подсеть с помощью командлета [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/new-azurermvirtualnetworksubnetconfig).

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24
```

Создайте виртуальную сеть с помощью командлета [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/new-azurermvirtualnetwork):

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-public-ip-address"></a>Создание общедоступного IP-адреса

Создайте общедоступный IP-адрес с помощью командлета [New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/new-azurermpublicipaddress):

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

### <a name="create-network-interface-card"></a>Создание сетевого адаптера

Создайте сетевой адаптер с помощью командлета [New-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/new-azurermnetworkinterface):

```powershell
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

### <a name="create-network-security-group"></a>Создание группы безопасности сети

[Группа безопасности сети](../virtual-network/virtual-networks-nsg.md) Azure управляет входящим и исходящим трафиком одной или нескольких виртуальных машин. Правила группы безопасности сети разрешают или запрещают передачу сетевого трафика на определенный порт или диапазон портов. Эти правила также могут включать префикс адреса источника, чтобы на виртуальную машину передавался только трафик от определенного источника. Для доступа к веб-серверу IIS, который вы устанавливаете, необходимо добавить правило группы безопасности сети для входящего трафика.

Чтобы создать его, используйте командлет [Add-AzureRmNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/add-azurermnetworksecurityruleconfig). Следующий пример создает правило NSG с именем `myNSGRule`, которое открывает порт `80` для виртуальной машины:

```powershell
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

Создайте группу безопасности сети с именем `myNSGRule` с помощью командлета [New-AzureRmNetworkSecurityGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/new-azurermnetworksecuritygroup):

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRule
```

Добавьте группу безопасности сети в подсеть виртуальной сети с помощью командлета [Set-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/set-azurermvirtualnetworksubnetconfig):

```powershell
Set-AzureRmVirtualNetworkSubnetConfig -Name mySubnet `
  -VirtualNetwork $vnet `
  -NetworkSecurityGroup $nsg `
  -AddressPrefix 192.168.1.0/24
```

Обновите виртуальную сеть, используя командлет [Set-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/set-azurermvirtualnetwork):

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-machine"></a>Создание виртуальной машины

При создании виртуальной машины доступно несколько вариантов, таких как образ операционной системы, определение размера диска и учетные данные администратора. В этом примере создается виртуальная машина с именем `myVM`, на которой выполняется последняя версия Windows Server 2016 Datacenter.

Настройте на виртуальной машине имя пользователя и пароль для учетной записи администратора с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Создайте начальную конфигурацию виртуальной машины с помощью командлета [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvmconfig):

```powershell
$vm = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1
```

Добавьте сведения об операционной системе в конфигурацию виртуальной машины с помощью командлета [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmoperatingsystem):

```powershell
$vm = Set-AzureRmVMOperatingSystem -VM $vm `
  -Windows `
  -ComputerName myVM `
  -Credential $cred `
  -ProvisionVMAgent `
  -EnableAutoUpdate
```

Добавьте сведения об образе в конфигурацию виртуальной машины с помощью командлета [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmsourceimage):

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest
```

Добавьте сведения о настройках диска операционной системы в конфигурацию виртуальной машины с помощью командлета [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmosdisk):

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm `
  -Name myOsDisk `
  -DiskSizeInGB 128 `
  -CreateOption FromImage `
  -Caching ReadWrite
```

Добавьте ранее созданный сетевой адаптер в конфигурацию виртуальной машины с помощью командлета [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/add-azurermvmnetworkinterface):

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Создайте виртуальную машину с помощью командлета [New-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvm):

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location westeurope -VM $vm
```

## <a name="step-4--add-data-disk"></a>Шаг 4. Добавление диска данных

По умолчанию виртуальные машины Azure создаются с одним диском операционной системы. Вы можете добавить дополнительные диски для создания конфигурации хранилища с несколькими дисками, для установки приложений или хранения данных.

### <a name="create-disk"></a>Создание диска

Создайте начальную конфигурацию диска данных, выполнив командлет [New-AzureRmDiskConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermdiskconfig). В следующем примере создается диск с именем `myDataDisk` размером 50 ГБ.

```powershell
$diskConfig = New-AzureRmDiskConfig `
  -Location westeurope `
  -CreateOption Empty `
  -DiskSizeGB 50
```

Создайте диск данных с помощью командлета [New-AzureRmDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermdisk):

```powershell
$dataDisk = New-AzureRmDisk `
  -ResourceGroupName myResourceGroup `
  -DiskName myDataDisk `
  -Disk $diskConfig
```

Получите виртуальную машину, в которую вы хотите добавить диск данных, выполнив командлет [Get-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/get-azurermvm):

```powershell
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

Добавьте диск данных в конфигурацию виртуальной машины с помощью командлета [Add-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/add-azurermvmdatadisk):

```powershell
$vm = Add-AzureRmVMDataDisk `
  -VM $vm `
  -Name myDataDisk `
  -CreateOption Attach `
  -ManagedDiskId $dataDisk.Id `
  -Lun 1
```

Обновите виртуальную машину с помощью командлета [Update-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/update-azurermvm):

```powershell
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm
```

## <a name="step-5--automate-configuration"></a>Шаг 5. Автоматизация настройки

Расширения виртуальной машины Azure позволяют автоматизировать задачи по настройке виртуальной машины, например установку приложений и настройку операционной системы. С помощью [расширения пользовательских скриптов для Windows](./virtual-machines-windows-extensions-customscript.md) можно запустить на виртуальной машине любой скрипт PowerShell. Это может быть скрипт, сохраненный в хранилище Azure, на любой доступной конечной точке HTTP или встроенный в конфигурацию расширения пользовательских скриптов. В этом руководстве мы автоматизируем два действия:

- установка IIS;
- форматирование диска данных на виртуальной машине.

Расширение выполняется во время развертывания виртуальной машины, поэтому файл **install-iis-format-disk.ps1** нужно определить до создания виртуальной машины. Файл, используемый в этом руководстве, расположен в репозитории скриптов Azure PowerShell. Этот файл содержит команды `Add-WindowsFeature Web-Server` и `Get-Disk | Where partitionstyle -eq 'raw' | Initialize-Disk -PartitionStyle MBR -PassThru | New-Partition -AssignDriveLetter -UseMaximumSize | Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false**`.

Добавьте расширение, применив командлет [Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmextension):

```powershell
Set-AzureRmVMExtension -Name myScript `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -VMName myVM `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.4 `
  -SettingString '{"fileUris":["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/virtual-machine/install-iis-format-disk/install-iis-format-disk.ps1?token=ABlGkLjzVTdZGoCRbu1pCXjvSDRMHnUlks5Y5nbZwA%3D%3D"], "commandToExecute":"powershell.exe -ExecutionPolicy Unrestricted -File install-iis-format-disk.ps1" }'
```

Получите общедоступный IP-адрес виртуальной машины с помощью командлета [Get-AzureRmPublicIPAddress](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.6.0/get-azurermpublicipaddress):

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroup -Name myPublicIPAddress | select IpAddress
```

Теперь перейдите к общедоступному IP-адресу виртуальной машины. После создания правила группы безопасности сети отобразится веб-сайт IIS по умолчанию.


## <a name="step-6--snapshot-virtual-machine"></a>Шаг 6. Создание моментального снимка виртуальной машины

Создавая моментальный снимок виртуальной машины, вы получаете доступную только для чтения копию диска операционной системы виртуальной машины на определенный момент времени. С помощью моментального снимка вы можете легко восстановить виртуальную машину в определенном состоянии или создать новую виртуальную машину с идентичным состоянием.

### <a name="create-snapshot"></a>Создание моментального снимка

Прежде чем создавать моментальный снимок диска виртуальной машины, вам нужно узнать идентификатор диска операционной системы для этой виртуальной машины.

Получите диск операционной системы с помощью командлета [Get-AzureRmDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/get-azurermdisk):

```powershell
$vmOSDisk = Get-AzureRmDisk -ResourceGroupName myResourceGroup -Name myOSDisk
```

Создайте конфигурацию моментального снимка, выполнив командлет New-AzureRmSnapshotConfig:

```powershell
$snapshotConfig = New-AzureRmSnapshotConfig -Location westeurope -CreateOption Copy -SourceResourceId $vmOSDisk.id
```

Создайте моментальный снимок с помощью командлета New-AzureRmSnapshot:

```powershell
$snapshot = New-AzureRmSnapshot -ResourceGroupName myResourceGroup -SnapshotName mySnapshot -Snapshot $snapshotConfig
```

### <a name="create-disk-from-snapshot"></a>Создание диска на основе моментального снимка

Этот моментальный снимок можно преобразовать в диск, с помощью которого можно повторно создать виртуальную машину.

Создайте конфигурацию диска, выполнив командлет [New-AzureRmDiskConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermdiskconfig):

```powershell
$diskConfig = New-AzureRmDiskConfig -Location westeurope -CreateOption Copy -SourceResourceId $snapshot.id
```

Создайте диск с помощью командлета [New-AzureRmDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermdisk):

```powershell
$disk = New-AzureRmDisk -ResourceGroupName myResourceGroup -DiskName myOSDiskFromSnapshot -Disk $diskConfig
```

### <a name="restore-virtual-machine-from-snapshot"></a>Восстановление виртуальной машины на основе моментального снимка

Чтобы продемонстрировать восстановление виртуальной машины, удалите существующую виртуальную машину.

```powershell
Remove-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

Создайте виртуальную машину на основе диска моментального снимка. В нашем примере указан существующий сетевой интерфейс. Эта конфигурация применяет все ранее созданные правила NSG к новой виртуальной машине.

Создайте начальную конфигурацию виртуальной машины с помощью командлета [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvmconfig):

```powershell
$vm = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1
```

Добавьте диск операционной системы с помощью командлета [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmosdisk):

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -CreateOption Attach -ManagedDiskId $disk.Id -Windows
```

Добавьте сетевой адаптер с помощью командлета [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/add-azurermvmnetworkinterface):

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Создайте виртуальную машину с помощью командлета [New-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvm):

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -Location westeurope
```

В адресной строке браузера введите общедоступный IP-адрес виртуальной машины. Это позволит вам убедиться, что IIS работает на восстановленной виртуальной машине.

## <a name="step-7--management-tasks"></a>Шаг 7. Задачи управления

В течение жизненного цикла виртуальной машины можно выполнять задачи управления, такие как запуск, остановка или удаление виртуальной машины. Кроме того, можно создавать скрипты для автоматизации повторяющихся или сложных задач. С помощью Azure PowerShell можно выполнять множество распространенных задач управления в командной строке или в скриптах.

### <a name="stop-virtual-machine"></a>Прекращение работы виртуальной машины

Остановите и освободите виртуальную машину с помощью командлета [Stop-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/stop-azurermvm):

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

Если вы хотите сохранить виртуальную машину в подготовленном состоянии, задайте параметр -StayProvisioned.

### <a name="resize-virtual-machine"></a>Изменение размера виртуальной машины

Чтобы изменить размер виртуальной машины, которую вы останавливаете и освобождаете, вам нужны названия размеров, доступных в выбранном регионе Azure. Эти размеры можно получить с помощью командлета [Get-AzureRmVMSize](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/get-azurermvmsize):

```powershell
Get-AzureRmVMSize -Location westeurope
```

Получите виртуальную машину, размер которой вы хотите изменить, выполнив командлет [Get-AzureRmDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/get-azurermdisk):

```powershell
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

Установите новый размер виртуальной машины, задав для свойства vmSize значение имени нового размера.

```powershell
$vm.HardwareProfile.vmSize = "Standard_F4s"
```

Обновите виртуальную машину с помощью командлета [Update-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/update-azurermvm):

```powershell
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm
```

### <a name="start-virtual-machine"></a>Запуск виртуальной машины

```powershell
Start-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

### <a name="delete-resource-group"></a>Удалить группу ресурсов

При удалении группы ресурсов будут также удалены все ресурсы, содержащиеся в ней.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Дальнейшие действия

[Примеры PowerShell для виртуальной машины Azure](./virtual-machines-windows-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

