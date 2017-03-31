---
title: "Создание масштабируемых наборов виртуальных машин с помощью командлетов PowerShell | Документация Майкрософт"
description: "Приступите к созданию своего первого масштабируемого набора виртуальных машин Azure и научитесь им управлять с помощью Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 430d9d64-1f35-48f0-a4fd-9b69910ffa59
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: danielsollondon
translationtype: Human Translation
ms.sourcegitcommit: b2e89f0d5e6b14ce8d5847f8adc3d57a6122172e
ms.openlocfilehash: a3a36028a75d6cb7eb36277f3e2b5ab833c96a96
ms.lasthandoff: 02/21/2017


---
# <a name="creating-virtual-machine-scale-sets-using-powershell-cmdlets"></a>Создание масштабируемых наборов виртуальных машин с помощью командлетов PowerShell
В этой статье подробно рассматривается пример того, как создать масштабируемый набор виртуальных машин (VMSS). В нем создается масштабируемый набор из трех узлов со связанными сетью и хранилищем.

## <a name="first-steps"></a>Первые шаги
Убедитесь, что у вас установлен последний модуль Azure PowerShell и доступны командлеты PowerShell, необходимые для обслуживания и создания масштабируемых наборов.
Перейдите к расположенным [здесь](http://aka.ms/webpi-azps) программам командной строки, чтобы узнать о последних доступных модулях Azure.

Чтобы найти связанные с VMSS командлеты, используйте строку поиска \*VMSS\*. Например, _gcm *vmss*_.

## <a name="creating-a-vmss"></a>Создание VMSS
#### <a name="create-resource-group"></a>Создать группу ресурсов
```
$loc = 'westus';
$rgname = 'mynewrgwu';
  New-AzureRmResourceGroup -Name $rgname -Location $loc -Force;
```

### <a name="create-networking-vnet--subnet"></a>Создание сетевой инфраструктуры (виртуальной сети и подсети)
#### <a name="subnet-specification"></a>Спецификация подсети
```
$subnetName = 'websubnet'
  $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix "10.0.0.0/24";
```

#### <a name="vnet-specification"></a>Спецификация виртуальной сети
```
$vnet = New-AzureRmVirtualNetwork -Force -Name ('vnet' + $rgname) -ResourceGroupName $rgname -Location $loc -AddressPrefix "10.0.0.0/16" -Subnet $subnet;
$vnet = Get-AzureRmVirtualNetwork -Name ('vnet' + $rgname) -ResourceGroupName $rgname;

# In this case assume the new subnet is the only one
$subnetId = $vnet.Subnets[0].Id;
```

#### <a name="create-public-ip-resource-to-allow-external-access"></a>Создание ресурса общедоступного IP-адреса для внешнего доступа
Выполняется привязка к подсистеме балансировки нагрузки.

```
$pubip = New-AzureRmPublicIpAddress -Force -Name ('pubip' + $rgname) -ResourceGroupName $rgname -Location $loc -AllocationMethod Dynamic -DomainNameLabel ('pubip' + $rgname);
$pubip = Get-AzureRmPublicIpAddress -Name ('pubip' + $rgname) -ResourceGroupName $rgname;
```

#### <a name="create-load-balancer"></a>Создание балансировщика нагрузки
```
$frontendName = 'fe' + $rgname
$backendAddressPoolName = 'bepool' + $rgname
$probeName = 'vmssprobe' + $rgname
$inboundNatPoolName = 'innatpool' + $rgname
$lbruleName = 'lbrule' + $rgname
$lbName = 'vmsslb' + $rgname

# Bind Public IP to Load Balancer
$frontend = New-AzureRmLoadBalancerFrontendIpConfig -Name $frontendName -PublicIpAddress $pubip
```

#### <a name="configure-load-balancer"></a>Настройка балансировщика нагрузки
Создайте конфигурацию внутреннего пула адресов, которая будет совместно использоваться сетевыми картами виртуальных машин в масштабируемом наборе.

```
$backendAddressPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name $backendAddressPoolName
```

Задайте порт пробы с балансировкой нагрузки и настройте подходящие параметры для приложения.

```
$probe = New-AzureRmLoadBalancerProbeConfig -Name $probeName -RequestPath healthcheck.aspx -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
```

Создайте пул входящих подключений NAT для подключений с прямой маршрутизацией (без балансировки) к виртуальным машинам в масштабируемом наборе с помощью подсистемы балансировки нагрузки. Это нужно для демонстрации использования протокола RDP и может не потребоваться в вашем приложении.

```
$frontendpoolrangestart = 3360
$frontendpoolrangeend = 3370
$backendvmport = 3389
$inboundNatPool = New-AzureRmLoadBalancerInboundNatPoolConfig -Name $inboundNatPoolName -FrontendIPConfigurationId `
$frontend.Id -Protocol Tcp -FrontendPortRangeStart $frontendpoolrangestart -FrontendPortRangeEnd $frontendpoolrangeend -BackendPort $backendvmport;
```

Создайте правило с балансировкой нагрузки. В этом примере показаны запросы через порт 80 для балансировки нагрузки с использованием параметров из предыдущих шагов.

```
$protocol = 'Tcp'
$feLBPort = 80
$beLBPort = 80

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name $lbruleName `
-FrontendIPConfiguration $frontend -BackendAddressPool $backendAddressPool `
-Probe $probe -Protocol $protocol -FrontendPort $feLBPort -BackendPort $beLBPort `
-IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -Verbose;
```

Создайте балансировщик нагрузки с конфигурацией.

```
$actualLb = New-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgname -Location $loc `
-FrontendIpConfiguration $frontend -BackendAddressPool $backendAddressPool `
-Probe $probe -LoadBalancingRule $lbrule -InboundNatPool $inboundNatPool -Verbose;
```

Проверьте параметры балансировки нагрузки и конфигурации портов с балансировкой нагрузки. Помните, что правила NAT для входящего трафика отображаются только после создания виртуальных машин в масштабируемом наборе.

```
$expectedLb = Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgname
```

##### <a name="configure-and-create-the-scale-set"></a>Настройка и создание масштабируемого набора
Обратите внимание, что в этом примере инфраструктуры показано, как настроить распространение и масштабирование веб-трафика в масштабируемом наборе, однако в указанных здесь образах виртуальных машин нет установленных веб-служб.

```
# specify scale set Name
$vmssName = 'vmss' + $rgname;

## specify VMSS specific details
$adminUsername = 'azadmin';
$adminPassword = "Password1234!";

$PublisherName = 'MicrosoftWindowsServer'
$Offer         = 'WindowsServer'
$Sku          = '2012-R2-Datacenter'
$Version       = 'latest'
$vmNamePrefix = 'winvmss'

###add an extension
$extname = 'BGInfo';
$publisher = 'Microsoft.Compute';
$exttype = 'BGInfo';
$extver = '2.1';
```

Привязка сетевой карты к балансировщику нагрузки и подсети

```
$ipCfg = New-AzureRmVmssIPConfig -Name 'nic' `
-LoadBalancerInboundNatPoolsId $actualLb.InboundNatPools[0].Id `
-LoadBalancerBackendAddressPoolsId $actualLb.BackendAddressPools[0].Id `
-SubnetId $subnetId;
```

Создание конфигурации масштабируемого набора

```
# Specify number of nodes
$numberofnodes = 3

$vmss = New-AzureRmVmssConfig -Location $loc -SkuCapacity $numberofnodes -SkuName 'Standard_A2' -UpgradePolicyMode 'automatic' `
    | Add-AzureRmVmssNetworkInterfaceConfiguration -Name $subnetName -Primary $true -IPConfiguration $ipCfg `
    | Set-AzureRmVmssOSProfile -ComputerNamePrefix $vmNamePrefix -AdminUsername $adminUsername -AdminPassword $adminPassword `
    | Set-AzureRmVmssStorageProfile -OsDiskCreateOption 'FromImage' -OsDiskCaching 'None' `
    -ImageReferenceOffer $Offer -ImageReferenceSku $Sku -ImageReferenceVersion $Version `
    -ImageReferencePublisher $PublisherName `
    | Add-AzureRmVmssExtension -Name $extname -Publisher $publisher -Type $exttype -TypeHandlerVersion $extver -AutoUpgradeMinorVersion $true
```

Сборка конфигурации масштабируемого набора

```
New-AzureRmVmss -ResourceGroupName $rgname -Name $vmssName -VirtualMachineScaleSet $vmss -Verbose;
```

Теперь вы создали масштабируемый набор. Подключение к отдельной виртуальной машине можно проверить с помощью протокола удаленного рабочего стола в этом примере:

```
VM0 : pubipmynewrgwu.westus.cloudapp.azure.com:3360
VM1 : pubipmynewrgwu.westus.cloudapp.azure.com:3361
VM2 : pubipmynewrgwu.westus.cloudapp.azure.com:3362
```

