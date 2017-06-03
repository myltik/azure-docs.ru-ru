---
title: "Создание виртуальных машин Windows и управление ими с помощью модуля Azure PowerShell | Документация Майкрософт"
description: "Руководство по созданию виртуальных машин Windows и управлению ими с помощью модуля Azure PowerShell."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: a26f33247710431d433f3309ecdaca20e605c75a
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017

---

# <a name="create-and-manage-windows-vms-with-the-azure-powershell-module"></a>Создание виртуальных машин Windows и управление ими с помощью модуля Azure PowerShell

Виртуальные машины Azure предоставляют полностью настраиваемую и гибкую вычислительную среду. В этом руководстве рассматриваются основные элементы развертывания виртуальной машины Azure, например выбор ее размера, образа и ее развертывание. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание виртуальной машины и подключение к ней
> * Выбор и использование образов виртуальных машин
> * Просмотр и использование определенных размеров виртуальных машин
> * Изменение размера виртуальной машины
> * Просмотр виртуальной машины и оценка ее состояния

Для работы с этим руководством требуется модуль Azure PowerShell версии не ниже 3.6. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-resource-group"></a>Создать группу ресурсов

Создайте группу ресурсов с помощью команды [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Группу ресурсов следует создавать до виртуальной машины. В этом примере создается группа ресурсов с именем *myResourceGroupVM* в регионе *EastUS*. 

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupVM -Location EastUS
```

Группа ресурсов указывается при создании или изменении виртуальной машины, что показывается в этом руководстве.

## <a name="create-virtual-machine"></a>Создание виртуальной машины

Виртуальная машина должна быть подключена к виртуальной сети. Взаимодействие с виртуальной машиной осуществляется через сетевой адаптер, к которому привязан общедоступный IP-адрес.

### <a name="create-virtual-network"></a>Создание виртуальной сети

Создайте подсеть с помощью командлета [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig).

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
```

Создайте виртуальную сеть с помощью командлета [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork):

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupVM `
  -Location EastUS `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 ` 
  -Subnet $subnetConfig
```
### <a name="create-public-ip-address"></a>Создание общедоступного IP-адреса

Создайте общедоступный IP-адрес с помощью командлета [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

```powershell
$pip = New-AzureRmPublicIpAddress ` 
  -ResourceGroupName myResourceGroupVM `
  -Location EastUS ` 
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

### <a name="create-network-interface-card"></a>Создание сетевого адаптера

Создайте сетевой адаптер с помощью командлета [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface):

```powershell
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroupVM  `
  -Location EastUS `
  -Name myNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

### <a name="create-network-security-group"></a>Создание группы безопасности сети

[Группа безопасности сети](../../virtual-network/virtual-networks-nsg.md) Azure управляет входящим и исходящим трафиком одной или нескольких виртуальных машин. Правила группы безопасности сети разрешают или запрещают передачу сетевого трафика на определенный порт или диапазон портов. Эти правила также могут включать префикс адреса источника, чтобы на виртуальную машину передавался только трафик от определенного источника. Для доступа к веб-серверу IIS, который вы устанавливаете, необходимо добавить правило группы безопасности сети для входящего трафика.

Чтобы создать его, используйте командлет [Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig). Следующий пример создает правило NSG *myNSGRule*, которое открывает порт *3389* для виртуальной машины.

```powershell
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow
```

Создайте группу безопасности сети *myNSGRule* с помощью командлета [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup).

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupVM `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRule
```

Добавьте группу безопасности сети в подсеть виртуальной сети с помощью командлета [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig):

```powershell
Set-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -VirtualNetwork $vnet `
    -NetworkSecurityGroup $nsg `
    -AddressPrefix 192.168.1.0/24
```

Обновите виртуальную сеть, используя командлет [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork):

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-machine"></a>Создание виртуальной машины

При создании виртуальной машины доступно несколько вариантов, таких как образ операционной системы, определение размера диска и учетные данные администратора. В этом примере создается виртуальная машина *myVM*, на которой выполняется последняя версия Windows Server 2016 Datacenter.

Настройте на виртуальной машине имя пользователя и пароль для учетной записи администратора с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Создайте начальную конфигурацию виртуальной машины с помощью командлета [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig):

```powershell
$vm = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1
```

Добавьте сведения об операционной системе в конфигурацию виртуальной машины с помощью командлета [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem):

```powershell
$vm = Set-AzureRmVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM `
    -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
```

Добавьте сведения об образе в конфигурацию виртуальной машины с помощью командлета [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage):

```powershell
$vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
```

Добавьте сведения о настройках диска операционной системы в конфигурацию виртуальной машины с помощью командлета [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk):

```powershell
$vm = Set-AzureRmVMOSDisk `
    -VM $vm `
    -Name myOsDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
```

Добавьте ранее созданный сетевой адаптер в конфигурацию виртуальной машины с помощью командлета [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Создайте виртуальную машину с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroupVM -Location EastUS -VM $vm
```

## <a name="connect-to-vm"></a>Подключение к виртуальной машине

После завершения развертывания создайте подключение к удаленному рабочему столу виртуальной машины.

Выполните приведенные ниже команды, чтобы получить общедоступный IP-адрес виртуальной машины. Запишите этот IP-адрес. Он потребуется, чтобы подключиться к виртуальной машине в браузере и проверить возможность подключения к Интернету на следующем этапе.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroupVM  | Select IpAddress
```

Используйте следующую команду для создания сеанса удаленного рабочего стола с виртуальной машиной. Замените IP-адрес значением *publicIPAddress* виртуальной машины. При появлении запроса введите учетные данные, использованные при создании виртуальной машины.

```powershell
mstsc /v:<publicIpAddress>
```

## <a name="understand-vm-images"></a>Описание образов виртуальных машин

Azure Marketplace содержит множество образов виртуальных машин, которые можно использовать для создания виртуальной машины. На предыдущих шагах виртуальная машина создавалась с помощью образа Windows Server 2016-Datacenter. На этом шаге модуль PowerShell используется для поиска других образов Windows на сайте Marketplace, которые можно также использовать для создания виртуальных машин. Этот процесс состоит из поиска издателя, предложения и имени образа (SKU). 

Используйте команду [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), чтобы получить список издателей образов.  

```powersehll
Get-AzureRmVMImagePublisher -Location "EastUS"
```

Используйте команду [Get-AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer), чтобы получить список предложений образов. С помощью этой команды возвращается список, отфильтрованный по указанному издателю. 

```powershell
Get-AzureRmVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"
```

```powershell
Offer             PublisherName          Location
-----             -------------          -------- 
Windows-HUB       MicrosoftWindowsServer EastUS 
WindowsServer     MicrosoftWindowsServer EastUS   
WindowsServer-HUB MicrosoftWindowsServer EastUS   
```

Команда [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) отфильтрует список по имени издателя и названию предложения, отобразив список имен образов.

```powershell
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

```powershell
Skus                            Offer         PublisherName          Location
----                            -----         -------------          --------
2008-R2-SP1                     WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-BYOL                WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                 WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-BYOL            WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter              WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-BYOL         WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                 WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core     WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-with-Containers WindowsServer MicrosoftWindowsServer EastUS  
2016-Nano-Server                WindowsServer MicrosoftWindowsServer EastUS
```

Эти сведения можно использовать для развертывания виртуальной машины на основе конкретного образа. В этом примере задается имя образа для объекта виртуальной машины. Полные инструкции по развертыванию приведены в предыдущих примерах в этом руководстве.

```powershell
$vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter-with-Containers `
    -Version latest
```

## <a name="understand-vm-sizes"></a>Описание размеров виртуальных машин

Размер виртуальной машины определяет количество выделяемых ей вычислительных ресурсов, таких как ЦП, GPU и память. Виртуальные машины нужно создавать с размером, подходящим для ожидаемой рабочей нагрузки. При увеличении рабочей нагрузки размер существующей виртуальной машины может быть изменен.

### <a name="vm-sizes"></a>Размеры виртуальных машин

В приведенной ниже таблицы указаны категории размеров и примеры использования.  

| Тип                     | Размеры           |    Описание       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| Универсальные         |DSv2, Dv2, DS, D, Av2, A0–A7| Сбалансированное соотношение ресурсов ЦП и памяти. Идеально подходят для разработки и тестирования малых и средних приложений и решений для обработки данных.  |
| Оптимизированные для вычислений      | Fs, F             | Высокое соотношение ресурсов ЦП и памяти. Подходят для приложений со средним объемом трафика, сетевых устройств и пакетных процессов.        |
| Оптимизированные для памяти       | GS, G, DSv2, DS, Dv2, D   | Высокое соотношение ресурсов памяти и числа ядер. Отлично подходят для реляционных баз данных, кэша среднего и большого объема, а также выполняющейся в памяти аналитики.                 |
| Оптимизированные для хранилища       | Ls                | Высокая пропускная способность дисков и количество операций ввода-вывода. Идеальный вариант для работы с большими данными, а также с базами данных SQL и NoSQL.                                                         |
| Графический процессор           | NV, NC            | Специализированные виртуальные машины, предназначенные для ресурсоемкой отрисовки изображений и редактирования видео.       |
| Высокопроизводительные | H, A8–A11          | Виртуальные машины с самыми мощными ЦП, для которых можно настроить сетевые интерфейсы с высокой пропускной способностью (RDMA). 


### <a name="find-available-vm-sizes"></a>Поиск всех доступных размеров виртуальных машин

Чтобы просмотреть список доступных размеров виртуальных машин в определенном регионе, используйте команду [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize).

```powershell
Get-AzureRmVMSize -Location EastUS
```

## <a name="resize-a-vm"></a>Изменение размера виртуальной машины

После развертывания виртуальной машины ее размер можно изменить, чтобы увеличить или уменьшить выделенные ей ресурсы.

Перед изменением размера виртуальной машины проверьте, доступен ли желаемый размер в текущем кластере виртуальных машин. Команда [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) возвращает список размеров. 

```powershell
Get-AzureRmVMSize -ResourceGroupName myResourceGroupVM -VMName myVM 
```

Если желаемый размер доступен, то размер виртуальной машины можно изменить во включенном состоянии, однако виртуальную машину нужно будет перезагрузить.

```powershell
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM 
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
```

Если желаемый размер в текущем кластере недоступен, то перед изменением размера виртуальную машину нужно освободить. Обратите внимание на то, что после повторного включения виртуальной машины все данные на временном диске будут удалены, а общедоступный IP-адрес изменится, если только не используется статический IP-адрес. 

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
Start-AzureRmVM -ResourceGroupName myResourceGroupVM  -Name $vm.name
```

## <a name="vm-power-states"></a>Состояния включенной виртуальной машины

Включенная виртуальная машина Azure может находиться в одном из многих состояний. Это состояние отражает текущее состояние виртуальной машины с точки зрения гипервизора. 

### <a name="power-states"></a>Состояния включения

| Состояние включения | Описание
|----|----|
| Starting | Указывает, что виртуальная машина запущена. |
| Выполнение | Указывает, что виртуальная машина работает. |
| Остановка | Указывает, что виртуальная машина останавливается. | 
| Остановлено | Указывает, что виртуальная машина остановлена. Обратите внимание, что за виртуальные машины в остановленном состоянии по-прежнему взимается плата за вычислительные операции.  |
| Отмена выделения | Указывает, что виртуальная машина освобождается. |
| Освобождено | Указывает, что виртуальная машина полностью удалена из гипервизора, но по-прежнему доступна в плоскости управления. За виртуальные машины в освобожденном состоянии не взимается плата за вычислительные операции. |
| - | Указывает, что состояние включенной виртуальной машины неизвестно. |

### <a name="find-power-state"></a>Поиск состояние включения

Чтобы получить состояние конкретной виртуальной машины, используйте команду [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm). Необходимо указать допустимое имя виртуальной машины и группы ресурсов. 

```powershell
Get-AzureRmVM `
    -ResourceGroupName myResourceGroup `
    -Name myVM `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

Выходные данные:

```powershell
Status
------
PowerState/running
```

## <a name="management-tasks"></a>Задачи управления

В течение жизненного цикла виртуальной машины можно выполнять задачи управления, такие как запуск, остановка или удаление виртуальной машины. Кроме того, можно создавать скрипты для автоматизации повторяющихся или сложных задач. С помощью Azure PowerShell можно выполнять множество распространенных задач управления в командной строке или в скриптах.

### <a name="stop-virtual-machine"></a>Прекращение работы виртуальной машины

Остановите и освободите виртуальную машину с помощью командлета [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm):

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
```

Если вы хотите сохранить виртуальную машину в подготовленном состоянии, задайте параметр -StayProvisioned.

### <a name="start-virtual-machine"></a>Запуск виртуальной машины

```powershell
Start-AzureRmVM -ResourceGroupName myResourceGroupVM -Name myVM
```

### <a name="delete-resource-group"></a>Удалить группу ресурсов

При удалении группы ресурсов будут также удалены все ресурсы, содержащиеся в ней.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroupVM -Force
```

## <a name="next-steps"></a>Дальнейшие действия

В рамках этого руководства вы изучили основы создания виртуальной машины и управления ею. Вы узнали, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание виртуальной машины и подключение к ней
> * Выбор и использование образов виртуальных машин
> * Просмотр и использование определенных размеров виртуальных машин
> * Изменение размера виртуальной машины
> * Просмотр виртуальной машины и оценка ее состояния

Перейдите к следующему руководству, чтобы узнать о дисках виртуальных машин.  

> [!div class="nextstepaction"]
> [Управление дисками Azure с помощью Azure CLI](./tutorial-manage-data-disk.md)

