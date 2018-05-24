---
title: Создание виртуальной машины Azure с ускоренной сетью | Документация Майкрософт
description: Узнайте, как создать виртуальную машину Linux с ускоренной сетью.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: gsilva
ms.openlocfilehash: de69cdf69f30639d048dccd7d433c86f6cb9db7b
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894192"
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>Создание виртуальной машины Windows с ускоренной сетью

В этом руководстве вы узнаете, как создать виртуальную машину Windows с ускоренной сетью. Сведения о создании виртуальной машины Linux с ускоренной сетью см. в статье [Создание виртуальной машины Linux с ускоренной сетью](create-vm-accelerated-networking-cli.md). Функция ускорения сети позволяет использовать виртуализацию ввода-вывода с единым корнем (SR-IOV), повышая тем самым производительность сети виртуальной машины. Этот высокопроизводительный метод обеспечивает обход узла на пути к данным, что уменьшает задержку, дрожание и нагрузку ЦП. Он предназначен для ресурсоемких рабочих нагрузок сети на виртуальных машинах поддерживаемых типов. На следующем рисунке приведена схема обмена данными между двумя виртуальными машинами с функцией ускорения сети и без нее:

![Сравнение](./media/create-vm-accelerated-networking/accelerated-networking.png)

Без функции ускорения сети весь входящий и исходящий сетевой трафик виртуальной машины должен проходить через узел и виртуальный коммутатор. Виртуальный коммутатор обеспечивает принудительное применение всех политик, таких как группы безопасности сети, списки управления доступом, изоляция, и использование других служб виртуализации сети для сетевого трафика. Дополнительные сведения о виртуальных коммутаторах см. в статье о [виртуализации сети Hyper-V](https://technet.microsoft.com/library/jj945275.aspx).

При использовании функции ускорения сети трафик поступает в сетевой интерфейс (NIC), а затем перенаправляется на виртуальную машину. Все сетевые политики, которые применяет виртуальный коммутатор, выгружены и применяются на аппаратном уровне. Благодаря применению политик на аппаратном уровне сетевая карта может перенаправлять сетевой трафик непосредственно на виртуальную машину в обход узла и виртуального коммутатора. При этом все политики, примененные на узле, сохраняются.

Возможности функции ускорения сети применяются только к той виртуальной машине, где она включена. Для получения наилучших результатов необходимо включить эту функцию по крайней мере на двух виртуальных машинах, подключенных к одной виртуальной сети Azure. При обмене данными между виртуальными или локальными сетями эта функция практически не влияет на общую задержку.

## <a name="benefits"></a>Преимущества
* **Уменьшение задержки (больше пакетов в секунду).** Благодаря обходу виртуального коммутатора на пути к данным на узле не выполняется обработка политики пакетов. Таким образом, увеличивается число пакетов, которые могут быть обработаны на виртуальной машине.
* **Уменьшение дрожания.** Обработка с помощью виртуального коммутатора зависит от числа политик, которые необходимо применить, и рабочей нагрузки ЦП, выполняющего обработку. Так как принудительное применение политик осуществляется на аппаратном уровне, эта зависимость устраняется за счет доставки пакетов непосредственно на виртуальную машину. Это позволяет избежать обмена данными между узлом и виртуальной машиной, прерываний работы программного обеспечения и переключений контекста.
* **Уменьшение нагрузки ЦП.** Обход виртуального коммутатора на узле приводит к меньшему использованию ЦП для обработки сетевого трафика.

## <a name="limitations-and-constraints"></a>Пределы и ограничения

### <a name="supported-operating-systems"></a>Поддерживаемые операционные системы
Без дополнительной настройки из коллекции Azure поддерживаются следующие дистрибутивы: 
* **Windows Server 2016 Datacenter** 
* **Центр обработки данных Windows Server 2012 R2** 

### <a name="supported-vm-instances"></a>Поддерживаемые экземпляры виртуальных машин
Функция ускорения сети поддерживается для большинства размеров экземпляров, оптимизированных для вычислений, и экземпляров общего назначения с количеством виртуальных ЦП от 2.  Поддерживаемые серии: D/DSv2 и F/Fs

На экземплярах, поддерживающих технологию Hyper-Threading, функция ускорения сети поддерживается в экземплярах виртуальных машин с количеством виртуальных ЦП от 4. Поддерживаемые серии: D/DSv3, E/ESv3, Fsv2 и Ms/Mms

Дополнительные сведения см. в статье [Размеры виртуальных машин Windows в Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="regions"></a>регионы
Доступна во всех общедоступных регионах Azure и облаке Azure для государственных организаций.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Включение ускоренной сети для работающей виртуальной машины
Чтобы включить ускоренную сеть для виртуальной машины поддерживаемого размера, для которой эта функция отключена, виртуальную машину необходимо остановить и отменить ее распределение.

### <a name="deployment-through-azure-resource-manager"></a>Развертывание с помощью Azure Resource Manager
Классические виртуальные машины нельзя развернуть с включенной функцией ускорения сети.

## <a name="create-a-windows-vm-with-azure-accelerated-networking"></a>Создание виртуальной машины Windows с ускорением сети Azure

В этой статье описано, как создать виртуальную машину с ускорением работы в сети. Но с помощью Azure PowerShell вы также можете [создать аналогичную виртуальную машину на портале Azure](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Создавая виртуальную машину на портале в разделе **Параметры** выберите **Включено** для параметра **Ускоренная сеть**. Параметр включения ускоренной сети не отобразится на портале, пока не выбраны [поддерживаемая операционная система](#supported-operating-systems) и [размер виртуальной машины](#supported-vm-instances). После создания виртуальной машины необходимо выполнить инструкции по [подтверждению установки драйвера в операционной системе](#confirm-the-driver-is-installed-in-the-operating-system).

## <a name="create-a-virtual-network"></a>Создать виртуальную сеть

Установите [Azure PowerShell](/powershell/azure/install-azurerm-ps) версии 5.1.1 или более поздней. Чтобы определить текущую версию, выполните командлет `Get-Module -ListAvailable AzureRM`. Если требуется установка или обновление, установите последнюю версию модуля AzureRM из [коллекции PowerShell](https://www.powershellgallery.com/packages/AzureRM). В сеансе PowerShell войдите в учетную запись Azure с помощью командлета [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount).

В следующих примерах замените имена параметров собственными значениями. Примеры имен параметров: *myResourceGroup*, *myNic* и *myVM*.

Создайте группу ресурсов с помощью командлета [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *centralus*.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Сначала создайте конфигурацию подсети с помощью командлета [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetworkSubnetConfig). В следующем примере создается подсеть с именем *mySubnet*:

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Создайте виртуальную сеть с помощью командлета [New-AzureRmVirtualNetwork](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetwork) с подсетью *mySubnet*.

```powershell
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Сначала создайте правило группы безопасности сети с помощью командлета [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityRuleConfig).

```powershell
$rdp = New-AzureRmNetworkSecurityRuleConfig `
    -Name 'Allow-RDP-All' `
    -Description 'Allow RDP' `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 100 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389
```

Затем с помощью командлета [New-AzureRmNetworkSecurityGroup](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityGroup) создайте группу безопасности сети и назначьте ей правило безопасности *Allow-RDP-All*. Кроме правила *Allow-RDP-All* группа безопасности сети содержит несколько стандартных правил. С помощью одного правила по умолчанию запрещается входящий трафик из Интернета, из-за чего правило *Allow-RDP-All* и назначается группе безопасности сети. Таким образом вы можете удаленно подключиться к виртуальной машине после ее создания.

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Свяжите группу безопасности сети с подсетью *mySubnet* с помощью командлета [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/Set-AzureRmVirtualNetworkSubnetConfig). Правило в группе безопасности сети распространяется на все ресурсы, развернутые в подсети.

```powershell
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Создание сетевого интерфейса с функцией ускорения сети
Создайте общедоступный IP-адрес с помощью командлета [New-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/New-AzureRmPublicIpAddress). Если вы не планируете входить на виртуальную машину из Интернета, общедоступный IP-адрес не требуется, однако он нужен, чтобы выполнить шаги, описанные в этой статье.

```powershell
$publicIp = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

С помощью командлета [New-AzureRmNetworkInterface](/powershell/module/AzureRM.Network/New-AzureRmNetworkInterface) создайте сетевой интерфейс с включенной функцией ускорения сети и назначьте этому интерфейсу общедоступный IP-адрес. В следующем примере создается сетевой интерфейс с именем *myNic* в подсети *mySubnet* виртуальной сети *myVnet* и ему назначается общедоступный IP-адрес *myPublicIp*.

```powershell
$nic = New-AzureRmNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>Создание виртуальной машины

Задайте переменной `$cred` учетные данные виртуальной машины, используя командлет [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

Сначала определите виртуальную машину с помощью командлета [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). В следующем примере определяется виртуальная машина с именем *myVM* и используется размер виртуальной машины с поддержкой функции ускорения сети (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Чтобы просмотреть список всех размеров виртуальных машин и их характеристики, ознакомьтесь со статьей [Размеры виртуальных машин Windows в Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Создайте оставшуюся часть конфигурации виртуальной машины с помощью [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) и [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage). В следующем примере создается виртуальная машина Windows Server 2016:

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig `
    -Windows `
    -ComputerName "myVM" `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig `
    -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" `
    -Skus "2016-Datacenter" `
    -Version "latest"
```

Подключите созданный ранее сетевой интерфейс с помощью командлета [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Наконец, создайте виртуальную машину с помощью [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Проверка установки драйвера в операционной системе

После создания виртуальной машины в Azure подключитесь к ней и проверьте, установлен ли драйвер в Windows.

1. Откройте [портал](https://portal.azure.com) Azure в браузере и войдите, используя учетную запись Azure.
2. В верхней области портала Azure в поле с текстом *Поиск ресурсов* введите *MyVm*. Когда виртуальная машина **MyVm** появится в результатах поиска, щелкните ее. Если под кнопкой **Подключиться** отображается элемент **Создание**, это означает, что виртуальная машина по-прежнему создается в Azure. Когда элемент **Создание** больше не будет отображаться под кнопкой **Подключиться**, вы можете нажать кнопку **подключения** в левом верхнем углу страницы обзора.
3. Введите имя пользователя и пароль, указанный при [создании виртуальной машины](#create-the-virtual-machine). Ознакомьтесь с разделом [Подключение к виртуальной машине](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine), если вы никогда не подключались к виртуальной машине Windows в Azure.
4. В главном меню Windows щелкните правой кнопкой мыши кнопку "Пуск" и выберите **Диспетчер устройств**. Разверните узел **Сетевые адаптеры**. Проверьте, есть ли в списке виртуальный адаптер **Mellanox ConnectX-3 Virtual Function Ethernet Adapter**, как показано на следующем рисунке:

    ![Диспетчер устройств](./media/create-vm-accelerated-networking/device-manager.png)

Ускорение работы в сети теперь включено на вашей виртуальной машине.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Включение ускоренной сети для имеющихся виртуальных машин
Если вы создали виртуальную машину без ускоренной сети, для нее можно включить эту функцию позднее.  Виртуальная машина должна поддерживать ускоренную сеть, то есть соответствовать указанным ниже требованиям, которые также описаны выше.

* Виртуальная машина должна иметь размер, поддерживаемый функцией ускоренной сети.
* Виртуальная машина должна быть развернута из поддерживаемого образа коллекции Azure (и иметь поддерживаемую версию ядра для Linux).
* Все виртуальные машины в группе доступности или масштабируемом наборе виртуальных машин должны быть остановлены и освобождены перед включением ускоренной сети в любом из сетевых адаптеров.

### <a name="individual-vms--vms-in-an-availability-set"></a>Отдельные виртуальные машины и виртуальные машины в группе доступности
Сначала остановите виртуальную машину и отмените ее выделение. В случае с группой доступности сделайте это со всеми виртуальными машинами в наборе:

```azurepowershell
Stop-AzureRmVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

Обратите внимание на то, что если виртуальная машина была создана отдельно, то есть без группы доступности, для включения ускоренной сети необходимо остановить только эту виртуальную машину и отменить ее выделение.  Если виртуальная машина была создана с группой доступности, перед включением ускоренной сети в любом сетевом адаптере необходимо остановить все виртуальные машины в группе и отменить их выделение. 

После остановки виртуальной машины включите ускоренную сеть в ее сетевом адаптере:

```azurepowershell
$nic = Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic"

$nic.EnableAcceleratedNetworking = $true

$nic | Set-AzureRmNetworkInterface
```

Перезапустите виртуальную машину или, в случае с группой доступности, все виртуальные машины в группе и убедитесь в том, что ускоренная сеть включена: 

```azurepowershell
Start-AzureRmVM -ResourceGroup "myResourceGroup" `
    -Name "myVM"
```

### <a name="vmss"></a>Масштабируемый набор виртуальных машин
Процедура для масштабируемого набора виртуальных машин немного отличается, но в целом похожа.  Сначала остановите виртуальные машины:

```azurepowershell
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" ` 
    -VMScaleSetName "myScaleSet"
```

После остановки виртуальных машин измените значение свойства ускоренной сети в сетевом интерфейсе:

```azurepowershell
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet"

$vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true

Update-AzureRmVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Обратите внимание на то, что обновления к виртуальным машинам в масштабируемом наборе применяются тремя разными способами: автоматически, последовательно и вручную.  В рассматриваемом случае настроено автоматическое обновление, поэтому изменения применяются к масштабируемому набору виртуальных машин сразу после перезапуска.  Чтобы настроить автоматическое обновление, выполните следующую команду: 

```azurepowershell
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true

Update-AzureRmVmss -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Наконец, перезапустите масштабируемый набор виртуальных машин:

```azurepowershell
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" ` 
    -VMScaleSetName "myScaleSet"
```

После перезапуска дождитесь завершения обновления. Как только оно закончится, в виртуальной машине появится виртуальная функция.  (Убедитесь в том, что вы используете поддерживаемую ОС и размер виртуальной машины.)

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Изменение размеров имеющихся виртуальных машин с ускоренной сетью

Размер виртуальных машин с ускоренной сетью можно изменить только на такой, который также поддерживает ускоренную сеть.  

Виртуальную машину с включенной виртуальной сетью нельзя изменить на экземпляр такого размера, который не поддерживает ускоренную сеть, с помощью операции изменения размера.  Вместо этого нужно выполнить указанные ниже действия. 

* Остановите виртуальную машину и отмените ее выделение. В случае с группой доступности или масштабируемым набором виртуальных машин сделайте это со всеми виртуальными машинами в группе или наборе.
* Отключите ускоренную сеть в сетевом адаптере виртуальной машины.В случае с группой доступности или масштабируемым набором виртуальных машин сделайте это со всеми виртуальными машинами в группе или наборе.
* После отключения ускоренной сети можно изменить размер виртуальной машины, группы доступности или масштабируемого набора виртуальных машин на новый, который не поддерживает ускоренную сеть, а затем перезапустить виртуальные машины.  



