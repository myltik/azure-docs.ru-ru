---
title: Создание виртуальной машины Azure с ускоренной сетью | Документация Майкрософт
description: Узнайте, как создать виртуальную машину Linux с ускоренной сетью.
services: virtual-network
documentationcenter: ''
author: jdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: jimdial
ms.openlocfilehash: 995f40599c059434c419bea95019f8700f756ad8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>Создание виртуальной машины Windows с ускоренной сетью

> [!IMPORTANT]
> Виртуальные машины должны создаваться с включенной функцией ускорения сети. Эту функцию невозможно включить на имеющихся виртуальных машинах. Чтобы включить функцию ускорения сети, сделайте следующее:
>   1. Удаление виртуальной машины
>   2. Повторно создайте виртуальную машину с включенной функцией ускорения сети.
>

В этом руководстве вы узнаете, как создать виртуальную машину Windows с ускоренной сетью. Функция ускорения сети позволяет использовать виртуализацию ввода-вывода с единым корнем (SR-IOV), повышая тем самым производительность сети виртуальной машины. Этот высокопроизводительный метод обеспечивает обход узла на пути к данным, что уменьшает задержку, дрожание и нагрузку ЦП. Он предназначен для ресурсоемких рабочих нагрузок сети на виртуальных машинах поддерживаемых типов. На следующем рисунке приведена схема обмена данными между двумя виртуальными машинами с функцией ускорения сети и без нее:

![Сравнение](./media/create-vm-accelerated-networking/accelerated-networking.png)

Без функции ускорения сети весь входящий и исходящий сетевой трафик виртуальной машины должен проходить через узел и виртуальный коммутатор. Виртуальный коммутатор обеспечивает принудительное применение всех политик, таких как группы безопасности сети, списки управления доступом, изоляция, и использование других служб виртуализации сети для сетевого трафика. Дополнительные сведения о виртуальных коммутаторах см. в статье о [виртуализации сети Hyper-V](https://technet.microsoft.com/library/jj945275.aspx).

При использовании функции ускорения сети трафик поступает в сетевой интерфейс (NIC), а затем перенаправляется на виртуальную машину. Все сетевые политики, которые применяет виртуальный коммутатор, выгружены и применяются на аппаратном уровне. Благодаря применению политик на аппаратном уровне сетевая карта может перенаправлять сетевой трафик непосредственно на виртуальную машину в обход узла и виртуального коммутатора. При этом все политики, примененные на узле, сохраняются.

Возможности функции ускорения сети применяются только к той виртуальной машине, где она включена. Для получения наилучших результатов необходимо включить эту функцию по крайней мере на двух виртуальных машинах, подключенных к одной виртуальной сети Azure. При обмене данными между виртуальными или локальными сетями эта функция практически не влияет на общую задержку.

## <a name="benefits"></a>Преимущества
* **Уменьшение задержки (больше пакетов в секунду).** Благодаря обходу виртуального коммутатора на пути к данным на узле не выполняется обработка политики пакетов. Таким образом, увеличивается число пакетов, которые могут быть обработаны на виртуальной машине.
* **Уменьшение дрожания.** Обработка с помощью виртуального коммутатора зависит от числа политик, которые необходимо применить, и рабочей нагрузки ЦП, выполняющего обработку. Так как принудительное применение политик осуществляется на аппаратном уровне, эта зависимость устраняется за счет доставки пакетов непосредственно на виртуальную машину. Это позволяет избежать обмена данными между узлом и виртуальной машиной, прерываний работы программного обеспечения и переключений контекста.
* **Уменьшение нагрузки ЦП.** Обход виртуального коммутатора на узле приводит к меньшему использованию ЦП для обработки сетевого трафика.

## <a name="supported-operating-systems"></a>Поддерживаемые операционные системы
Microsoft Windows Server 2012 R2 Datacenter и Windows Server 2016.

## <a name="supported-vm-instances"></a>Поддерживаемые экземпляры виртуальных машин
Функция ускорения сети поддерживается для большинства размеров экземпляров, оптимизированных для вычислений, и экземпляров общего назначения с количеством виртуальных ЦП от 4. На таких экземплярах, как D/DSv3 или E/ESv3, поддерживающих технологию Hyper-Threading, функция ускорения сети поддерживается на экземплярах виртуальной машины с количеством виртуальных ЦП от 8. Поддерживаемые серии: D/DSv2, D/DSv3, E-ESv3, F-Fs-Fsv2 и Ms-Mms.

Дополнительные сведения см. в статье [Размеры виртуальных машин Windows в Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="regions"></a>регионы
Доступна во всех общедоступных регионах Azure и облаке Azure для государственных организаций.

## <a name="limitations"></a>Ограничения
При использовании этой возможности действуют следующие ограничения:

* **Создание сетевого интерфейса.** Функцию ускорения сети можно включить только в новом сетевом интерфейсе. Ее нельзя включить в имеющемся сетевом интерфейсе.
* **Создание виртуальной машины.** Сетевой интерфейс с включенной функцией ускорения сети можно подключить к виртуальной машине при ее создании. Этого нельзя сделать для имеющейся виртуальной машины. При добавлении виртуальной машины в имеющуюся группу доступности для всех виртуальных машин в группе доступности нужно включить функцию ускорения сети.
* **Развертывание только с помощью Azure Resource Manager:** классические виртуальные машины нельзя развернуть с включенной функцией ускорения сети.

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
