---
title: Создание масштабируемого набора виртуальных машин с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как быстро создать масштабируемый набор виртуальных машин с помощью Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: c5ca90ae303d62c4ad453971f84da78866b0c599
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2018
ms.locfileid: "30201093"
---
# <a name="create-a-virtual-machine-scale-set-with-azure-powershell"></a>Создание масштабируемого набора виртуальных машин с помощью Azure PowerShell
Масштабируемый набор виртуальных машин обеспечивает развертывание и администрирование набора идентичных автомасштабируемых виртуальных машин. Вы можете вручную изменить число виртуальных машин в масштабируемом наборе или определить правила для автоматического масштабирования на основе использования ЦП, объема памяти или сетевого трафика. Следуя инструкциям в этой статье, вы создадите масштабируемый набор виртуальных машин с помощью Azure PowerShell. Вы также можете создать масштабируемый набор с помощью [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md) или [портала Azure](virtual-machine-scale-sets-create-portal.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально, для работы с этим руководством вам понадобится модуль Azure PowerShell 4.4.1 или более поздней версии. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzureRmAccount`, чтобы создать подключение к Azure.


## <a name="create-a-resource-group"></a>Создание группы ресурсов
Прежде чем создать масштабируемый набор, выполните команду [az group create](/powershell/module/azurerm.resources/new-azurermresourcegroup) для создания группы ресурсов. В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *EastUS*:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Location "EastUS"
```


## <a name="create-load-balancer"></a>Создание подсистемы балансировки нагрузки
Экземпляры виртуальных машин в наборе масштабирования подключены к подсистеме балансировки нагрузки. Azure Load Balancer представляет собой балансировщик нагрузки уровня 4 (TCP, UDP), который обеспечивает высокий уровень доступности, распределяя входящий трафик между работоспособными виртуальными машинами. Проба работоспособности позволяет отслеживать данный порт на каждой виртуальной машине и передавать трафик только в рабочую виртуальную машину.

Создайте виртуальную сеть и подсистему балансировки нагрузки с общедоступным IP-адресом, которая распределяет веб-трафик через порт 80. Чтобы создать эти ресурсы, скопируйте и вставьте следующие командлеты PowerShell:

```azurepowershell-interactive
# Create a virtual network subnet
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVnet" `
  -Location "EastUS" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet

# Create a public IP address
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -AllocationMethod Static `
  -Name "myPublicIP"

# Create a frontend and backend IP pool
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"

# Create a Network Address Translation (NAT) pool
$inboundNATPool = New-AzureRmLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389

# Create the load balancer
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName "myResourceGroup" `
  -Name "myLoadBalancer" `
  -Location "EastUS" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool

# Create a load balancer health probe on port 80
Add-AzureRmLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2

# Create a load balancer rule to distribute traffic on port 80
Add-AzureRmLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80

# Update the load balancer configuration
Set-AzureRmLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzureRmVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id
```


## <a name="create-a-scale-set"></a>Создание масштабируемого набора
Теперь создайте масштабируемый набор виртуальных машин с помощью командлета [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). В следующем примере создается масштабируемый набор с именем *myScaleSet*, использующий образ платформы *Windows Server 2016 Datacenter*. Объект *vmssConfig* создает два экземпляра виртуальной машины в регионе "Восточная часть США" с учетными данными, которые указаны в переменных *adminUsername* и *securePassword*. Укажите собственные учетные данные и создайте масштабируемый набор, как показано ниже:

```azurepowershell-interactive
# Provide your own secure password for use with the VM instances
$securePassword = "P@ssword!"
$adminUsername = "azureuser"

# Create a config object
$vmssConfig = New-AzureRmVmssConfig `
    -Location "EastUS" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic

# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher "MicrosoftWindowsServer" `
  -ImageReferenceOffer "WindowsServer" `
  -ImageReferenceSku "2016-Datacenter" `
  -ImageReferenceVersion "latest"

# Set up information for authenticating with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig `
  -AdminUsername $adminUsername `
  -AdminPassword $securePassword `
  -ComputerNamePrefix "myVM"

# Attach the virtual network to the config object
Add-AzureRmVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig

# Create the scale set with the config object (this step might take a few minutes)
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmssConfig
```

Создание и настройка всех ресурсов и виртуальных машин масштабируемого набора занимает несколько минут.


## <a name="install-iis-webserver"></a>Установка веб-сервера IIS
Для проверки масштабируемого набора используйте расширение пользовательского скрипта, чтобы скачать и запустить скрипт, который позволяет установить IIS на экземплярах виртуальных машин. Это расширение можно использовать для настройки после развертывания, установки программного обеспечения и других задач настройки или управления. Дополнительные сведения см. в статье [Расширение Custom Script в ОС Windows](../virtual-machines/windows/extensions-customscript.md).

Примените расширение пользовательского скрипта, которое устанавливает IIS, как показано ниже:

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroup" `
            -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```


## <a name="test-your-web-server"></a>Тестирование веб-сервера
Чтобы изучить работу веб-сервера, получите общедоступный IP-адрес своей подсистемы балансировки нагрузки с помощью командлета [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Следующий пример кода получает IP-адрес, созданный в группе ресурсов *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Введите в браузер общедоступный IP-адрес подсистемы балансировки нагрузки. Подсистема балансировки нагрузки передаст запрос на один из экземпляров виртуальной машины, как показано в следующем примере:

![Выполнение сайта IIS](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Очистка ресурсов
Вы можете удалить ненужную группу ресурсов, масштабируемый набор и все связанные ресурсы с помощью командлета [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup), как показано ниже:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>Дополнительная информация
Следуя инструкциям из этого руководства по началу работы, вы создали простой масштабируемый набор и использовали расширение пользовательского скрипта, чтобы установить базовый веб-сервер IIS на экземплярах виртуальных машин. Следующие руководства помогут вам расширить возможности масштабируемого набора, чтобы оптимизировать масштабируемость и автоматизацию.

- [Развертывание приложения в масштабируемых наборах виртуальных машин](virtual-machine-scale-sets-deploy-app.md)
- Автоматическое масштабирование с помощью [Azure CLI](virtual-machine-scale-sets-autoscale-powershell.md), [Azure PowerShell](virtual-machine-scale-sets-autoscale-cli.md) или [портала Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Автоматические обновления ОС масштабируемого набора виртуальных машин Azure](virtual-machine-scale-sets-automatic-upgrade.md)
