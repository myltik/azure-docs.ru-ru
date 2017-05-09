---
title: "Пиринговая связь между виртуальными сетями Azure: PowerShell | Документация Майкрософт"
description: "Узнайте, как создать пиринговую связь между виртуальными сетями с помощью Azure PowerShell."
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: dac579bd-7545-461a-bdac-301c87434c84
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayan; annahar
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 57991a59feb784fd2e5277e893110de2a776a060
ms.contentlocale: ru-ru
ms.lasthandoff: 04/27/2017


---
# <a name="create-a-virtual-network-peering-using-azure-powershell"></a>Создание пиринговой связи между виртуальными сетями с помощью Azure PowerShell
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Чтобы создать пиринговую связь между виртуальными сетями с помощью PowerShell, сделайте следующее:

1. Если вы ранее не использовали Azure PowerShell, следуйте указаниям в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview) до этапа входа в Azure и выбора подписки.

    > [!NOTE]
    > Командлет PowerShell для управления пирингом виртуальных сетей включен в выпуск [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)
    >

2. Чтение объектов виртуальных сетей.

    ```powershell
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
    $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2
    ```

3. Чтобы настроить пиринг между двумя виртуальными сетями, необходимо создать два соединения, по одному для каждого направления. Сначала создадим пиринговое соединение от виртуальной сети VNet1 к сети VNet2.

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id
    ```

    Возвращаемые выходные данные:
        
        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Initiated
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
            UseRemoteGateways    : False
            RemoteGateways        : null
            RemoteVirtualNetworkAddressSpace : null

4. Затем создадим пиринговое соединение от виртуальной сети VNet2 к сети VNet1.

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id
    ```

    Возвращаемые выходные данные:

        Name            : LinkToVNet1
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet2
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
        UseRemoteGateways    : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null
5. После создания пиринговой связи между виртуальными сетями введите следующую команду, чтобы просмотреть состояние связи:

    ```powershell
    Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2
    ```

    Возвращаемые выходные данные:
   
        Name            : LinkToVNet2
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null
   
    Вы можете изменить несколько свойств пиринговой связи между виртуальными сетями.
   
   | Параметр | Описание | значение по умолчанию |
   |:--- |:--- |:--- |
   | AllowVirtualNetworkAccess |Определяет, будет ли адресное пространство пиринговой виртуальной сети включено как часть тега Virtual_network. |Да |
   | AllowForwardedTraffic |Определяет, будет ли приниматься трафик, поступающий не из пиринговой виртуальной сети. |Нет |
   | AllowGatewayTransit |Разрешает пиринговой виртуальной сети использовать шлюз вашей виртуальной сети. |Нет |
   | UseRemoteGateways |Необходимо использовать шлюз вашей пиринговой виртуальной сети. Этот шлюз должен быть настроен. Кроме того, должен быть выбран параметр AllowGatewayTransit. Этот параметр нельзя использовать, если вы уже настроили шлюз. |Нет |

    Каждая связь в пиринге между виртуальными сетями имеет описанные выше свойства. Например, вы можете указать для параметра `AllowVirtualNetworkAccess` значение *True*, чтобы настроить пиринговую связь от VNet1 к VNet2, или значение *False*, чтобы настроить пиринговую связь в другом направлении.

    ```powershell
    $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 `
    -ResourceGroupName vnet101 -Name LinkToVNet2

    $LinktoVNet2.AllowForwardedTraffic = $true
    Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2
    ```

    Выполните командлет `Get-AzureRmVirtualNetworkPeering`, чтобы проверить значение свойства после изменения. В выходных данных вы увидите, что после выполнения приведенных выше командлетов значение свойства `AllowForwardedTraffic` изменяется на *True*.

        Name            : LinkToVNet2
        Id            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic        : True
        AllowGatewayTransit        : False
        UseRemoteGateways        : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null

    После установления пиринговой связи виртуальные машины смогут взаимодействовать друг с другом через обе виртуальные сети. По умолчанию свойство `AllowVirtualNetworkAccess` имеет значение *True*. Поэтому при пиринге виртуальных сетей будут подготовлены соответствующие списки управления доступом, обеспечивая обмен данными между этими виртуальными сетями. Но вы также можете заблокировать возможность подключения (например, к определенной подсети или виртуальной машине) с помощью правил группы безопасности сети (NSG), реализуя более точное управление доступом между двумя виртуальными сетями. Дополнительные сведения о группах безопасности сети см. в статье [Управление потоком сетевого трафика с помощью групп безопасности сети](virtual-networks-create-nsg-arm-ps.md).

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Чтобы с помощью PowerShell создать пиринговую связь между виртуальными сетями в разных подписках, выполните такие действия.

1. Войдите в Azure, используя учетную запись привилегированного пользователя A для подписки A, и запустите следующий командлет:

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5
    ```

    Это не является обязательным требованием. Пиринговую связь можно установить, даже если пользователи отдельно отправляют запросы о пиринговой связи для своих виртуальных сетей. Нужно только, чтобы эти запросы совпадали друг с другом. Добавление привилегированного пользователя из другой виртуальной сети в качестве пользователя локальной сети упрощает настройку.
2. Войдите в Azure, используя учетную запись привилегированного пользователя В для подписки В, и запустите следующий командлет:

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3
    ```

    > [!IMPORTANT]
    > Если вы создаете пиринговую связь между двумя виртуальными сетями, созданными по модели развертывания с помощью Azure Resource Manager, переходите к следующим действиям из этого раздела. Если две виртуальные сети созданы с помощью разных моделей развертывания, пропустите оставшиеся шаги в этом разделе и выполните действия, описанные в [этом разделе](#x-model).

3. Затем в сеансе пользователя A выполните следующую команду:

    ```powershell
    $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscription-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess
    ```

4. Затем в сеансе пользователя В выполните следующий командлет:

    ```powershell
    $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess
    ```

5. После настройки пиринговой связи любая виртуальная машина из сети VNet3 сможет обмениваться данными с любой виртуальной машиной из сети VNet5.

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. В этом сценарии можно выполнить приведенные ниже командлеты PowerShell, чтобы настроить пиринговую связь между виртуальными сетями. Укажите для свойства `AllowForwardedTraffic` значение *True* и создайте связь между виртуальными сетями VNET1 и HubVNet, чтобы разрешить входящий трафик, поступающий не из адресного пространства пиринговой виртуальной сети.

    ```powershell
    $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

    Add-AzureRmVirtualNetworkPeering -Name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.Id -AllowForwardedTraffic

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.Id
    ```

2. Настроив пиринговую связь, ознакомьтесь с этой [статьей](virtual-network-create-udr-arm-ps.md). Из нее вы узнаете, как настроить пользовательский маршрут для перенаправления трафика VNet1 через виртуальное устройство. Указывая адрес следующего прыжка в маршруте, вы можете выбрать IP-адрес виртуального устройства в пиринговой виртуальной сети HubVNet. Ниже приведен пример:

    ```powershell
    $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

    $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

    Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1
    ```

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

1. Если вы создаете пиринговую связь между виртуальными сетями, развернутыми в *одной* подписке с помощью разных моделей развертывания, перейдите к шагу 2. Функция создания пиринговой связи между виртуальными сетями, развернутыми с помощью разных моделей развертывания в *разных* подписках, доступна в режиме **предварительной версии**. В отличие от функций в общедоступных версиях, функции в предварительной версии не обеспечивают аналогичный уровень надежности и не подкреплены соглашением об уровне обслуживания. Если вы создаете пиринговую связь между виртуальными сетями, развернутыми в разных подписках с помощью разных моделей развертывания, вам нужно сначала выполнить следующие задачи.
    - Зарегистрируйте функцию предварительной версии в подписке Azure. Для этого введите следующие команды в PowerShell: `Register-AzureRmProviderFeature -FeatureName AllowClassicCrossSubscriptionPeering -ProviderNamespace Microsoft.Network` и `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network`.
    - Выполните шаги 1–2 из раздела этой статьи, посвященного [пиринговой связи между разными подписками](#x-sub).
2. Прочтите объект виртуальной сети **VNET1** (виртуальная сеть Azure Resource Manager). Для этого введите следующую команду:

    ```powershell
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
    ```

3. Чтобы настроить пиринг виртуальных сетей в этом сценарии, требуется только одно соединение, в частности соединение от **VNET1** к **VNET2**. Для этого необходимо знать идентификатор ресурса классической виртуальной сети. Формат идентификатора группы ресурсов показан в примере ниже:

           subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.ClassicNetwork/virtualNetworks/{VirtualNetworkName}

    Замените SubscriptionID, ResourceGroupName и VirtualNetworkName соответствующими именами.

    Это можно сделать с помощью следующей команды:

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2
    ```

4. Создав пиринговую связь между виртуальными сетями, вы увидите состояние связи, как показано в выходных данных ниже:
   
        Name                             : LinkToVNet2
        Id                               : /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeerings/LinkToVNet2
        Etag                             : W/"acecbd0f-766c-46be-aa7e-d03e41c46b16"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                         "Id": "/subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                       }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

## <a name="remove-a-vnet-peering"></a>Удаление пиринговой связи между виртуальными сетями
1. Чтобы удалить пиринговую связь между виртуальными сетями, выполните следующий командлет:

    ```powershell
    Remove-AzureRmVirtualNetworkPeering
    ```

    Чтобы удалить обе связи, выполните следующие команды:

    ```powershell
    Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
    Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
    ```

2. Когда вы удалите одну связь в пиринге между виртуальными сетями, состояние пирингового соединения изменится на *Отключено*. В этом состоянии вы не сможете повторно создать соединение, пока состояние не изменится на *Инициировано*. Перед повторной настройкой пиринга виртуальных сетей рекомендуется удалить оба соединения.


