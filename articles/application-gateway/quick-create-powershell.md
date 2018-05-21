---
title: Краткое руководство. Направление веб-трафика с помощью шлюза приложений Azure в Azure PowerShell | Документация Майкрософт
description: Узнайте, как с помощью Azure PowerShell создать шлюз приложений Azure, который направляет веб-трафик к виртуальным машинам в серверном пуле.
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: azurepowershell
ms.topic: quickstart
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 95b806eaabaf0ba93b1e8b6823340e82842b0f1c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2018
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-powershell"></a>Краткое руководство. Направление веб-трафика с помощью шлюза приложений Azure в Azure PowerShell

Шлюз приложений Azure позволяет направлять веб-трафик приложения к определенным ресурсам. Для этого портам назначаются прослушиватели, создаются определенные правила, и в серверный пул добавляются соответствующие ресурсы.

Из этого краткого руководства вы узнаете, как с помощью портала Azure быстро создать шлюз приложений с двумя виртуальными машинами в серверном пуле. Затем вы протестируете шлюз, чтобы убедиться, что он работает правильно.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Если вы решили установить и использовать PowerShell локально, то для работы с этим руководством вам понадобится модуль Azure PowerShell версии 3.6 или более поздней. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Login-AzureRmAccount`, чтобы создать подключение к Azure.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Для ресурсов обязательно нужно создать группу ресурсов. Создайте группу ресурсов Azure с помощью командлета [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Создание сетевых ресурсов 

Для шлюза приложений нужно создать виртуальную сеть, чтобы он мог обмениваться данными с другими ресурсами. В этом примере создаются две подсети: одна для шлюза приложений, а другая — для внутренних серверов. 

Создайте конфигурации подсетей, выполнив командлет [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Создайте виртуальную сеть с конфигурациями подсетей, используя командлет [New AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Наконец, создайте общедоступный IP-адрес, выполнив командлет [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress).

```azurepowershell-interactive
$backendSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```
## <a name="create-backend-servers"></a>Создание внутренних серверов

В этом примере вы создадите две виртуальные машины, которые будут использоваться как внутренние серверы для шлюза приложений. 

Вы также установите службы IIS на виртуальных машинах, чтобы убедиться, что шлюз приложений успешно создан.

### <a name="create-two-virtual-machines"></a>Создание двух виртуальных машин

Создайте сетевой интерфейс с помощью командлета [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). Создайте конфигурацию виртуальной машины, используя командлет [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). При запуске приведенных ниже команд запрашиваются учетные данные. Введите *azureuser* в качестве имени пользователя и *Azure123456!* в качестве пароля. Создайте виртуальную машину с помощью командлета [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzureRmNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -SubnetId $vnet.Subnets[1].Id
  $vm = New-AzureRmVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2
  $vm = Set-AzureRmVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  $vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  $vm = Add-AzureRmVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  $vm = Set-AzureRmVMBootDiagnostics `
    -VM $vm `
    -Disable
  New-AzureRmVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzureRmVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="create-an-application-gateway"></a>Создание шлюза приложений

### <a name="create-the-ip-configurations-and-frontend-port"></a>Создание IP-конфигураций и интерфейсного порта

Используйте командлет [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration), чтобы создать конфигурацию, которая позволяет связать созданную ранее подсеть со шлюзом приложений. Используйте командлет [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig), чтобы создать конфигурацию, которая позволяет назначить шлюзу приложений созданный ранее общедоступный IP-адрес. Используйте командлет [New-AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendport), чтобы назначить порт 80, который будет использоваться для доступа к шлюзу приложений.

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$pip = Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzureRmApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool"></a>Создание внутреннего пула

Создайте внутренний пул для шлюза приложений с помощью командлета [New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool). Настройте параметры для внутреннего пула, используя командлет [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$address1 = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzureRmApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool `
  -BackendIPAddresses $address1.ipconfigurations[0].privateipaddress, $address2.ipconfigurations[0].privateipaddress
$poolSettings = New-AzureRmApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-add-a-rule"></a>Создание прослушивателя и добавление правила

Прослушиватель требуется для того, чтобы шлюз приложений правильно маршрутизировал трафик на внутренние пулы. Создайте прослушиватель, используя командлет [New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener), с конфигурацией внешнего интерфейса и интерфейсным портом, созданными ранее. Правило требуется для того, чтобы указать прослушивателю, какой внутренний пул использовать для входящего трафика. Используйте командлет [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule), чтобы создать правило с именем *rule1*.

```azurepowershell-interactive
$defaultlistener = New-AzureRmApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Создание шлюза приложений

Теперь, когда вы создали необходимые вспомогательные ресурсы, с помощью командлета [New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku) укажите параметры для шлюза приложений, а затем с помощью командлета [New-AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway) создайте шлюз.

```azurepowershell-interactive
$sku = New-AzureRmApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
New-AzureRmApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

## <a name="test-the-application-gateway"></a>Тестирование шлюза приложений

Для создания шлюза приложений не требуется устанавливать службы IIS. Но в рамках этого руководства они устанавливаются, чтобы проверить, создан ли шлюз приложений. Используйте командлет [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress), чтобы получить общедоступный IP-адрес шлюза приложений. Скопируйте общедоступный IP-адрес и вставьте его в адресную строку браузера.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Тестирование шлюза приложений](./media/quick-create-powershell/application-gateway-iistest.png)

Когда вы обновляете браузер, должно отобразиться имя другой виртуальной машины.

## <a name="clean-up-resources"></a>Очистка ресурсов

Сначала просмотрите ресурсы, созданные вместе со шлюзом приложений. Затем вы можете удалить группу ресурсов, шлюз приложений и все связанные с ними ресурсы, если они больше не требуются. Для этого выполните командлет [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Manage web traffic with an application gateway using Azure PowerShell](./tutorial-manage-web-traffic-powershell.md) (Управление веб-трафиком с помощью шлюза приложений в Azure PowerShell)

