---
title: Создание внутренней подсистемы балансировки нагрузки Azure с помощью PowerShell | Документация Майкрософт
description: Узнайте, как создать внутренний балансировщик нагрузки с помощью модуля Azure PowerShell с помощью Azure Resource Manager
services: load-balancer
documentationcenter: na
author: KumudD
manager: jennoc
tags: azure-resource-manager
ms.assetid: c6c98981-df9d-4dd7-a94b-cc7d1dc99369
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 1b599e5b88026c06a6912ede9952497c489b0269
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-internal-load-balancer-by-using-the-azure-powershell-module"></a>Создание внутренней подсистемы балансировки нагрузки с помощью модуля Azure PowerShell

> [!div class="op_single_selector"]
> * [портал Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Интерфейс командной строки Azure](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Шаблон](../load-balancer/load-balancer-get-started-ilb-arm-template.md)


[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="get-started-with-the-configuration"></a>Начало работы с конфигурацией

В этой статье объясняется, как создать внутренний балансировщик нагрузки с помощью Azure Resource Manager и модуля Azure PowerShell. В модели развертывания Resource Manager по отдельности настраиваются объекты, необходимые для создания внутренней подсистемы балансировки нагрузки. После создания и настройки объектов они объединяются для создания подсистемы балансировки нагрузки.

Чтобы развернуть подсистему балансировки нагрузки, необходимо создать следующие объекты:

* Внешний пул IP-адресов: частный IP-адрес для входящего сетевого трафика.
* Пул адресов серверной части: сетевые интерфейсы для приема трафика с балансировкой нагрузки с внешних IP-адресов.
* Правила балансировки нагрузки: конфигурация исходного и локального портов для подсистемы балансировки нагрузки.
* Конфигурация проверки: проверки состояния работоспособности для виртуальных машин.
* Правила NAT для входящих подключений: правила порта для прямого доступа к виртуальным машинам.

Дополнительные сведения о компонентах подсистемы балансировки нагрузки см. в статье [Использование поддержки Azure Resource Manager с Azure Load Balancer](load-balancer-arm.md).

Далее описана настройка балансировщика нагрузки для двух виртуальных машин.

## <a name="set-up-powershell-to-use-resource-manager"></a>Настройка PowerShell для использования Resource Manager

Убедитесь, что установлена последняя версия рабочего модуля Azure PowerShell. Для доступа к подписке Azure среда PowerShell должна быть правильно настроена.

### <a name="step-1-start-powershell"></a>Шаг 1. Запуск PowerShell

Запустите модуль PowerShell для Azure Resource Manager.

```powershell
Connect-AzureRmAccount
```

### <a name="step-2-view-your-subscriptions"></a>Шаг 2. Просмотр подписок

Проверьте доступные подписки Azure.

```powershell
Get-AzureRmSubscription
```

Когда появится запрос на проверку подлинности, введите свои учетные данные.

### <a name="step-3-select-the-subscription-to-use"></a>Шаг 3. Выбор используемой подписки

Выберите подписки Azure, которые будут использоваться для развертывания подсистемы балансировки нагрузки.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4-choose-the-resource-group-for-the-load-balancer"></a>Шаг 4. Выбор группы ресурсов для подсистемы балансировки нагрузки

Создайте группу ресурсов для подсистемы балансировки нагрузки. Если используется существующая группа ресурсов, пропустите этот шаг.

```powershell
New-AzureRmResourceGroup -Name NRP-RG -location "West US"
```

Диспетчер ресурсов Azure требует, чтобы все группы ресурсов указывали расположение. Оно используется в качестве расположения по умолчанию для ресурсов в этой группе ресурсов. Всегда используйте одну группу ресурсов для всех команд, связанных с созданием подсистемы балансировки нагрузки.

В этом примере мы создали группу ресурсов с именем **NRP RG** с расположением "Западная часть США".

## <a name="create-the-virtual-network-and-ip-address-for-the-front-end-ip-pool"></a>Создание виртуальной сети и IP-адреса для интерфейсного пула IP-адресов

Создайте подсеть для виртуальной сети и присвойте ее переменной **$backendSubnet**.

```powershell
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
```

Создайте виртуальную сеть.

```powershell
$vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet
```

При этом будет создана виртуальная сеть, подсеть **LB-Subnet-BE** добавляется в виртуальную сеть **NRPVNet**, а значения присваиваются переменной **$vnet**.

## <a name="create-the-front-end-ip-pool-and-back-end-address-pool"></a>Создание интерфейсного пула IP-адресов и серверного пула адресов

Создайте интерфейсный пул IP-адресов для входящего трафика и серверный пул адресов для приема трафика балансировки нагрузки.

### <a name="step-1-create-a-front-end-ip-pool"></a>Шаг 1. Создание интерфейсного пула IP-адресов

Создайте интерфейсный пул IP-адресов с частным IP-адресом 10.0.2.5 для подсети 10.0.2.0/24. Этот адрес является конечной точкой входящего сетевого трафика.

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id
```

### <a name="step-2-create-a-back-end-address-pool"></a>Шаг 2. Создание серверного пула адресов

Создайте серверный пул адресов для приема входящего трафика из пула интерфейсных IP-адресов.

```powershell
$beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
```

## <a name="create-the-configuration-rules-probe-and-load-balancer"></a>Создание правил конфигурации, проверки и подсистемы балансировки нагрузки

После создания интерфейсного пула IP-адресов и серверного пула адресов задайте правила для ресурса подсистемы балансировки нагрузки.

### <a name="step-1-create-the-configuration-rules"></a>Шаг 1. Создание правил конфигурации

В примере создаются следующие четыре объекта правил:

* Правило NAT для входящего трафика для протокола удаленного рабочего стола (RDP): перенаправляет весь входящий трафик порта 3441 через порт 3389.
* Второе правило NAT для входящего трафика для RDP: перенаправляет весь входящий трафик порта 3442 через порт 3389.
* Правило проверки работоспособности: проверяется состояние работоспособности пути HealthProbe.aspx.
* Правило подсистемы балансировки нагрузки: весь трафик, поступающий на общедоступный порт 80, будет направляться на локальный порт 80 в серверном пуле адресов.

```powershell
$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

### <a name="step-2-create-the-load-balancer"></a>Шаг 2. Создание подсистемы балансировки нагрузки

Создайте подсистему балансировки нагрузки и объедините объекты правил (правила NAT для входящего трафика для удаленного рабочего стола, подсистемы балансировки нагрузки и проверки работоспособности):

```powershell
$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
```

## <a name="create-the-network-interfaces"></a>Создание сетевых интерфейсов

После создания внутренней подсистемы балансировки нагрузки необходимо определить сетевые интерфейсы, к которым могут применяться правила NAT и проверки и которые могут получать сетевой трафик с распределенной нагрузкой. Каждый сетевой интерфейс настраивается отдельно и позже присваивается виртуальной машине.

### <a name="step-1-create-the-first-network-interface"></a>Шаг 1. Создание первого сетевого интерфейса

Определите ресурс виртуальной сети и подсети. Эти значения используются для создания сетевых интерфейсов:

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet
```

Создайте первый сетевой интерфейс с именем **lb-nic1-be**. Назначьте интерфейс серверному пулу подсистемы балансировки нагрузки. Свяжите первое правило NAT для протокола удаленного рабочего стола с этим сетевым интерфейсом:

```powershell
$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]
```

### <a name="step-2-create-the-second-network-interface"></a>Шаг 2. Создание второго сетевого интерфейса

Создайте второй сетевой интерфейс с именем **lb-nic2-be**. Назначьте второй интерфейс тому же серверному пулу внутренней подсистемы балансировки, что и первый интерфейс. Свяжите второй сетевой интерфейс со вторым правилом NAT для протокола удаленного рабочего стола:

```powershell
$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]
```

Проверьте конфигурацию:

    $backendnic1

Параметры должны выглядеть так:

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3-assign-the-nic-to-a-vm"></a>Шаг 3. Назначение сетевого адаптера виртуальной машине

Назначьте сетевой адаптер виртуальной машине с помощью команды `Add-AzureRmVMNetworkInterface`.

Пошаговые инструкции по созданию виртуальной машины и назначению сетевого адаптера см. в статье [Создание виртуальной машины Windows с помощью PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="add-the-network-interface"></a>Добавление сетевого интерфейса

После создания виртуальной машины добавьте сетевой интерфейс.

### <a name="step-1-store-the-load-balancer-resource"></a>Шаг 1. Сохранение ресурса подсистемы балансировки нагрузки

Сохраните ресурс подсистемы балансировки нагрузки в переменную (если вы этого еще не сделали). Мы используем имя переменной **$lb**. В качестве значений атрибутов в скрипте используйте имена ресурсов подсистемы балансировки нагрузки, созданные на предыдущих шагах.

```powershell
$lb = Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG
```

### <a name="step-2-store-the-back-end-configuration"></a>Шаг 2. Сохранение серверной конфигурации

Сохраните серверную конфигурацию в переменной **$backend**.

```powershell
$backend = Get-AzureRmLoadBalancerBackendAddressPoolConfig -name LB-backend -LoadBalancer $lb
```

### <a name="step-3-store-the-network-interface"></a>Шаг 3. Сохранение сетевого интерфейса

Сохраните сетевой интерфейс в другую переменную. Этот интерфейс был создан в разделе "Шаг 1. Создание сетевых интерфейсов". Мы используем имя переменной **$nic1**. Используйте то же имя сетевого интерфейса из предыдущего примера.

```powershell
$nic = Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG
```

### <a name="step-4-change-the-back-end-configuration"></a>Шаг 4. Изменение серверной конфигурации

Измените конфигурацию серверной части в сетевом интерфейсе.

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend
```

### <a name="step-5-save-the-network-interface-object"></a>Шаг 5. Сохранение объекта сетевого интерфейса

Сохраните объект сетевого интерфейса.

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

После добавления интерфейса в серверный пул для сетевого трафика выполняется балансировка нагрузки в соответствии с правилами, настроенными в разделе "Создание правил конфигурации, проверки и подсистемы балансировки нагрузки".

## <a name="update-an-existing-load-balancer"></a>Обновление существующего балансировщика нагрузки

### <a name="step-1-assign-the-load-balancer-object-to-a-variable"></a>Шаг 1. Присваивание объекта подсистемы балансировки нагрузки переменной

Назначьте объект подсистемы балансировки нагрузки (из предыдущего примера) переменной **$slb** с помощью команды `Get-AzureRmLoadBalancer`:

```powershell
$slb = Get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

### <a name="step-2-add-a-nat-rule"></a>Шаг 2. Добавление правила NAT

Добавьте новое правило NAT для входящего трафика в имеющуюся подсистему балансировки нагрузки. Используйте порт 81 для интерфейсного пула и порт 8181 для серверного пула:

```powershell
$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp
```

### <a name="step-3-save-the-configuration"></a>Шаг 3. Сохранение конфигурации

Сохраните новую конфигурацию с помощью команды `Set-AzureLoadBalancer`:

```powershell
$slb | Set-AzureRmLoadBalancer
```

## <a name="remove-an-existing-load-balancer"></a>Удаление имеющейся подсистемы балансировки нагрузки

Удалите подсистему балансировки нагрузки **NRP-LB** в группе ресурсов **NRP-RG** с помощью команды `Remove-AzureRmLoadBalancer`:

```powershell
Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG
```

> [!NOTE]
> Используйте дополнительный параметр **-Force**, чтобы избежать запроса подтверждения удаления.

## <a name="next-steps"></a>Дополнительная информация

* [Настройка режима распределения для подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)
* [Настройка параметров времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)
