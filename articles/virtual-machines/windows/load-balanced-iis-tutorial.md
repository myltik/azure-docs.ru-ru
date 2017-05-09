---
title: "Руководство по созданию высокодоступного приложения, выполняемого на виртуальных машинах Azure | Документация Майкрософт"
description: "Сведения о создании высокодоступного безопасного приложения, выполняемого на трех виртуальных машинах Windows с использованием подсистемы балансировки нагрузки в Azure."
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
ms.date: 03/30/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 61fd0dc45cfa2d320713819be3db811be58ab77e
ms.contentlocale: ru-ru
ms.lasthandoff: 04/27/2017

---

# <a name="build-a-load-balanced-highly-available-application-on-windows-virtual-machines-in-azure"></a>Создание в Azure высокодоступного приложения с балансировкой нагрузки, выполняемого на виртуальных машинах Windows

В этом руководстве описывается создание высокодоступного приложения, устойчивого к сбоям обслуживания. Приложение использует подсистему балансировки нагрузки, группу доступности и три виртуальные машины Windows. В этом руководстве устанавливается IIS. Тем не менее, с его помощью вы можете развернуть другую платформу приложения, используя те же высокодоступные компоненты и рекомендации. 

## <a name="step-1---azure-prerequisites"></a>Шаг 1. Предварительные требования Azure

Для работы с этим руководством необходимо установить последнюю версию модуля [Azure PowerShell](/powershell/azure/overview).

Прежде всего войдите в подписку Azure с помощью команды Login-AzureRmAccount и следуйте инструкциям на экране.

```powershell
Login-AzureRmAccount
```

Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Прежде чем создавать другие ресурсы Azure, необходимо создать группу ресурсов с помощью командлета [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). В следующем примере создается группа ресурсов с именем `myResourceGroup` в регионе `westeurope`: 

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location westeurope
```

## <a name="step-2---create-availability-set"></a>Шаг 2. Создание группы доступности

Виртуальные машины можно создать в логических доменах сбоя и обновления. Каждый логический домен представляет собой часть оборудования в основном центре обработки данных Azure. При создании двух и более виртуальных машин вычислительные ресурсы и ресурсы хранения распределяются по этим доменам. Это позволяет поддерживать доступность приложения, когда компонент оборудования нуждается в обслуживании. Группы доступности позволяют определить эти логические домены сбоя и обновления.

Создайте группу доступности с помощью командлета [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). В следующем примере создается группа доступности с именем `myAvailabilitySet`.

```powershell
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName myResourceGroup `
  -Name myAvailabilitySet `
  -Location westeurope `
  -Managed `
  -PlatformFaultDomainCount 3 `
  -PlatformUpdateDomainCount 2
```

## <a name="step-3---create-load-balancer"></a>Шаг 3. Создание балансировщика нагрузки

Приложение Azure Load Balancer распределяет трафик между набором определенных виртуальных машин с помощью правил балансировщика нагрузки. Проба работоспособности позволяет отслеживать данный порт на каждой виртуальной машине и передавать трафик только в рабочую виртуальную машину.

### <a name="create-public-ip-address"></a>Создание общедоступного IP-адреса

Для доступа к приложению через Интернет назначьте общедоступный IP-адрес для балансировщика нагрузки. Создайте общедоступный IP-адрес с помощью командлета [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). В следующем примере создается общедоступный IP-адрес с именем `myPublicIP`.

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -AllocationMethod Static `
  -Name myPublicIP
```

### <a name="create-load-balancer"></a>Создание подсистемы балансировки нагрузки

Создайте интерфейсный IP-адрес с помощью командлета [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig). В следующем примере создается интерфейсный IP-адрес с именем `myFrontEndPool`: 

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name myFrontEndPool -PublicIpAddress $pip
```

Создайте внутренний пул адресов с помощью командлета [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig). В следующем примере создается внутренний пул адресов с именем `myBackEndPool`:

```powershell
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool
```

Теперь создайте балансировщик нагрузки с помощью командлета [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer). В следующем примере с помощью адреса `myPublicIP` создается балансировщик нагрузки с именем `myLoadBalancer`.

```powershell
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroup `
  -Name myLoadBalancer `
  -Location westeurope `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-health-probe"></a>Создание пробы работоспособности

Чтобы балансировщик нагрузки мог следить за состоянием приложения, необходимо настроить пробу работоспособности. Проба работоспособности динамически добавляет или удаляет виртуальные машины из балансировщика нагрузки на основе их ответа на проверки работоспособности. По умолчанию виртуальная машина удаляется из числа машин, на которые балансировщик распределяет нагрузку, после двух последовательных сбоев с интервалом в 15 секунд.

Создайте пробу работоспособности с помощью командлета [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig). В следующем примере создается проба работоспособности с именем `myHealthProbe`, которая отслеживает каждую виртуальную машину.

```powershell
Add-AzureRmLoadBalancerProbeConfig -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

### <a name="create-load-balancer-rule"></a>Создание правила подсистемы балансировки нагрузки

Правило балансировщика нагрузки позволяет определить распределение трафика между виртуальными машинами.

Создайте правило подсистемы балансировки нагрузки с помощью командлета [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig). В следующем примере создается правило балансировщика нагрузки с именем `myLoadBalancerRule` для балансировки трафика через порт `80`:

```powershell
Add-AzureRmLoadBalancerRuleConfig -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

Обновите подсистему балансировки нагрузки с помощью командлета [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer):

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

## <a name="step-4---configure-networking"></a>Шаг 4. Настройка сети

Каждая виртуальная машина содержит один или несколько виртуальных сетевых адаптеров (NIC), подключенных к виртуальной сети. Эта виртуальная сеть защищена благодаря фильтрации трафика в соответствии с определенными правилами доступа.

### <a name="create-virtual-network"></a>Создание виртуальной сети

Сначала настройте подсеть с помощью командлета [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). В следующем примере создается подсеть с именем `mySubnet`

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
```

Чтобы установить сетевое подключение к виртуальным машинам, создайте виртуальную сеть с помощью командлета [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). В следующем примере создается виртуальная сеть `myVnet` с подсетью `mySubnet`:

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

### <a name="configure-network-security"></a>Настройка безопасности сети

[Группа безопасности сети](../../virtual-network/virtual-networks-nsg.md) Azure управляет входящим и исходящим трафиком одной или нескольких виртуальных машин. Правила группы безопасности сети разрешают или запрещают передачу сетевого трафика на определенный порт или диапазон портов. Эти правила также могут включать префикс адреса источника, чтобы на виртуальную машину передавался только трафик от определенного источника.

Чтобы разрешить получение веб-трафика в приложении, создайте правило группы безопасности сети с помощью командлета [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). В следующем примере создается правило группы безопасности сети с именем `myNetworkSecurityGroupRule`.

```powershell
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

Создайте группу безопасности сети с помощью командлета [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). В следующем примере создается группа безопасности сети с именем `myNetworkSecurityGroup`:

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRule
```

Добавьте группу безопасности сети в подсеть с помощью командлета [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig):

```powershell
Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
  -Name mySubnet `
  -NetworkSecurityGroup $nsg `
  -AddressPrefix 192.168.1.0/24
```

Обновите виртуальную сеть с помощью командлета [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork):

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-network-interface-cards"></a>Создание виртуальных сетевых адаптеров

При работе балансировщиков нагрузки используется сетевой адаптер, связанный с виртуальной, а не с фактической виртуальной машиной. Виртуальный сетевой адаптер подключается к балансировщику нагрузки, а затем к виртуальной машине.

Создайте виртуальную сетевую карту с помощью командлета [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). В следующем примере создаются три виртуальных сетевых адаптера (один виртуальный сетевой адаптер создается для каждой виртуальной машины, используемой приложением).


```powershell
for ($i=1; $i -le 3; $i++)
{
   New-AzureRmNetworkInterface -ResourceGroupName myResourceGroup `
     -Name myNic$i `
     -Location westeurope `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}

```

## <a name="step-5---create-virtual-machines"></a>Шаг 5. Создание виртуальных машин

Теперь, когда все базовые компоненты готовы, можно создать высокодоступные виртуальные машины для запуска приложения. 

Получите имя пользователя и пароль, необходимые для учетной записи администратора на виртуальной машине, с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Создайте виртуальные машины с помощью командлетов [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvmconfig), [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmoperatingsystem), [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmsourceimage), [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmosdisk), [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/add-azurermvmnetworkinterface) и [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). В следующем примере создаются три виртуальные машины:

```powershell
for ($i=1; $i -le 3; $i++)
{
   $vm = New-AzureRmVMConfig -VMName myVM$i -VMSize Standard_D1 -AvailabilitySetId $availabilitySet.Id
   $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName myVM$i -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest
   $vm = Set-AzureRmVMOSDisk -VM $vm -Name myOsDisk$i -StorageAccountType StandardLRS -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
   $nic = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroup -Name myNic$i
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM -ResourceGroupName myResourceGroup -Location westeurope -VM $vm
}

```

Создание и настройка всех трех виртуальных машин занимает несколько минут. Проба работоспособности балансировщика нагрузки автоматически определяет, когда приложение работает на каждой виртуальной машине. Как только приложение будет запущено, правило балансировщика нагрузки начнет распределение трафика.

### <a name="install-the-app"></a>Установка приложения 

Расширения виртуальной машины Azure позволяют автоматизировать задачи по настройке виртуальной машины, например установку приложений и настройку операционной системы. С помощью [расширения пользовательских скриптов для Windows](./../virtual-machines-windows-extensions-customscript.md) можно запустить на виртуальной машине любой скрипт PowerShell. Сценарий может быть сохранен в хранилище Azure или в любой доступной конечной точке HTTP, а также встроен в конфигурацию расширения пользовательских сценариев. При использовании расширения пользовательских сценариев агент виртуальной машины Azure управляет выполнением сценария.

Воспользуйтесь командлетом [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension), чтобы установить расширение пользовательских сценариев. Расширение запускает `powershell Add-WindowsFeature Web-Server` для установки веб-сервера IIS:

```powershell
for ($i=1; $i -le 3; $i++)
{
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
     -ExtensionName IIS `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.4 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
     -Location westeurope
}
```

### <a name="test-your-app"></a>Тестирование приложения

Получите общедоступный IP-адрес своей подсистемы балансировки нагрузки с помощью командлета [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Следующий пример позволяет получить IP-адрес для созданного ранее `myPublicIP`.

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroup -Name myPublicIP | select IpAddress
```

Введите общедоступный IP-адрес в веб-браузер. После создания правила группы безопасности сети отобразится веб-сайт IIS по умолчанию. 

![Сайт IIS по умолчанию](media/load-balanced-iis-tutorial/iis.png)

## <a name="step-6--management-tasks"></a>Шаг 6. Задачи управления

На виртуальных машинах, выполняющих приложение, может потребоваться выполнить обслуживание, например установить обновления операционной системы. Для обработки большего объема трафика в приложении необходимо добавить дополнительные виртуальные машины. В этом разделе показано, как удалить или добавить виртуальную машину из балансировщика нагрузки. 

### <a name="remove-a-vm-from-the-load-balancer"></a>Удаление виртуальной машины из балансировщика нагрузки

Удалите виртуальную машину из внутреннего пула адресов, сбросив свойство LoadBalancerBackendAddressPools сетевой карты.

Получите сетевую карту с помощью командлета [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface):

```powershell
$nic = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroup -Name myNic2
``` 

Задайте свойству LoadBalancerBackendAddressPools сетевой карты значение $null:

```powershell
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
```

Обновите сетевую карту:

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

### <a name="add-a-vm-to-the-load-balancer"></a>Добавление виртуальной машины в балансировщик нагрузки

После выполнения обслуживания виртуальной машины, или если необходимо расширить емкость, добавьте сетевую карту виртуальной машины во внутренний пул адресов подсистемы балансировки нагрузки.

Получите подсистему балансировки нагрузки:

```powershell
$lb = Get-AzureRMLoadBalancer -ResourceGroupName myResourceGroup -Name myLoadBalancer 
```

Добавьте внутренний пул адресов подсистемы балансировки нагрузки в сетевую карту:

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
```

Обновите сетевую карту:

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Дальнейшие действия

[Примеры PowerShell для виртуальной машины Azure](./../virtual-machines-windows-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

