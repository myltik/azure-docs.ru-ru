---
title: "Перенос каналов ExpressRoute и связанных виртуальных сетей из классической модели на модель Azure Resource Manager | Документация Майкрософт"
description: "В этой статье описывается перенос классического канала и связанных виртуальных сетей на модель Resource Manager."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 08152836-23e7-42d1-9a56-8306b341cd91
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: ganesr;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 9a3ad5be7cc12f1c82eab4a8b3089276a434c234
ms.lasthandoff: 03/29/2017


---
# <a name="migrate-expressroute-circuits-and-associated-virtual-networks-from-the-classic-to-the-resource-manager-deployment-model"></a>Перенос каналов ExpressRoute и связанных виртуальных сетей из классической модели развертывания на модель Resource Manager

В этой статье описывается процесс переноса каналов Azure ExpressRoute и связанных виртуальных сетей из классической модели развертывания на модель развертывания с помощью Azure Resource Manager. 


## <a name="before-you-begin"></a>Перед началом работы
* Убедитесь в наличии последней версии модулей Azure PowerShell. Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Не забудьте изучить [предварительные требования](expressroute-prerequisites.md), [требования к маршрутизации](expressroute-routing.md) и [рабочие процессы](expressroute-workflows.md), прежде чем приступать к настройке.
* Просмотрите сведения в статье [Перемещение каналов ExpressRoute из классической модели развертывания в модель развертывания с помощью Resource Manager](expressroute-move.md). Убедитесь, что вам полностью понятны пределы и ограничения.
* Убедитесь, что канал полноценно работает в классической модели развертывания.
* Убедитесь в наличии группы ресурсов, созданной в модели развертывания Resource Manager.
* Ознакомьтесь со следующими статьями о переносе ресурсов:

    * [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [Platform-supported migration of IaaS resources from classic to Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md) (Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager)
    * [Common errors during classic to Azure Resource Manager migration](../virtual-machines/virtual-machines-migration-errors.md) (Распространенные ошибки при миграции из классической модели в модель Azure Resource Manager)

## <a name="supported-and-unsupported-scenarios"></a>Поддерживаемые и неподдерживаемые сценарии

* Канал ExpressRoute можно перенести из классической среды в среду Resource Manager без простоев. Вы можете переместить любой канал ExpressRoute из классической среды в среду Resource Manager без простоев. Дополнительные сведения см. в статье [Перемещение каналов ExpressRoute из классической модели развертывания в модель Resource Manager с помощью PowerShell](expressroute-howto-move-arm.md). Перемещение ресурсов, подключенных к виртуальной сети, является обязательным условием.
* Виртуальные сети, шлюзы и связанные развертывания в виртуальной сети, подключенные к каналу ExpressRoute в одной подписке, можно перенести в среду Resource Manager без простоев. Выполните приведенные ниже действия, чтобы перенести ресурсы, такие как виртуальные сети, шлюзы и виртуальные машины, развернутые в виртуальной сети. Прежде чем приступать к переносу виртуальных сетей, необходимо убедиться, что они настроены правильно. 
* Для переноса виртуальных сетей, шлюзов и связанных развертываний в виртуальной сети, которые принадлежат не к той подписке, где находится канал ExpressRoute, требуется некоторое время простоя. В последнем разделе статьи описываются шаги, которые необходимо выполнить при переносе ресурсов.

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Перемещение канала ExpressRoute из классической модели развертывания в модель развертывания с помощью Resource Manager
Прежде чем приступить к переносу ресурсов, подключенных к каналу ExpressRoute, этот канал необходимо переместить из классической среды в среду Resource Manager. Для этого используйте приведенные ниже ссылки:

* Просмотрите сведения в статье [Перемещение каналов ExpressRoute из классической модели развертывания в модель развертывания с помощью Resource Manager](expressroute-move.md).
* [Перемещение каналов ExpressRoute из классической модели развертывания в модель Resource Manager с помощью PowerShell](expressroute-howto-move-arm.md).
* Используя портал управления службами Azure, выполните рабочий процесс для [создания канала ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) и выберите параметр импорта. 

При этой операции прости не возникают. Вы можете продолжить перемещение данных между локальной средой и Майкрософт во время выполнения миграции.

## <a name="prepare-your-virtual-network-for-migration"></a>Подготовка виртуальной сети к переносу
Необходимо убедиться, что перемещаемая виртуальная сеть не содержит ненужных артефактов. Чтобы скачать конфигурацию виртуальной сети и при необходимости ее обновить, выполните приведенный ниже командлет PowerShell.

    Add-AzureAccount
    Select-AzureSubscription -SubscriptionName <VNET Subscription>
    Get-AzureVNetConfig -ExportToFile C:\virtualnetworkconfig.xml
      
Необходимо убедиться, что все ссылки на <ConnectionsToLocalNetwork> удалены из перемещаемых виртуальных сетей. В следующем фрагменте кода показан пример конфигурации сети:

    <VirtualNetworkSite name="MyVNet" Location="East US">
        <AddressSpace>
            <AddressPrefix>10.0.0.0/8</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="Subnet-1">
                <AddressPrefix>10.0.0.0/11</AddressPrefix>
            </Subnet>
            <Subnet name="GatewaySubnet">
                <AddressPrefix>10.32.0.0/28</AddressPrefix>
            </Subnet>
        </Subnets>
        <Gateway>
            <ConnectionsToLocalNetwork>
            </ConnectionsToLocalNetwork>
        </Gateway>
    </VirtualNetworkSite>
 
Если <ConnectionsToLocalNetwork> не пустой, удалите в нем ссылки и повторно отправьте конфигурацию сети. Для этого можно выполнить следующий командлет PowerShell:

    Set-AzureVNetConfig -ConfigurationPath c:\virtualnetworkconfig.xml

## <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Перенос виртуальных сетей, шлюзов и связанных развертываний, которые находятся в той же подписке, что и канал ExpressRoute
В этом разделе описываются шаги, которые необходимо выполнить для переноса виртуальной сети, шлюза и связанных развертываний, находящихся в той же подписке, что и канал ExpressRoute. При таком переносе простои не возникают. Во время миграции можно продолжать использовать все ресурсы, однако плоскость управления заблокирована. 

1. Убедитесь, что канал ExpressRoute перемещен из классической среды в среду Resource Manager.
2. Убедитесь, что виртуальная сеть подготовлена для переноса.
3. Зарегистрируйте подписку для переноса ресурсов. Для этого используйте следующий фрагмент кода PowerShell: 
    ```
    Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    ```
4. Выполните проверку, подготовку и миграцию. Чтобы переместить виртуальную сеть, используйте приведенный ниже фрагмент кода PowerShell.
    ```
    Move-AzureVirtualNetwork -Prepare $vnetName  
    Move-AzureVirtualNetwork -Commit $vnetName
    ```
    Вы также можете прервать миграцию, выполнив следующий командлет PowerShell:
    ```
    Move-AzureVirtualNetwork -Abort $vnetName
    ``` 
## <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-a-different-subscription-from-that-of-the-expressroute-circuit"></a>Перенос виртуальных сетей, шлюзов и связанных развертываний, которые находятся не в одной подписке с каналом ExpressRoute

1. Убедитесь, что канал ExpressRoute перемещен из классической среды в среду Resource Manager.
2. Убедитесь, что виртуальная сеть подготовлена для переноса.
3. Обеспечьте возможность работы канала ExpressRoute в классической среде и среде Resource Manager. Чтобы разрешить использование канала в классической среде и среде Resource Manager, используйте приведенный ниже скрипт PowerShell. 
    ```
    Login-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName <My subscription>
    $circuit = Get-AzureRmExpressRouteCircuit -Name <CircuitName> -ResourceGroupName <ResourceGroup Name> 
    $circuit.AllowClassicOperations = $true
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    ```
4. Создайте разрешения в среде Resource Manager. Дополнительные сведения см. в статье [Подключение виртуальной сети к каналу ExpressRoute](expressroute-howto-linkvnet-arm.md). Чтобы создать разрешение, используйте приведенный ниже фрагмент кода PowerShell.
    ```
    circuit = Get-AzureRmExpressRouteCircuit -Name <CircuitName> -ResourceGroupName <ResourceGroup Name> 
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "AuthorizationForMigration"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    $circuit = Get-AzureRmExpressRouteCircuit -Name MigrateCircuit -ResourceGroupName MigrateRGWest

    $id = $circuit.id 
    $auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "AuthorizationForMigration"

    $key=$auth1.AuthorizationKey 
    ```
    Запишите идентификатор и ключ авторизации канала. Эти элементы используются для подключения канала к виртуальной сети после завершения переноса.
  
5. Удалите выделенный канал связи, связанный с виртуальной сетью. Чтобы удалить канал связи в классической среде, используйте следующий командлет: 
    ```
    $skey = Get-AzureDedicatedCircuit | select ServiceKey
    Remove-AzureDedicatedCircuitLink -ServiceKey $skey -VNetName $vnetName
    ```  

6. Зарегистрируйте подписку для переноса ресурсов. Для этого используйте следующий фрагмент кода PowerShell: 
    ```
    Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
    ```
7. Выполните проверку, подготовку и миграцию. Чтобы переместить виртуальную сеть, используйте приведенный ниже фрагмент кода PowerShell.
    ```
    Move-AzureVirtualNetwork -Prepare $vnetName  
    Move-AzureVirtualNetwork -Commit $vnetName
    ```
    Вы также можете прервать миграцию, выполнив следующий командлет PowerShell:
    ```
    Move-AzureVirtualNetwork -Abort $vnetName
    ```
8. Подключите виртуальную сеть обратно к каналу ExpressRoute. Следующий фрагмент кода PowerShell выполняется в контексте подписки, в которой создана виртуальная сеть. Его не следует выполнять в подписке, где создан канал. Используйте идентификатор канала в PeerID и ключа авторизации, указанного на шаге 4.
    ```
    Select-AzureRMSubscription –SubscriptionName <customer subscription>  
    $gw = Get-AzureRmVirtualNetworkGateway -Name $vnetName-Default-Gateway -ResourceGroupName ($vnetName + "-Migrated")
    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroup  ($vnetName + "-Migrated")  

    New-AzureRmVirtualNetworkGatewayConnection -Name  ($vnetName + "-GwConn") -ResourceGroupName ($vnetName + "-Migrated")  -Location $vnet.Location -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey $key
    ```
## <a name="next-steps"></a>Дальнейшие действия
* [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [Platform-supported migration of IaaS resources from classic to Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md) (Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager)
* [Common errors during classic to Azure Resource Manager migration](../virtual-machines/virtual-machines-migration-errors.md) (Распространенные ошибки при миграции из классической модели в модель Azure Resource Manager)

