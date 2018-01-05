---
title: "Создание виртуальной машины Azure с ускоренной сетью | Документация Майкрософт"
description: "Описание способов создания виртуальной машины Linux с помощью Accelerated сетевых."
services: virtual-network
documentationcenter: 
author: jdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/20/2017
ms.author: jimdial
ms.openlocfilehash: 8c2cc9ef487ee754f904f04e604ef76c3f9e07af
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>Создание виртуальной машины Windows с помощью Accelerated сетевых

В этом учебнике вы узнаете, как для создания виртуальной машины (ВМ) Windows с Accelerated работа по сети. Функция ускорения сети позволяет использовать виртуализацию ввода-вывода с единым корнем (SR-IOV), повышая тем самым производительность сети виртуальной машины. Этот путь высокой производительности обходит узла из пути к данным, уменьшение задержки, флуктуации и использования ЦП, для использования с наиболее ресурсоемких рабочих нагрузок сети в поддерживаемых типов ВМ. На следующем рисунке показаны связи между двумя виртуальными машинами с и без ускорителем сети:

![Сравнение](./media/create-vm-accelerated-networking/accelerated-networking.png)

Без функции ускорения сети весь входящий и исходящий сетевой трафик виртуальной машины должен проходить через узел и виртуальный коммутатор. Виртуальный коммутатор обеспечивает принудительное применение всех политик, таких как группы безопасности сети, списки управления доступом, изоляция, и использование других служб виртуализации сети для сетевого трафика. Дополнительные сведения о виртуальных коммутаторах см. в статье о [виртуализации сети Hyper-V](https://technet.microsoft.com/library/jj945275.aspx).

При использовании функции ускорения сети трафик поступает в сетевой интерфейс (NIC), а затем перенаправляется на виртуальную машину. Все сетевые политики, применяемых виртуального коммутатора теперь прямой и применяется в оборудовании. Благодаря применению политик на аппаратном уровне сетевая карта может перенаправлять сетевой трафик непосредственно на виртуальную машину в обход узла и виртуального коммутатора. При этом все политики, примененные на узле, сохраняются.

Возможности функции ускорения сети применяются только к той виртуальной машине, где она включена. Для получения наилучших результатов необходимо включить эту функцию по крайней мере на двух виртуальных машинах, подключенных к одной виртуальной сети Azure. При обмене данными между виртуальными или локальными сетями эта функция практически не влияет на общую задержку.

## <a name="benefits"></a>Преимущества
* **Уменьшение задержки (больше пакетов в секунду).** Благодаря обходу виртуального коммутатора на пути к данным на узле не выполняется обработка политики пакетов. Таким образом, увеличивается число пакетов, которые могут быть обработаны на виртуальной машине.
* **Уменьшение дрожания.** Обработка с помощью виртуального коммутатора зависит от числа политик, которые необходимо применить, и рабочей нагрузки ЦП, выполняющего обработку. Так как принудительное применение политик осуществляется на аппаратном уровне, эта зависимость устраняется за счет доставки пакетов непосредственно на виртуальную машину. Это позволяет избежать обмена данными между узлом и виртуальной машиной, прерываний работы программного обеспечения и переключений контекста.
* **Уменьшение нагрузки ЦП.** Обход виртуального коммутатора на узле приводит к меньшему использованию ЦП для обработки сетевого трафика.

## <a name="supported-operating-systems"></a>Поддерживаемые операционные системы
Microsoft Windows Server 2012 R2 Datacenter и Windows Server 2016.

## <a name="supported-vm-instances"></a>Поддерживаемые экземпляров виртуальных Машин
Ускорителем сети поддерживается на наиболее общего назначения и размеров оптимизированных вычислительных экземпляров с 4 или более ЦП. На экземплярах как D/DSv3 или E-ESv3, поддерживающие технологию Hyper-Threading Accelerated сети поддерживается в экземплярах виртуальных Машин с 8 или более ЦП. Поддерживаемые рядов: D/серии DSv2, D/DSv3, E-ESv3, F-Fs-Fsv2 и Ms-Mms.

Дополнительные сведения о экземпляров виртуальных Машин см. в разделе [размеры виртуальных Машин Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="regions"></a>регионы
Доступно во всех открытых регионах Azure и государственных облако Azure. 

## <a name="limitations"></a>Ограничения
При использовании этой возможности действуют следующие ограничения:

* **Создание сетевого интерфейса.** Функцию ускорения сети можно включить только в новом сетевом интерфейсе. Ее нельзя включить в имеющемся сетевом интерфейсе.
* **Создание виртуальной машины.** Сетевой интерфейс с включенной функцией ускорения сети можно подключить к виртуальной машине при ее создании. Этого нельзя сделать для имеющейся виртуальной машины. Если добавление ВМ в существующую группу, все виртуальные машины в группу доступности должен также accelerated сети включена.
* **Только при развертывании на диспетчере ресурсов Azure:** с помощью Accelerated сетевых невозможно развернуть виртуальные машины (классические).

## <a name="create-a-virtual-network"></a>Создать виртуальную сеть

Установка [Azure PowerShell](/powershell/azure/install-azurerm-ps) версии 5.1.1 или более поздней версии. Чтобы определить текущую версию, запустите `Get-Module -ListAvailable AzureRM`. Если требуется установка или обновление, установите последнюю версию модуля AzureRM из [коллекции PowerShell](https://www.powershellgallery.com/packages/AzureRM). В сеансе PowerShell войдите в учетную запись Azure с помощью [AzureRmAccount добавить](/powershell/module/AzureRM.Profile/Add-AzureRmAccount).

В следующих примерах замените имена параметров собственными значениями. Примеры имен параметров включены *myResourceGroup*, *myNic*, и *myVM*.

Создайте группу ресурсов с помощью командлета [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). В следующем примере создается группа ресурсов с именем *myResourceGroup* в *centralus* расположение:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Во-первых, создайте конфигурацию подсети с [New AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetworkSubnetConfig). В следующем примере создается подсеть с именем *mySubnet*:

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Создайте виртуальную сеть с [New AzureRmVirtualNetwork](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetwork), с *mySubnet* подсети.

```powershell
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Во-первых, создайте правило группы безопасности сети с [New AzureRmNetworkSecurityRuleConfig](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityRuleConfig).

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

Создание группы безопасности сети с [New AzureRmNetworkSecurityGroup](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityGroup) и назначить *разрешить протокола удаленного рабочего СТОЛА все* правила безопасности. В дополнение к *разрешить протокола удаленного рабочего СТОЛА все* правил сетевой группы безопасности содержит несколько правил по умолчанию. Одно правило по умолчанию отключает все входящий доступ из Интернета, поэтому *разрешить протокола удаленного рабочего СТОЛА все* правило назначается группу безопасности сети, таким образом, можно удаленно подключиться к виртуальной машине после ее создания.

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Связать группу безопасности сети для *mySubnet* подсети с [AzureRmVirtualNetworkSubnetConfig набор](/powershell/module/AzureRM.Network/Set-AzureRmVirtualNetworkSubnetConfig). Правила в группу безопасности сети является эффективным для всех ресурсов, развернутых в подсети.

```powershell
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Создание сетевого интерфейса ускорителем сети.
Создайте общедоступный IP-адрес с помощью командлета [New-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/New-AzureRmPublicIpAddress). Общедоступный IP-адрес не требуется, если для доступа к виртуальной машине из Интернета, но для выполнения шагов в данной статье не планируется, это необходимо.

```powershell
$publicIp = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Создание сетевого интерфейса с [New AzureRmNetworkInterface](/powershell/module/AzureRM.Network/New-AzureRmNetworkInterface) с accelerated сети включена и назначить общедоступный IP-адрес сетевого интерфейса. В следующем примере создается с именем сетевого интерфейса *myNic* в *mySubnet* подсети *myVnet* виртуальную сеть и назначит *myPublicIp*  общедоступный IP-адрес, к нему:

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

Задать учетные данные виртуальной Машины `$cred` переменной с помощью [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

Во-первых, определите ВМ с [New AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). В следующем примере определяется Виртуальную машину с именем *myVM* с размером виртуальной Машины, который поддерживает Accelerated сети (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Список всех размеров виртуальных Машин и характеристики см. в разделе [размеры виртуальных Машин Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

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

Подключить сетевой интерфейс, созданный ранее с [AzureRmVMNetworkInterface добавить](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Наконец, создайте виртуальную машину с помощью [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Убедитесь, что драйвер установлен в операционной системе

После создания виртуальной Машины в Azure, подключитесь к виртуальной Машине и убедитесь, что установлен драйвер в Windows. 

1. Веб-браузер, откройте Azure [портала](https://portal.azure.com) и выполните вход с учетной записью Azure.
2. В поле, содержащее текст *Найдите ресурсы* в верхней части портала Azure, введите *myVm*. Когда **myVm** появится в результатах поиска щелкните его. Если **создание** отображается в группе **Connect** кнопки, Azure не завершено создание виртуальной Машины. Нажмите кнопку **Connect** в левом верхнем углу Обзор только после больше не отображается для **создание** под **Connect** кнопки.
3. Введите имя пользователя и пароль, введенный в [создания виртуальной машины](#create-the-virtual-machine). Если вы не подключились к виртуальной Машине Windows в Azure, см. раздел [подключиться к виртуальной машине](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. В главном меню Windows щелкните правой кнопкой мыши кнопку "Пуск" и выберите **Диспетчер устройств**. Разверните узел **Сетевые адаптеры**. Проверьте, есть ли в списке виртуальный адаптер **Mellanox ConnectX-3 Virtual Function Ethernet Adapter**, как показано на следующем рисунке:
   
    ![Диспетчер устройств](./media/create-vm-accelerated-networking/device-manager.png)

Ускорение работы в сети теперь включено на вашей виртуальной машине.
