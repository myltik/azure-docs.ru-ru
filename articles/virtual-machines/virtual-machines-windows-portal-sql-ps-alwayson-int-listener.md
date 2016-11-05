---
title: Настройка прослушивателей групп доступности AlwaysOn | Microsoft Docs
description: Настройте прослушиватели группы доступности в модели Azure Resource Manager с использованием внутреннего балансировщика нагрузки с одним или несколькими IP-адресами.
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: monicar

ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: MikeRayMSFT

---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manger"></a>Настройка одного или нескольких прослушивателей групп доступности Always On в модели Resource Manger
В этой статье вы узнаете, как выполнить два действия:

* создать внутренний балансировщик нагрузки для групп доступности SQL Server с помощью командлетов PowerShell;
* добавить дополнительные IP-адреса к балансировщику нагрузки для поддержки нескольких групп доступности SQL Server. 

> **Важно** Поддержка нескольких прослушивателей для групп доступности AlwaysOn в виртуальных машинах Azure доступна в предварительной версии. Использование этой функции регулируется условиями предварительной версии, указанными в лицензионном соглашении (например, в соглашениях Enterprise, Microsoft Azure или Microsoft Online Subscription), а также всеми действующими [Дополнительными условиями использования предварительных версий Microsoft Azure](http://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> 
> 

Прослушиватель группы доступности в SQL Server — это виртуальная сеть, к которой подключаются клиенты для доступа к базе данных в первичной или вторичной реплике. На виртуальных машинах Azure подсистема балансировки нагрузки содержит IP-адрес прослушивателя. Балансировщик нагрузки направляет трафик в экземпляр SQL Server, который прослушивает порт пробы. В большинстве случаев группа доступности использует внутренний балансировщик нагрузки. В такой системе можно разместить один или несколько IP-адресов. Каждый IP-адрес использует определенный порт пробы. В этой статье описывается, как использовать PowerShell для создания балансировщика нагрузки или добавления IP-адресов в имеющийся балансировщик нагрузки для групп доступности SQL Server. 

Возможность назначения нескольких IP-адресов для внутреннего балансировщика нагрузки впервые представлена в Azure. Она доступна только в модели Resource Manager. Для этого необходимо развернуть группу доступности SQL Server на виртуальных машинах Azure с использованием модели Resource Manager. Обе виртуальные машины SQL Server должны принадлежать к одной и той же группе доступности. Вы можете использовать [шаблон Майкрософт](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) , чтобы автоматически создать группу доступности в Azure Resource Manager. Этот шаблон также автоматически создает внутренний балансировщик нагрузки. При необходимости можно [вручную настроить группу доступности AlwaysOn](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Здесь предполагается, что группы доступности уже настроены.  

Связанные разделы включают:

* [Настройка групп доступности AlwaysOn в виртуальной машине Azure (графический пользовательский интерфейс)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
* [Настройка подключения между виртуальными сетями с помощью Azure Resource Manager и PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [Start your PowerShell session](../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>Настройка брандмауэра Windows
Настройте брандмауэр Windows для разрешения доступа к SQL Server. В брандмауэре необходимо разрешить TCP-подключения к портам, используемым в экземпляре SQL Server, а также к порту пробы прослушивателя. Дополнительные сведения см. в разделе [Настройка брандмауэра Windows для доступа к ядру СУБД](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Создайте правило входящего трафика для порта SQL Server и порта пробы.

## <a name="example-script:-create-an-internal-load-balancer-with-powershell"></a>Пример сценария. Создание внутреннего балансировщика нагрузки с помощью PowerShell
> [!NOTE]
> Если вы создали группу доступности с помощью [шаблона Майкрософт](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) , вы можете пропустить этот шаг. 
> 
> 

Следующий сценарий PowerShell создает внутренний балансировщик нагрузки, задает IP-адрес для нее, а также настраивает правила балансировки нагрузки. Для запуска сценария откройте интегрированную среду сценариев Windows PowerShell и вставьте его в область сценариев. Чтобы войти в PowerShell, используйте `Login-AzureRMAccount` . Если подписок Azure несколько, для установки подписки используется `Select-AzureRmSubscription ` . 

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

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the Front End configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the Back End configuration

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

## <a name="example-script:-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a>Пример сценария. Добавление IP-адреса в имеющийся балансировщик нагрузки с помощью PowerShell
Чтобы применить несколько групп доступности, используйте PowerShell для добавления другого IP-адреса в имеющийся балансировщик нагрузки. Каждому IP-адресу требуется собственное правило балансировки нагрузки, порт пробы и внешний порт.

Внешний порт — это порт, используемый приложениями для подключения к экземпляру SQL Server. Один и тот же внешний порт может использоваться для IP-адресов различных групп доступности.

> [!NOTE]
> Для групп доступности SQL Server для каждого IP-адреса требуется определенный порт пробы. Например, если один IP-адрес в балансировщике нагрузки использует порт пробы 59999, другие IP-адреса не могут использовать этот порт пробы. 
> 
> 

* Дополнительные сведения об ограничениях см. в разделе **Частный внешний IP-адрес для балансировщика нагрузки** статьи [Сетевые ограничения — Azure Resource Manager](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Дополнительные сведения об ограничениях групп доступности см. в разделе [Ограничения (группы доступности)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

Следующий сценарий добавляет новый IP-адрес в имеющийся балансировщик нагрузки. Обновите переменные для среды. Внутренний балансировщик нагрузки использует порт прослушивателя для внешнего порта балансировки нагрузки. Это может быть порт, который прослушивает SQL Server. Для экземпляров SQL Server по умолчанию — это порт 1433. Для правила балансировки нагрузки группы доступности требуется плавающий IP-адрес (прямой ответ от сервера), поэтому внутренний порт не отличается от внешнего порта.

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



## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Настройка использования IP-адреса балансировщика нагрузки для кластера
Теперь необходимо настроить прослушиватель в кластере и подключить его. Для этого сделайте следующее: 

1. Создайте прослушиватель группы доступности в отказоустойчивом кластере. 
2. Подключение прослушивателя

## <a name="1.-create-the-availability-group-listener-on-the-failover-cluster"></a>1. Создание прослушивателя группы доступности в отказоустойчивом кластере
На этом шаге точка доступа клиента добавляется в отказоустойчивый кластер с помощью диспетчера отказоустойчивости кластеров, а затем настраивается ресурс кластера для прослушивания порта пробы с использованием PowerShell. 

* Подключитесь по протоколу RDP к виртуальной машине Azure, на которой размещена основная реплика. 
* Откройте диспетчер отказоустойчивости кластеров.
* Выберите узел **Сети** и запишите имя сети кластера. Это имя нужно использовать в переменной `$ClusterNetworkName` в сценарии PowerShell.
* Разверните имя кластера и нажмите кнопку **Роли**.
* На панели **Роли** щелкните правой кнопкой мыши имя группы доступности и выберите **Добавить ресурс** > **Точка доступа клиента**.
* В поле **Имя** создайте имя для этого нового прослушивателя, а затем дважды щелкните **Далее** и нажмите кнопку **Готово**. Не подключайте прослушивателя или ресурс на этом этапе.
  
  > [!NOTE]
  > Имя для нового прослушивателя — это имя сети, которое будут использовать приложения для подключения к базам данных в группе доступности SQL Server.
  > 
  > 
* Перейдите на вкладку **Ресурсы** и разверните созданную точку доступа клиента. Щелкните правой кнопкой мыши ресурс IP-адреса и выберите пункт "Свойства". Запишите IP-адрес. Это имя будет использоваться в переменной `$IPResourceName` в сценарии PowerShell.
* В разделе **IP Address** click **Статический IP-адрес** and set the static IP-адрес to the same address that you used when you set the load balancer IP-адрес on the Azure portal. 
* Отключите протокол NetBIOS для этого адреса и нажмите кнопку **ОК**. Повторите этот шаг для каждого IP-ресурса, если решение распространяется на несколько виртуальных сетей Azure. 
* Сделайте так, чтобы ресурс группы доступности SQL Server зависел от IP-адреса. Щелкните правой кнопкой мыши ресурс в диспетчере кластера (на вкладке **Ресурсы** в разделе **Другие ресурсы**). 
* В узле кластера, в котором находится основная реплика, откройте интегрированную среду сценариев PowerShell с повышенными привилегиями и вставьте в новый сценарий следующие команды: На вкладке **Зависимости** щелкните имя прослушивателя. 
  
    ```powershell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>
  
    Import-Module FailoverClusters
  
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```
* Обновите переменные и запустите сценарий PowerShell, чтобы настроить IP-адрес и порт для нового прослушивателя.
  
  > [!NOTE]
  > Если серверы SQL Server находятся в разных регионах, необходимо дважды запустить сценарий PowerShell. В первый раз используйте сетевое имя кластера, имя ресурса IP-адреса кластера и IP-адрес балансировщика нагрузки из первой группы ресурсов. Во второй раз используйте сетевое имя кластера, имя ресурса IP-адреса кластера и IP-адрес балансировщика нагрузки из второй группы ресурсов.
  > 
  > 

Теперь у кластера есть ресурс прослушивателя группы доступности.

## <a name="2.-bring-the-listener-online"></a>2) Подключение прослушивателя
С помощью настроенного ресурса прослушивателя группы доступности можно подключить прослушиватель, чтобы приложения могли подключаться через него к базам данных в группе доступности.

* Перейдите обратно в диспетчер отказоустойчивости кластеров. Разверните **Роли** и выделите свою группу доступности. На вкладке **Ресурсы** щелкните правой кнопкой мыши имя прослушивателя и выберите **Свойства**.
* Перейдите на вкладку **Зависимости** . Если в списке несколько ресурсов, убедитесь, что IP-адреса имеют зависимости OR, а не AND. Нажмите кнопку **ОК**.
* Щелкните правой кнопкой мыши имя прослушивателя и выберите **Подключить**.
* После подключения прослушивателя на вкладке **Ресурсы** щелкните правой кнопкой мыши группу доступности и выберите **Свойства**.
* Создайте зависимость для ресурса имени прослушивателя (не имени ресурсов IP адреса). Нажмите кнопку **ОК**.
* Запустите SQL Server Management Studio и подключитесь к основной реплике.
* Перейдите в раздел **Высокий уровень доступности AlwaysOn** | **Группы доступности** | **Прослушиватели группы доступности**. 
* Теперь вы увидите имя прослушивателя, созданного в диспетчере отказоустойчивости кластеров. Щелкните правой кнопкой мыши имя прослушивателя и выберите **Свойства**.
* В поле **Порт** укажите номер порта для прослушивателя группы доступности с помощью использованного ранее параметра $EndpointPort (по умолчанию использовалось значение 1433) и нажмите кнопку **ОК**.

Теперь у вас есть группа доступности SQL Server на виртуальных машинах Azure в режиме Resource Manager. 

## <a name="test-the-connection-to-the-listener"></a>Проверка подключения к прослушивателю
Чтобы проверить подключение:

1. Подключитесь по протоколу RDP к серверу SQL Server, который находится в той же виртуальной сети, но не содержит реплику. Это может быть другой сервер SQL Server в кластере.
2. Для проверки подключения используйте служебную программу **sqlcmd** . Например, в следующем сценарии **sqlcmd** подключается к основной реплике через прослушиватель с использованием аутентификации Windows.
   
        sqlmd -S <listenerName> -E
   
    Если прослушиватель использует порт, отличный от порта по умолчанию (1433), укажите порт в строке подключения. Например, следующая команда sqlcmd подключается к прослушивателю через порт 1435. 
   
        sqlcmd -S <listenerName>,1435 -E

sqlcmd автоматически подключается к любому экземпляру SQL Server, на котором размещена основная реплика. 

> [!NOTE]
> Указанный порт должен быть открыт в брандмауэре обоих серверов SQL Server. Для обоих серверов требуется правило для входящего трафика используемого TCP-порта. Дополнительные сведения см. в статье [Добавление и изменение правила брандмауэра](http://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

## <a name="guidelines-and-limitations"></a>Рекомендации и ограничения
Обратите внимание на следующие рекомендации относительно прослушивателя группы доступности в Azure, использующего внутренний балансировщик нагрузки.

* С помощью внутреннего балансировщика нагрузки можно получить доступ только к прослушивателю, который находится в той же виртуальной сети.

## <a name="for-more-information"></a>Дополнительные сведения
Дополнительные сведения см. в статье [Ручная настройка группы доступности AlwaysOn на виртуальной машине Azure в модели Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

### <a name="powershell-cmdlets"></a>Командлеты PowerShell
Для создания внутреннего балансировщика нагрузки на виртуальных машинах Azure можно использовать следующие командлеты PowerShell.

* `New-AzureRmLoadBalancer` создает балансировщик нагрузки. Дополнительные сведения см. в разделе [New-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt619450.aspx). 
* `New-AzureRMLoadBalancerFrontendIpConfig` создает конфигурацию внешних IP-адресов для балансировщика нагрузки. Дополнительные сведения см. в разделе [New-AzureRMLoadBalancerFrontendIpConfig](http://msdn.microsoft.com/library/mt603510.aspx).
* `New-AzureRmLoadBalancerRuleConfig` создает конфигурацию правила для балансировщика нагрузки. Дополнительные сведения см. в разделе [New-AzureRmLoadBalancerRuleConfig](http://msdn.microsoft.com/library/mt619391.aspx). 
* `New-AzureRMLoadBalancerBackendAddressPoolConfig` создает серверный пул адресов для балансировщика нагрузки. Дополнительные сведения см. в разделе [New-AzureRmLoadBalancerBackendAddressPoolConfig](http://msdn.microsoft.com/library/mt603791.aspx). 
* `New-AzureRmLoadBalancerProbeConfig` создает конфигурацию пробы для балансировщика нагрузки. Дополнительные сведения см. в разделе [New-AzureRmLoadBalancerProbeConfig](http://msdn.microsoft.com/library/mt603847.aspx).

Если необходимо удалить балансировщик нагрузки из группы ресурсов Azure, используйте `Remove-AzureRmLoadBalancer`. Дополнительные сведения см. в разделе [Remove-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx).

<!--HONumber=Oct16_HO2-->


