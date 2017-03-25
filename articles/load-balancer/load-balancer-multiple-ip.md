---
title: "Балансировка нагрузки в конфигурациях с несколькими IP-адресами | Документация Майкрософт"
description: "Балансировка нагрузки между основной и дополнительной IP-конфигурациями."
services: load-balancer
documentationcenter: na
author: anavinahar
manager: narayan
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2017
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 555f43d6fca2ac6ba53eae94bff22426d667c444
ms.lasthandoff: 03/11/2017

---

# <a name="load-balancing-on-multiple-ip-configurations"></a>Балансировка нагрузки в конфигурациях с несколькими IP-адресами

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-multiple-ip.md)
> * [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](load-balancer-multiple-ip-cli.md)
>

В этой статье описывается, как использовать Azure Load Balancer в конфигурации, в которой у каждого виртуального сетевого интерфейса (сетевой карты) имеется несколько IP-адресов. Поддержка нескольких IP-адресов для сетевой карты в настоящее время находится на этапе предварительной версии. Дополнительные сведения см. в разделе [Ограничения](#limitations) этой статьи. Приведенный ниже сценарий иллюстрирует, как эта функция работает с подсистемой балансировки нагрузки.

В данном сценарии имеются две виртуальные машины под управлением Windows, каждая из которых имеет одну сетевую карту. Для каждой сетевой карты настроено несколько IP-адресов. Каждая виртуальная машина содержит веб-сайты contoso.com и fabrikam.com. Каждый веб-сайт привязан к одной из IP-конфигураций сетевой карты. Мы используем подсистему балансировки нагрузки, чтобы предоставить два интерфейсных IP-адреса, по одному для каждого веб-сайта. Это позволяет направлять трафик в соответствующую IP-конфигурацию для веб-сайта. В данном сценарии используется одинаковый номер порта для обоих внешних интерфейсов, как и для обоих IP-адресов внутренних пулов.

![Схема балансировки нагрузки для сценария](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="limitations"></a>Ограничения

В настоящее время настройка балансировки нагрузки для дополнительных IP-конфигураций возможна только с помощью Azure PowerShell и интерфейса командной строки Azure (Azure CLI). Это ограничение является временным и может быть изменено в любое время. Вернитесь на эту страницу, чтобы проверить наличие новой информации.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

Зарегистрируйтесь для получения предварительной версии, выполнив следующие команды в PowerShell после входа, и выберите соответствующую подписку:

```
Register-AzureRmProviderFeature -FeatureName AllowMultipleIpConfigurationsPerNic -ProviderNamespace Microsoft.Network

Register-AzureRmProviderFeature -FeatureName AllowLoadBalancingonSecondaryIpconfigs -ProviderNamespace Microsoft.Network

Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

Не пытайтесь выполнить остальные шаги, пока не увидите следующий результат при выполнении команды ```Get-AzureRmProviderFeature```:
        
```powershell
FeatureName                            ProviderName      RegistrationState
-----------                            ------------      -----------------      
AllowLoadBalancingOnSecondaryIpConfigs Microsoft.Network Registered       
AllowMultipleIpConfigurationsPerNic    Microsoft.Network Registered       
```
        
>[!NOTE] 
>Это может занять несколько минут.

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Инструкции по балансировке нагрузки в конфигурациях с несколькими IP-адресами

Выполните следующие действия, чтобы реализовать сценарий, описанный в этой статье.

1. Установите Azure PowerShell. Сведения об установке последней версии Azure PowerShell, а также о выборе нужной подписки и входе в учетную запись Azure см. в статье [Установка и настройка служб Azure PowerShell](/powershell/azureps-cmdlets-docs).
2. Создайте группу ресурсов, указав приведенные ниже параметры.

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Чтобы узнать больше, ознакомьтесь с шагом 2 в разделе [Создание группы ресурсов](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

3. [Создайте группу доступности](../virtual-machines/virtual-machines-windows-create-availability-set.md?toc=%2fazure%2fload-balancer%2ftoc.json) для виртуальных машин. В рамках данного сценария воспользуйтесь следующей командой.

    ```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Следуйте инструкциям для шагов 3–5 в разделе [Создание виртуальной машины Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json), чтобы подготовить среду к созданию виртуальной машины с одной сетевой картой. Выполните шаг 6.1, затем вместо шага 6.2 выполните следующую команду.

    ```powershell
    $availset = Get-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzureRmVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    После этого выполните шаги 6.3–6.8 раздела [Создание виртуальной машины Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

5. Добавьте вторую IP-конфигурации в каждую виртуальную машину. Следуйте инструкциям в статье [Назначение виртуальным машинам нескольких IP-адресов](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add). Используйте следующие параметры конфигурации.

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    В рамках данного руководства нет необходимости связывать дополнительные IP-конфигурации с общедоступными IP-адресами. Измените команду, чтобы удалить связывание с общедоступным IP-адресом.

6. Повторите шаги 4–6 данной статьи для VM2. Не забудьте заменить в командах VM на VM2. Обратите внимание, что для второй виртуальной машины не нужно создавать виртуальную сеть. Вы можете создать или не создавать новую подсеть в зависимости от требований ситуации.

7. Создайте два общедоступных IP-адреса и сохраните их в соответствующих переменных, как показано ниже.

    ```powershell
    $publicIP1 = New-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Создайте две интерфейсные IP-конфигурации.

    ```powershell
    $frontendIP1 = New-AzureRmLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Создайте внутренние пулы адресов, пробу и правила балансировки нагрузки.

    ```powershell
    $beaddresspool1 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzureRmLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzureRmLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. После создания этих ресурсов создайте подсистему балансировки нагрузки.

    ```powershell
    $mylb = New-AzureRmLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Добавьте второй внутренний пул адресов и интерфейсную IP-конфигурацию в созданную подсистему балансировки нагрузки.

    ```powershell
    $mylb = Get-AzureRmLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzureRmLoadBalancer

    $mylb | Add-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzureRmLoadBalancer
    
    Add-AzureRmLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzureRmLoadBalancer
    ```

12. Приведенные ниже команды используются для получения сетевых карт и добавления обеих IP-конфигураций каждой дополнительной сетевой карты во внутренний пул адресов подсистемы балансировки нагрузки.

    ```powershell
    $nic1 = Get-AzureRmNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzureRmNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzureRmLoadBalancer

    $nic1 | Set-AzureRmNetworkInterface
    $nic2 | Set-AzureRmNetworkInterface
    ```

13. Наконец, необходимо настроить записи ресурсов DNS, чтобы они указывали на соответствующие интерфейсные IP-адреса подсистемы балансировки нагрузки. Домены можно разместить в Azure DNS. Дополнительные сведения об использовании Azure DNS с подсистемой балансировки нагрузки см. в разделе [Использование Azure DNS с другими службами Azure](../dns/dns-for-azure-services.md).

