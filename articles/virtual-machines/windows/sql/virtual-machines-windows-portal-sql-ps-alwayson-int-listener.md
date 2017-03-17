---
title: "Настройка прослушивателей групп доступности AlwaysOn в Microsoft Azure | Документы Майкрософт"
description: "Настройте прослушиватели группы доступности в модели Azure Resource Manager с использованием внутреннего балансировщика нагрузки с одним или несколькими IP-адресами."
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/28/2016
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 1430807db46326779866f57bca3982e5f9448951
ms.lasthandoff: 03/07/2017


---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Настройка одного или нескольких прослушивателей групп доступности AlwaysOn в модели Resource Manager
В этой статье вы узнаете, как:

* создать внутренний балансировщик нагрузки для групп доступности SQL Server с помощью командлетов PowerShell;
* добавить дополнительные IP-адреса в подсистему балансировки нагрузки для поддержки нескольких групп доступности. 

Прослушиватель группы доступности — это виртуальная сеть, к которой подключаются клиенты, чтобы получить доступ к базе данных. На виртуальных машинах Azure балансировщик нагрузки содержит IP-адрес прослушивателя. Балансировщик нагрузки направляет трафик в экземпляр SQL Server, который прослушивает порт пробы. Обычно группа доступности использует внутреннюю подсистему балансировки нагрузки. В такой системе можно разместить один или несколько IP-адресов. Каждый IP-адрес использует определенный порт пробы. В этой статье описывается, как использовать PowerShell для создания подсистемы балансировки нагрузки или добавления IP-адресов в имеющуюся подсистему балансировки нагрузки для групп доступности SQL Server. 

Возможность назначения нескольких IP-адресов для внутреннего балансировщика нагрузки впервые представлена в Azure. Она доступна только в модели Resource Manager. Для этого необходимо развернуть группу доступности SQL Server на виртуальных машинах Azure с использованием модели Resource Manager. Обе виртуальные машины SQL Server должны принадлежать к одной и той же группе доступности. Вы можете использовать [шаблон Майкрософт](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) , чтобы автоматически создать группу доступности в Azure Resource Manager. Этот шаблон также автоматически создает внутренний балансировщик нагрузки. При необходимости можно [вручную настроить группу доступности AlwaysOn](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Здесь предполагается, что группы доступности уже настроены.  

Связанные разделы включают:

* [Настройка групп доступности AlwaysOn в виртуальной машине Azure (графический пользовательский интерфейс)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
* [Настройка подключения между виртуальными сетями с помощью Azure Resource Manager и PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>Настройка брандмауэра Windows
Настройте брандмауэр Windows для разрешения доступа к SQL Server. Правила брандмауэра разрешают TCP-подключения к портам, используемым экземпляром SQL Server и пробой прослушивателя. Дополнительные сведения см. в разделе [Настройка брандмауэра Windows для доступа к ядру СУБД](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Создайте правило входящего трафика для порта SQL Server и порта пробы.

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Пример сценария. Создание внутреннего балансировщика нагрузки с помощью PowerShell
> [!NOTE]
> Если вы создали группу доступности с помощью [шаблона Майкрософт](virtual-machines-windows-portal-sql-alwayson-availability-groups.md), внутренняя подсистема балансировки нагрузки уже создана. 
> 
> 

Следующий сценарий PowerShell создает внутренний балансировщик нагрузки, задает IP-адрес для нее, а также настраивает правила балансировки нагрузки. Для запуска сценария откройте интегрированную среду сценариев Windows PowerShell и вставьте его в область сценариев. Войдите в PowerShell, используя командлет `Login-AzureRMAccount`. Если подписок Azure несколько, для установки подписки используется `Select-AzureRmSubscription ` . 

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzureRMLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="Add-IP"></a> Пример сценария. Добавление IP-адреса в имеющуюся подсистему балансировки нагрузки с помощью PowerShell
Чтобы применить несколько групп доступности, добавьте другой IP-адрес в подсистему балансировки нагрузки. Каждому IP-адресу требуется собственное правило балансировки нагрузки, порт пробы и внешний порт.

Внешний порт — это порт, используемый приложениями для подключения к экземпляру SQL Server. Один и тот же внешний порт может использоваться для IP-адресов различных групп доступности.

> [!NOTE]
> Для групп доступности SQL Server для каждого IP-адреса требуется определенный порт пробы. Например, если один IP-адрес в балансировщике нагрузки использует порт пробы 59999, другие IP-адреса не могут использовать этот порт пробы.

* Дополнительные сведения об ограничениях см. в пункте **Частный внешний IP-адрес на балансировщик нагрузки** раздела [Ограничения сети — Azure Resource Manager](../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Дополнительные сведения об ограничениях групп доступности см. в разделе [Ограничения (группы доступности)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

Следующий сценарий добавляет новый IP-адрес в имеющийся балансировщик нагрузки. Внутренняя подсистема балансировки нагрузки использует порт прослушивателя для внешнего порта балансировки нагрузки. Это может быть порт, который прослушивает SQL Server. Для экземпляров SQL Server по умолчанию — это порт 1433. Для правила балансировки нагрузки группы доступности требуется плавающий IP-адрес (прямой ответ от сервера), поэтому внутренний порт не отличается от внешнего порта. Обновите переменные для среды. 

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzureRmLoadBalancer 

$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzureRmLoadBalancer   
```

## <a name="configure-the-listener"></a>Настройка прослушивателя

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]


<!------------------------------- The content below is duplicated. Pointing to the link. Thinking about an include. 

## Configure the cluster to use the load balancer IP address
The next step is to configure the listener on the cluster, and bring the listener online. To accomplish this, do the following: 

1. Create the availability group listener on the failover cluster  
2. Bring the listener online

## Create the availability group listener on the failover cluster

The availability group listener is an IP address and network name that the SQL Server availability group listens on. To create the availability group listener, do the following steps:

1. [Get the name of the cluster network resource](#getnet).

1. [Add the client access point](#addcap).

1. [Configure the IP resource for the availability group](#congroup).

1. [Make the availability group resource dependent on the listener resource name](#listname).

1. [Set the cluster parameters in PowerShell](#setparam).

The following sections provide detailed instructions for each of these steps. 

### <a name="getnet">Get the name of the cluster network resource</a> 

1. Use RDP to connect to the Azure virtual machine that hosts the primary replica. 

1. Open Failover Cluster Manager.

1. Select the **Networks** node, and note the cluster network name. Use this name in the `$ClusterNetworkName` variable in the PowerShell script.

### <a name="addcap">Add the client access point</a>

1. Expand the cluster name, and then click **Roles**.

1. In the **Roles** pane, right-click the availability group name and then select **Add Resource** > **Client Access Point**.

1. In the **Name** box, create a name for this new listener. 

   The name for the new listener is the network name that applications will use to connect to databases in the SQL Server availability group.
   
   To finish creating the listener, click **Next** twice, and then click **Finish**. Do not bring the listener or resource online at this point.
   
### <a name="congroup">Configure the IP resource for the availability group</a>

1. Click the **Resources** tab, then expand the Client Access Point you just created. Right-click the IP resource and click properties. Note the name of the IP address. You will use this name in the `$IPResourceName` variable in the PowerShell script.

1. Under **IP Address** click **Static IP Address** and set the static IP address to the same address that you used when you set the load balancer IP address on the Azure portal. 

1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 

### <a name="listname">Make the availability group resource dependent on the listener resource</a>

1. In Failover Cluster Manager click **Roles** and click your Availability Group. 

1. On the **Resources** tab, right-click the availability resource group and click **Properties**. 

1. Click the **Dependencies** tab. Set a dependency on the listener resource name. If there are multiple resources listed, verify that the IP addresses have OR, not AND, dependencies. Click **OK**. 

1. Right-click the listener name and click **Bring Online**. 

### <a name="setparam">Set the cluster parameters in PowerShell</a>

Set the cluster parameters. To do this, update the following PowerShell script. Set the variables with the values for your environment. Run the PowerShell script on one of the cluster nodes.  
    
   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```
> [!NOTE]
> If your SQL Servers are in separate regions, you need to run the PowerShell script twice. The first time use the `$ILBIP` and `$ProbePort` from the first region. The second time, use the `$ILBIP` and `$ProbePort` from the second region. The cluster network name, and the cluster IP resource name are the same. 

## Set the listener port in SQL Server Management Studio

1. Launch SQL Server Management Studio and connect to the primary replica.

1. Navigate to **AlwaysOn High Availability** | **Availability Groups** | **Availability Group Listeners**. 

1. You should now see the listener name that you created in Failover Cluster Manager. Right-click the listener name and click **Properties**.

1. In the **Port** box, specify the port number for the availability group listener by using the $EndpointPort you used earlier (1433 was the default), then click **OK**.

You now have a SQL Server availability group in Azure virtual machines running in Resource Manager mode. 
-------------------------------->

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Настройка порта прослушивателя в SQL Server Management Studio

1. Запустите SQL Server Management Studio и подключитесь к основной реплике.

1. Перейдите в раздел **Высокий уровень доступности AlwaysOn** | **Группы доступности** | **Прослушиватели группы доступности**. 

1. Теперь вы увидите имя прослушивателя, созданного в диспетчере отказоустойчивости кластеров. Щелкните правой кнопкой мыши имя прослушивателя и выберите **Свойства**.

1. В поле **Порт** укажите номер порта для прослушивателя группы доступности с помощью использованного ранее параметра $EndpointPort (по умолчанию использовалось значение&1433;) и нажмите кнопку **ОК**.

## <a name="test-the-connection-to-the-listener"></a>Проверка подключения к прослушивателю

Чтобы проверить подключение:

1. Подключитесь по протоколу RDP к серверу SQL Server, который находится в той же виртуальной сети, но не содержит реплику. Это может быть другой сервер SQL Server в кластере.

1. Для проверки подключения используйте служебную программу **sqlcmd** . Например, в следующем сценарии **sqlcmd** подключается к основной реплике через прослушиватель с использованием аутентификации Windows.
   
    ```
    sqlmd -S <listenerName> -E
    ```
   
    Если прослушиватель использует порт, отличный от порта по умолчанию (1433), укажите порт в строке подключения. Например, следующая команда sqlcmd подключается к прослушивателю через порт 1435. 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

sqlcmd автоматически подключается к любому экземпляру SQL Server, на котором размещена основная реплика. 

> [!NOTE]
> Указанный порт должен быть открыт в брандмауэре обоих серверов SQL Server. Для обоих серверов требуется правило для входящего трафика используемого TCP-порта. Дополнительные сведения см. в статье [Добавление и изменение правила брандмауэра](http://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

## <a name="guidelines-and-limitations"></a>Рекомендации и ограничения
Обратите внимание на следующие рекомендации относительно прослушивателя группы доступности в Azure, использующего внутренний балансировщик нагрузки Azure.

* Если вы используете внутреннюю подсистему балансировки нагрузки, доступ к прослушивателю можно получить только из этой же виртуальной сети.


## <a name="for-more-information"></a>Дополнительные сведения
Дополнительные сведения см. в статье [Введение в группы доступности AlwaysOn SQL Server на виртуальных машинах Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

## <a name="powershell-cmdlets"></a>Командлеты PowerShell
Для создания внутреннего балансировщика нагрузки на виртуальных машинах Azure можно использовать следующие командлеты PowerShell.

* Командлет [New-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt619450.aspx) создает подсистему балансировки нагрузки. 
* Командлет [New-AzureRMLoadBalancerFrontendIpConfig](http://msdn.microsoft.com/library/mt603510.aspx) создает конфигурацию внешних IP-адресов для подсистемы балансировки нагрузки. 
* Командлет [New-AzureRmLoadBalancerRuleConfig](http://msdn.microsoft.com/library/mt619391.aspx) создает конфигурацию правила для подсистемы балансировки нагрузки. 
* Командлет [New-AzureRmLoadBalancerBackendAddressPoolConfig](http://msdn.microsoft.com/library/mt603791.aspx) создает конфигурацию внутреннего пула IP-адресов для подсистемы балансировки нагрузки. 
* Командлет [New-AzureRmLoadBalancerProbeConfig](http://msdn.microsoft.com/library/mt603847.aspx) создает конфигурацию пробы для подсистемы балансировки нагрузки.
* Командлет [Remove-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx) удаляет подсистему балансировки нагрузки из группы ресурсов Azure.

