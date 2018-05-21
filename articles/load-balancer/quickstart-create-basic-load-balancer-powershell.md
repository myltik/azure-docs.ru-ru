---
title: Краткое руководство. Создание Load Balancer уровня "Базовый" с помощью Azure PowerShell | Документация Майкрософт
description: В этом кратком руководстве показано, как с помощью PowerShell создать Load Balancer уровня "Базовый"
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
tags: azure-resource-manager
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2018
ms.author: kumud
ms:custom: mvc
ms.openlocfilehash: 2e80a090d003770f47d28dfaacf7ba5140f7b41f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="get-started"></a>Краткое руководство. Создание общедоступной подсистемы балансировки нагрузки с помощью Azure PowerShell
Из этого краткого руководства вы узнаете, как с помощью Azure PowerShell создать Load Balancer уровня "Базовый" Чтобы проверить работу подсистемы балансировки нагрузки, вы развернете две виртуальные машины с Windows Server и распределите между ними нагрузку веб-приложения.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Чтобы установить и использовать PowerShell локально, для работы с этой статьей вам понадобится модуль Azure PowerShell 5.4.1 или более поздней версии. Выполните командлет `Get-Module -ListAvailable AzureRM`, чтобы узнать установленную версию. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzureRmAccount`, чтобы создать подключение к Azure. 

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Прежде чем создавать подсистему балансировки нагрузки, создайте группу ресурсов с помощью командлета [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). В следующем примере создается группа ресурсов с именем *myResourceGroupLB* в расположении *EastUS*:

```azurepowershell-interactive
New-AzureRmResourceGroup `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS"
```
## <a name="create-a-public-ip-address"></a>Создание общедоступного IP-адреса
Для доступа к приложению через Интернет требуется общедоступный IP-адрес для балансировщика нагрузки. Создайте общедоступный IP-адрес с помощью командлета [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). В следующем примере создается общедоступный IP-адрес с именем *myPublicIP* в группе ресурсов *myResourceGroupLB*:

```azurepowershell-interactive
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -AllocationMethod "Dynamic" `
  -Name "myPublicIP"
```
## <a name="create-basic-load-balancer"></a>Создание подсистемы балансировки нагрузки уровня "Базовый"
 В рамках этого раздела вы настроите интерфейсный IP-адрес и серверный пул IP-адресов подсистемы балансировки нагрузки, а затем создадите Load Balancer уровня "Базовый".
 
### <a name="create-frontend-ip"></a>Создание интерфейсного IP-адреса
Создайте интерфейсный IP-адрес с помощью командлета [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig). В следующем примере создается конфигурация с именем *myFrontEnd* для интерфейсного IP-адреса и к ней подключается адрес *myPublicIP*: 

```azurepowershell-interactive
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name "myFrontEnd" `
  -PublicIpAddress $publicIP
```

### <a name="configure-backend-address-pool"></a>Настройка серверного пула адресов

Создайте внутренний пул адресов с помощью командлета [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig). Виртуальные машины присоединяются в этот внутренний пул на следующих шагах. В следующем примере создается внутренний пул адресов *myBackEndPool*.

```azurepowershell-interactive
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
```
### <a name="create-a-health-probe"></a>Создание пробы работоспособности
Чтобы балансировщик нагрузки мог следить за состоянием приложения, необходимо настроить пробу работоспособности. Проба работоспособности динамически добавляет или удаляет виртуальные машины из балансировщика нагрузки на основе их ответа на проверки работоспособности. По умолчанию виртуальная машина удаляется из числа машин, на которые балансировщик распределяет нагрузку, после двух последовательных сбоев с интервалом в 15 секунд. Пробу работоспособности можно создать на основе протокола или конкретной страницы проверки работоспособности приложения. 

В следующем примере создается проба TCP. Вы также можете создать настраиваемую пробу HTTP для более детальных проверок. При использовании настраиваемой пробы HTTP нужно создать страницу проверки работоспособности, например *healthcheck.aspx*. Чтобы обеспечить работоспособность узла, проба должна возвращать ответ **HTTP 200 OK** для балансировщика нагрузки.

Чтобы создать пробу работоспособности TCP, используйте команду [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig). В следующем примере создается проба работоспособности *myHealthProbe*, которая отслеживает каждую виртуальную машину через *HTTP*-порт *80*:

```azurepowershell-interactive
$probe = New-AzureRmLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -RequestPath healthcheck2.aspx `
  -Protocol http `
  -Port 80 `
  -IntervalInSeconds 16 `
  -ProbeCount 2
  ```

### <a name="create-a-load-balancer-rule"></a>Создание правила балансировщика нагрузки
Правило балансировщика нагрузки позволяет определить распределение трафика между виртуальными машинами. Вы определяете интерфейсную конфигурацию IP-адресов для входящего трафика и внутренний пул IP-адресов для приема трафика, а также требуемый порт источника и назначения. Чтобы обеспечить получение трафика только работоспособными виртуальными машинами, можно также определить используемую пробу работоспособности.

Создайте правило балансировщика нагрузки с помощью командлета [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig). В следующем примере создается правило подсистемы балансировки нагрузки *myLoadBalancerRule* для балансировки трафика через *TCP*-порт *80*:

```azurepowershell-interactive
$lbrule = New-AzureRmLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

### <a name="create-the-nat-rules"></a>Создание правил NAT

Создайте правила NAT с помощью командлета [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig). В следующем примере создаются правила NAT с именами *myLoadBalancerRDP1* и *myLoadBalancerRDP2*, разрешающие RDP-подключения к внутренним серверам по портам 4221 и 4222:

```azurepowershell-interactive
$natrule1 = New-AzureRmLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP1' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4221 `
-BackendPort 3389

$natrule2 = New-AzureRmLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP2' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4222 `
-BackendPort 3389
```

### <a name="create-load-balancer"></a>Создание подсистемы балансировки нагрузки

Теперь создайте Load Balancer уровня "Базовый" с помощью командлета [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer). В приведенном ниже примере создается общедоступный экземпляр службы Load Balancer уровня "Базовый" с именем myLoadBalancer. Для него применяется конфигурация интерфейсного IP-адреса, серверный пул, проверка работоспособности, правило балансировки нагрузки и правила NAT, созданные на предыдущих шагах:

```azurepowershell-interactive
$lb = New-AzureRmLoadBalancer `
-ResourceGroupName 'myResourceGroupLB' `
-Name 'MyLoadBalancer' `
-Location 'eastus' `
-FrontendIpConfiguration $frontendIP `
-BackendAddressPool $backendPool `
-Probe $probe `
-LoadBalancingRule $lbrule `
-InboundNatRule $natrule1,$natrule2
```

## <a name="create-network-resources"></a>Создание сетевых ресурсов
Прежде чем развертывать виртуальные машины и тестировать подсистему балансировки нагрузки, создайте вспомогательные ресурсы, то есть виртуальную сеть и виртуальные сетевые адаптеры. 

### <a name="create-a-virtual-network"></a>Создать виртуальную сеть
Создайте виртуальную сеть с помощью командлета [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). В следующем примере создаются виртуальная сеть *myVnet* и подсеть *mySubnet*.

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-network-security-group"></a>Создание группы безопасности сети
Создайте группу безопасности сети для определения входящих подключений к виртуальной сети.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Создание правила группы безопасности сети для порта 3389
Создайте правило для группы безопасности сети, разрешающее RDP-подключения через порт 3389, с помощью командлета [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig).

```azurepowershell-interactive

$rule1 = New-AzureRmNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 1000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Создание правила группы безопасности сети для порта 80
Создайте правило для группы безопасности сети, разрешающее входящие подключения через порт 80, с помощью командлета [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig).

```azurepowershell-interactive
$rule2 = New-AzureRmNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleHTTP' `
-Description 'Allow HTTP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 2000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>Создание группы безопасности сети

Создайте группу безопасности сети с помощью командлета [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup).

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup`
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'myNetworkSecurityGroup'`
-SecurityRules $rule1,$rule2
```

###<a name="create-nics"></a>Создание сетевых адаптеров
Создайте виртуальные сетевые адаптеры с помощью командлета [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). В приведенном ниже примере создаются два виртуальных сетевых адаптера. (по одной виртуальной сетевой карте для каждой виртуальной машины, используемой приложением). Вы можете в любое время создать дополнительные виртуальные сетевые карты и виртуальные машины и добавить их в балансировщик нагрузки:

```azurepowershell-interactive
# Create NIC for VM1
$nicVM1 = New-AzureRmNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyNic1' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule1 `
-Subnet $vnet.Subnets[0]

# Create NIC for VM2
$nicVM2 = New-AzureRmNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyNic2' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule2 `
-Subnet $vnet.Subnets[0]

```

### <a name="create-virtual-machines"></a>Создание виртуальных машин
Чтобы улучшить высокую доступность приложения, поместите виртуальные машины в группу доступности.

Создайте группу доступности с помощью командлета [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). В следующем примере создается группа доступности *myAvailabilitySet*.

```azurepowershell-interactive
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

Укажите имя и пароль администратора для виртуальной машины с помощью командлета [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Теперь вы можете создать виртуальные машины с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). В приведенном ниже примере создаются две виртуальные машины и обязательные компоненты виртуальной сети, если их еще нет:

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzureRmVm `
        -ResourceGroupName "myResourceGroupLB" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

Параметр `-AsJob` создает виртуальную машину как фоновую задачу, поэтому PowerShell отображает запрос о возврате. Подробные сведения о фоновых заданиях можно просмотреть с помощью командлета `Job`. Создание и настройка двух виртуальных машин занимает несколько минут.

### <a name="install-iis-with-custom-web-page"></a>Установка IIS с пользовательской веб-страницей
 
Установите IIS с пользовательской веб-страницей на обеих виртуальных машинах серверной части. Для этого сделайте следующее:

1. Получите общедоступный IP-адрес Load Balancer. С помощью `Get-AzureRmPublicIPAdress` получите общедоступный IP-адрес Load Balancer.

  ```azurepowershell-interactive
    Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupLB" `
    -Name "myPublicIP" | select IpAddress
  ```
2. Создайте подключение к виртуальной машине VM1 по протоколу удаленного рабочего стола, используя полученный на предыдущем шаге общедоступный IP-адрес. 

  ```azurepowershell-interactive

      mstsc /v:PublicIpAddress:4221  
  
  ```
3. Введите учетные данные для *VM1*, чтобы начать сеанс удаленного рабочего стола.
4. Запустите на VM1 Windows PowerShell и выполните в нем приведенные ниже команды, чтобы установить сервер IIS и изменить стандартный HTM-файл.
    ```azurepowershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello from" + $env:computername)
    ```
5. Закройте RDP-подключение к *myVM1*.
6. Создайте RDP-подключение к *myVM2*, выполнив команду `mstsc /v:PublicIpAddress:4222`. Затем повторите шаг 4 для *VM2*.

## <a name="test-load-balancer"></a>Проверка балансировщика нагрузки
Получите общедоступный IP-адрес своей подсистемы балансировки нагрузки с помощью командлета [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Следующий пример позволяет получить IP-адрес для созданного ранее *myPublicIP*.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myPublicIP" | select IpAddress
```

После этого можно ввести общедоступный IP-адрес в веб-браузер. Отображается веб-сайт, а также имя узла виртуальной машины, на которую балансировщик нагрузки направил трафик, как показано в следующем примере:

![Проверка балансировщика нагрузки](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

Чтобы посмотреть, как подсистема балансировки нагрузки распределяет трафик между двумя виртуальными машинами, на которых выполняется приложение, принудительно обновите страницу в браузере.


## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, выполнив команду [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroupLB
```

## <a name="next-steps"></a>Дополнительная информация
- [Дополнительные сведения об Azure Load Balancer](load-balancer-overview.md)
