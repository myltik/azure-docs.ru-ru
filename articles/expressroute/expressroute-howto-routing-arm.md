---
title: "Как настроить маршрутизацию (пиринг) для канала ExpressRoute с помощью PowerShell в Azure (модель Resource Manager) | Документация Майкрософт"
description: "В этой статье описана процедура создания и подготовки частного пиринга, общедоступного пиринга и пиринга Microsoft для канала ExpressRoute, а также показано, как проверить состояние, обновить или удалить пиринги для канала."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0a036d51-77ae-4fee-9ddb-35f040fbdcdf
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: ganesr;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 97d61099ef4109d8fc1d6078bdaeddd36ad7c648
ms.lasthandoff: 05/02/2017


---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Создание и изменение пиринга для канала ExpressRoute с помощью PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager — портал Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [Resource Manager — PowerShell](expressroute-howto-routing-arm.md)
> * [Классическая модель — PowerShell](expressroute-howto-routing-classic.md)
> * [Видео — частный пиринг](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Видео — общедоступный пиринг](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Видео — пиринг Майкрософт](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> 
> 

В этой статье описано, как создать конфигурацию маршрутизации ExpressRoute и управлять ею, используя PowerShell и модель развертывания Azure Resource Manager.  Ниже описывается, как проверить состояние, обновить или удалить и отозвать пиринги для канала ExpressRoute. 


## <a name="configuration-prerequisites"></a>Предварительные требования для настройки
* Потребуется установить последнюю версию командлетов PowerShell для Azure Resource Manager. Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview). 
* Прежде чем приступать к настройке, обязательно изучите [предварительные требования](expressroute-prerequisites.md), [требования к маршрутизации](expressroute-routing.md) и [рабочие процессы](expressroute-workflows.md).
* Вам потребуется активный канал ExpressRoute. Приступая к работе, [создайте канал ExpressRoute](expressroute-howto-circuit-arm.md) ; он должен быть затем включен на стороне поставщика услуг подключения. Для выполнения описанных ниже командлетов канал ExpressRoute должен быть подготовлен и включен.

Эти инструкции распространяются только на каналы от поставщиков, предоставляющих услуги подключения второго уровня. Если вы работаете с поставщиком, предлагающим услуги третьего уровня (обычно IPVPN, типа MPLS), ваш поставщик услуг подключения выполнит настройку и управление конфигурацией самостоятельно.

> [!IMPORTANT]
> Мы пока не предлагаем использовать пиринги, которые настроены поставщиками услуг на портале управления службами. Такая возможность будет предоставлена позже. Перед настройкой пирингов BGP уточните информацию у поставщика услуг.
> 
> 

Для каждого канала ExpressRoute можно настроить один, два или три пиринга (частный пиринг Azure, общедоступный пиринг Azure и пиринг Microsoft). Пиринги можно настраивать в любом порядке, главное, выполнять их конфигурацию по очереди. 

## <a name="azure-private-peering"></a>Частный пиринг Azure
В этом разделе описано, как создать, получить, обновить и удалить конфигурацию частного пиринга Azure для канала ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Создание частного пиринга Azure
1. Импортируйте модуль PowerShell для ExpressRoute.
   
     Чтобы начать использовать командлеты ExpressRoute, необходимо установить последнюю версию установщика Powershell из [коллекции PowerShell](http://www.powershellgallery.com/) и импортировать модули диспетчера ресурсов Azure в сеанс PowerShell. Будет необходимо запустить PowerShell от имени администратора.
   
        Install-Module AzureRM
   
        Install-AzureRM
   
    Импортируйте все модули AzureRM.* в рамках диапазона известных семантических версий.
   
        Import-AzureRM
   
    Можно также просто импортировать выбранный модуль в рамках диапазона известных семантических версий. 
   
        Import-Module AzureRM.Network 
   
    Войдите в свою учетную запись.
   
        Login-AzureRmAccount
   
    Выберите подписку для создания канала ExpressRoute.
   
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
2. Создайте канал ExpressRoute.
   
    Выполните инструкции по созданию [канала ExpressRoute](expressroute-howto-circuit-arm.md). Поставщик услуг подключения должен подготовить его. 
   
    Если поставщик услуг подключения оказывает услуги третьего уровня, он может включить для вас частный пиринг Azure. В этом случае инструкции в следующих разделах выполнять не нужно. Если же поставщик услуг подключения не управляет маршрутизацией за вас, после создания канала выполните приведенные ниже инструкции. 
3. Проверьте канал ExpressRoute и убедитесь, что он подготовлен.
   
    Сначала убедитесь, что канал ExpressRoute подготовлен и включен. См. пример ниже.
   
        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   
    Ответ будет примерно таким, как показано в следующем примере.
   
        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []
4. Настройте для канала частный пиринг Azure.
   
    Прежде чем продолжить, убедитесь в наличии следующих элементов:
   
   * Подсеть /30 для основной ссылки. Она не должна входить в адресное пространство, зарезервированное для виртуальных сетей.
   * Подсеть /30 для дополнительной ссылки. Она не должна входить в адресное пространство, зарезервированное для виртуальных сетей.
   * Действительный идентификатор виртуальной локальной сети для установки пиринга. Идентификатор не должен использоваться ни одним другим пирингом в канале.
   * Номер AS для пиринга. Можно использовать 2-байтовые и 4-байтовые номера AS. Для этого пиринга можно использовать частный номер AS. Не используйте номер 65515.
   * Хэш MD5, если вы решите его использовать. **Это необязательно**.
     
    Чтобы настроить общедоступный пиринг Azure для своего канала, выполните следующий командлет.
     
          Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200
     
          Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
     
    Если вы решили использовать MD5-хэш, используйте следующий командлет:
     
          Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

     
   > [!IMPORTANT]
   > Номер AS должен быть указан в качестве ASN пиринга, а не ASN клиента.
   > 
   >


### <a name="to-view-azure-private-peering-details"></a>Просмотр сведений о частном пиринге Azure
Для получения сведений о конфигурации можно использовать следующий командлет:

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt    


### <a name="to-update-azure-private-peering-configuration"></a>Обновление конфигурации частного пиринга Azure
С помощью указанного ниже командлета можно обновить любую часть конфигурации. В приведенном ниже примере идентификатор виртуальной локальной сети канала можно обновить со 100 до 500.

    Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-delete-azure-private-peering"></a>Удаление частного пиринга Azure
Для удаления конфигурации пиринга выполните следующий командлет:

> [!WARNING]
> Перед выполнением этого командлета отсоедините от канала ExpressRoute все виртуальные сети. 
> 
> 

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt



## <a name="azure-public-peering"></a>Общедоступный пиринг Azure
В этом разделе описано, как создать, получить, обновить и удалить конфигурацию общедоступного пиринга Azure для канала ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Создание общедоступного пиринга Azure
1. Импортируйте модуль PowerShell для ExpressRoute.
   
     Чтобы начать использовать командлеты ExpressRoute, необходимо установить последнюю версию установщика Powershell из [коллекции PowerShell](http://www.powershellgallery.com/) и импортировать модули диспетчера ресурсов Azure в сеанс PowerShell. Будет необходимо запустить PowerShell от имени администратора.
   
        Install-Module AzureRM
   
        Install-AzureRM
   
    Импортируйте все модули AzureRM.* в рамках диапазона известных семантических версий.
   
        Import-AzureRM
   
    Можно также просто импортировать выбранный модуль в рамках диапазона известных семантических версий. 
   
        Import-Module AzureRM.Network 
   
    Войдите в свою учетную запись.
   
        Login-AzureRmAccount
   
    Выберите подписку для создания канала ExpressRoute.
   
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
2. Создайте канал ExpressRoute.
   
    Выполните инструкции по созданию [канала ExpressRoute](expressroute-howto-circuit-arm.md). Поставщик услуг подключения должен подготовить его. 
   
    Если поставщик услуг подключения оказывает услуги третьего уровня, он может включить для вас частный пиринг Azure. В этом случае инструкции в следующих разделах выполнять не нужно. Если же поставщик услуг подключения не управляет маршрутизацией за вас, после создания канала выполните приведенные ниже инструкции.
3. Проверьте, подготовлен ли канал ExpressRoute.
   
    Сначала убедитесь, что канал ExpressRoute подготовлен и включен. См. пример ниже.
   
        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   
    Ответ будет примерно таким, как показано в следующем примере.
   
        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []    
4. Настройте для канала общедоступный пиринг Azure.
   
       Make sure that you have the following information before you proceed further.
   
   * Подсеть /30 для основной ссылки. Это должен быть допустимый префикс общедоступного адреса IPv4.
   * Подсеть /30 для дополнительной ссылки. Это должен быть допустимый префикс общедоступного адреса IPv4.
   * Действительный идентификатор виртуальной локальной сети для установки пиринга. Идентификатор не должен использоваться ни одним другим пирингом в канале.
   * Номер AS для пиринга. Можно использовать 2-байтовые и 4-байтовые номера AS.
   * Хэш MD5, если вы решите его использовать. **Это необязательно**.

    
    Чтобы настроить общедоступный пиринг Azure для своего канала, выполните следующий командлет.
     
          Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

          Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
     
    Если вы решили использовать MD5-хэш, используйте следующий командлет:
     
          Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

          Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

     
> [!IMPORTANT]
> Номер AS должен быть указан в качестве ASN пиринга, а не ASN клиента.
> 
>


### <a name="to-view-azure-public-peering-details"></a>Просмотр сведений об общедоступном пиринге Azure
Для получения сведений о конфигурации можно использовать следующий командлет.

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt

### <a name="to-update-azure-public-peering-configuration"></a>Обновление конфигурации общедоступного пиринга Azure
С помощью указанного ниже командлета можно обновить любую часть конфигурации.

    Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600 

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

В приведенном выше примере идентификатор виртуальной локальной сети канала обновлен с 200 до 600.

### <a name="to-delete-azure-public-peering"></a>Удаление общедоступного пиринга Azure
Для удаления конфигурации пиринга выполните следующий командлет.

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="microsoft-peering"></a>Пиринг Майкрософт
В этом разделе описано, как создать, получить, обновить и удалить конфигурацию пиринга Майкрософт для канала ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Создание пиринга Майкрософт
1. Импортируйте модуль PowerShell для ExpressRoute.
   
     Чтобы начать использовать командлеты ExpressRoute, необходимо установить последнюю версию установщика Powershell из [коллекции PowerShell](http://www.powershellgallery.com/) и импортировать модули диспетчера ресурсов Azure в сеанс PowerShell. Будет необходимо запустить PowerShell от имени администратора.
   
        Install-Module AzureRM
   
        Install-AzureRM
   
    Импортируйте все модули AzureRM.* в рамках диапазона известных семантических версий.
   
        Import-AzureRM
   
    Можно также просто импортировать выбранный модуль в рамках диапазона известных семантических версий. 
   
        Import-Module AzureRM.Network 
   
    Войдите в свою учетную запись.
   
        Login-AzureRmAccount
   
    Выберите подписку для создания канала ExpressRoute.
   
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
2. Создайте канал ExpressRoute.
   
    Выполните инструкции по созданию [канала ExpressRoute](expressroute-howto-circuit-arm.md). Поставщик услуг подключения должен подготовить его. 
   
    Если поставщик услуг подключения оказывает услуги третьего уровня, он может включить для вас частный пиринг Azure. В этом случае инструкции в следующих разделах выполнять не нужно. Если же поставщик услуг подключения не управляет маршрутизацией за вас, после создания канала выполните приведенные ниже инструкции.
3. Проверьте, подготовлен ли канал ExpressRoute.
   
    Сначала убедитесь, что канал ExpressRoute подготовлен и включен. См. пример ниже.
   
        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   
    Ответ будет примерно таким, как показано в следующем примере.
   
        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []    
4. Настройте пиринг Майкрософт для этого канала.
   
    Перед началом работы убедитесь, что у вас есть следующие сведения.
   
   * Подсеть /30 для основной ссылки. Это должен быть допустимый префикс общедоступного адреса IPv4, принадлежащего вам и зарегистрированного в RIR/IRR.
   * Подсеть /30 для дополнительной ссылки. Это должен быть допустимый префикс общедоступного адреса IPv4, принадлежащего вам и зарегистрированного в RIR/IRR.
   * Действительный идентификатор виртуальной локальной сети для установки пиринга. Идентификатор не должен использоваться ни одним другим пирингом в канале.
   * Номер AS для пиринга. Можно использовать 2-байтовые и 4-байтовые номера AS.
   * Объявленные префиксы: необходимо предоставить список всех префиксов, которые вы планируете объявить во время сеанса BGP. Допускаются только общедоступные префиксы IP-адресов. Если планируется отправить набор префиксов, можно отправить список значений, разделенных запятыми. Эти префиксы должны быть зарегистрированы в RIR/IRR на ваше имя.
   * Клиент ASN: для объявления префиксов, не зарегистрированных с номером AS для пиринга, можно указать номер AS, с которым они зарегистрированы. **Это необязательно**.
   * Имя реестра маршрутизации: можно указать RIR/IRR, в котором зарегистрированы номер AS и префиксы.
   * Хэш MD5, если вы решите его использовать. **Это необязательно.**
     
      Чтобы настроить пиринг Майкрософт для своего канала, выполните следующий командлет:
     
          Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"
     
          Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

### <a name="to-get-microsoft-peering-details"></a>Получение сведений о пиринге Майкрософт
Для получения сведений о конфигурации можно использовать следующий командлет.

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt


### <a name="to-update-microsoft-peering-configuration"></a>Обновление конфигурации пиринга Майкрософт
С помощью указанного ниже командлета можно обновить любую часть конфигурации.

    Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-delete-microsoft-peering"></a>Удаление пиринга Майкрософт
Для удаления конфигурации пиринга выполните следующий командлет.

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="next-steps"></a>Дальнейшие действия
Следующий шаг — [связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Дополнительную информацию о рабочих процессах ExpressRoute см. в статье [Процедуры ExpressRoute для подготовки каналов и состояний каналов](expressroute-workflows.md).
* Дополнительную информацию о пиринге канала см. в статье [Каналы ExpressRoute и домены маршрутизации](expressroute-circuit-peerings.md).
* Подробнее о работе с виртуальными сетями см. в статье [Обзор виртуальных сетей](../virtual-network/virtual-networks-overview.md).


