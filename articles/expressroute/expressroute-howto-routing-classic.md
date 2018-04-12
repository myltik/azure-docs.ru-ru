---
title: Как настроить маршрутизацию (пиринг) для канала ExpressRoute в Azure (классическая модель) | Документация Майкрософт
description: В этой статье описана процедура создания и подготовки частного пиринга, общедоступного пиринга и пиринга Microsoft для канала ExpressRoute, а также показано, как проверить состояние, обновить или удалить пиринги для канала.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: a4bd39d2-373a-467a-8b06-36cfcc1027d2
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: d6c675d314e96cd0caab7e5eb5c60e3b1458e4a0
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>Создание и изменение пиринга для канала ExpressRoute (классическая модель)
> [!div class="op_single_selector"]
> * [портал Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [интерфейс командной строки Azure](howto-routing-cli.md)
> * [Видео — частный пиринг](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Видео — общедоступный пиринг](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Видео — пиринг Майкрософт](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (классическая модель)](expressroute-howto-routing-classic.md)
> 

В этой статье описано, как создавать настройку маршрутизации ExpressRoute и управлять ей, используя командлеты PowerShell и классическую модель развертывания. Ниже описывается, как проверить состояние, обновить или удалить и отозвать пиринги для канала ExpressRoute.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**О моделях развертывания Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Предварительные требования для настройки
* Потребуется установить последнюю версию командлетов PowerShell для управления службами Azure. Дополнительные сведения см. в разделе [Приступая к работе с командлетами Azure PowerShell](/powershell/azure/overview).  
* Прежде чем приступать к настройке, обязательно изучите [предварительные требования](expressroute-prerequisites.md), [требования к маршрутизации](expressroute-routing.md) и [рабочие процессы](expressroute-workflows.md).
* Вам потребуется активный канал ExpressRoute. Перед тем как продолжить, [создайте канал ExpressRoute](expressroute-howto-circuit-classic.md) и включите его на стороне поставщика услуг подключения. Для выполнения описанных ниже командлетов канал ExpressRoute должен быть подготовлен и включен.

> [!IMPORTANT]
> Эти инструкции распространяются только на каналы от поставщиков, предоставляющих услуги подключения второго уровня. Если вы работаете с поставщиком, предлагающим услуги третьего уровня (обычно IPVPN, типа MPLS), ваш поставщик услуг подключения выполнит настройку и управление конфигурацией самостоятельно.
> 
> 

Для каждого канала ExpressRoute можно настроить один, два или три пиринга (частный пиринг Azure, общедоступный пиринг Azure и пиринг Microsoft). Пиринги можно настраивать в любом порядке, главное, выполнять их конфигурацию по очереди.


### <a name="log-in-to-your-azure-account-and-select-a-subscription"></a>Войдите в учетную запись Azure и выберите подписку
1. Откройте консоль PowerShell с повышенными правами и подключитесь к своей учетной записи. Для подключения используйте следующий пример кода:

        Login-AzureRmAccount

2. Просмотрите подписки учетной записи.

        Get-AzureRmSubscription

3. При наличии нескольких подписок выберите подписку, которую вы хотите использовать.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

4. Затем воспользуйтесь следующим командлетом, чтобы добавить подписку Azure в PowerShell для классической модели развертывания.

        Add-AzureAccount


## <a name="azure-private-peering"></a>Частный пиринг Azure
В этом разделе описано, как создать, получить, обновить и удалить конфигурацию частного пиринга Azure для канала ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Создание частного пиринга Azure
1. **Импортируйте модуль PowerShell для ExpressRoute.**
   
    Чтобы приступить к использованию командлетов ExpressRoute, нужно импортировать модули Azure и ExpressRoute в сеанс PowerShell. Для этого выполните указанные ниже команды. Версия может отличаться.    
   
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
2. **Создайте канал ExpressRoute.**
   
    Выполните инструкции по созданию [канала ExpressRoute](expressroute-howto-circuit-classic.md). Поставщик услуг подключения должен подготовить его. Если поставщик услуг подключения оказывает услуги третьего уровня, он может включить для вас частный пиринг Azure. В этом случае инструкции в следующих разделах выполнять не нужно. Если же поставщик услуг подключения не управляет маршрутизацией за вас, после создания канала выполните приведенные ниже инструкции. 
3. **Проверьте канал ExpressRoute и убедитесь, что он подготовлен.**
   
    Сначала убедитесь, что канал ExpressRoute подготовлен и включен. См. пример ниже.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Убедитесь, что канал отображается как подготовленный и включенный. Если он не работает, обратитесь к поставщику услуг подключения чтобы перевести канал в нужное состояние.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Настройте для канала частный пиринг Azure.**
   
    Прежде чем продолжить, убедитесь в наличии следующих элементов:
   
   * Подсеть /30 для основной ссылки. Она не должна входить в адресное пространство, зарезервированное для виртуальных сетей.
   * Подсеть /30 для дополнительной ссылки. Она не должна входить в адресное пространство, зарезервированное для виртуальных сетей.
   * Действительный идентификатор виртуальной локальной сети для установки пиринга. Идентификатор не должен использоваться ни одним другим пирингом в канале.
   * Номер AS для пиринга. Можно использовать 2-байтовые и 4-байтовые номера AS. Для этого пиринга можно использовать частный номер AS. Не используйте номер 65515.
   * Хэш MD5, если вы решите его использовать. **Это необязательно**.
     
    Чтобы настроить общедоступный пиринг Azure для своего канала, выполните следующий командлет.
     
        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
     
    Если вы решили использовать MD5-хэш, используйте следующий командлет:
     
        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
     
     > [!IMPORTANT]
     > Номер AS должен быть указан в качестве ASN пиринга, а не ASN клиента.
     > 
     > 

### <a name="to-view-azure-private-peering-details"></a>Просмотр сведений о частном пиринге Azure
Для получения сведений о конфигурации можно использовать следующий командлет:

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100


### <a name="to-update-azure-private-peering-configuration"></a>Обновление конфигурации частного пиринга Azure
С помощью указанного ниже командлета можно обновить любую часть конфигурации. В приведенном ниже примере идентификатор виртуальной локальной сети канала можно обновить со 100 до 500.

    Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"

### <a name="to-delete-azure-private-peering"></a>Удаление частного пиринга Azure
Для удаления конфигурации пиринга выполните следующий командлет:

> [!WARNING]
> Перед выполнением этого командлета отсоедините от канала ExpressRoute все виртуальные сети. 
> 
> 

    Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"


## <a name="azure-public-peering"></a>Общедоступный пиринг Azure
В этом разделе описано, как создать, получить, обновить и удалить конфигурацию общедоступного пиринга Azure для канала ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Создание общедоступного пиринга Azure
1. **Импортируйте модуль PowerShell для ExpressRoute.**
   
    Чтобы приступить к использованию командлетов ExpressRoute, нужно импортировать модули Azure и ExpressRoute в сеанс PowerShell. Для этого выполните указанные ниже команды. Версия может отличаться.   
   
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
2. **Создание канала ExpressRoute**
   
    Выполните инструкции по созданию [канала ExpressRoute](expressroute-howto-circuit-classic.md). Поставщик услуг подключения должен подготовить его. Если поставщик услуг подключения оказывает услуги третьего уровня, он может включить для вас частный пиринг Azure. В этом случае инструкции в следующих разделах выполнять не нужно. Если же поставщик услуг подключения не управляет маршрутизацией за вас, после создания канала выполните приведенные ниже инструкции.
3. **Проверьте канал ExpressRoute и убедитесь, что он подготовлен.**
   
    Сначала убедитесь, что канал ExpressRoute подготовлен и включен. См. пример ниже.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Убедитесь, что канал отображается как подготовленный и включенный. Если он не работает, обратитесь к поставщику услуг подключения чтобы перевести канал в нужное состояние.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Настройте для канала общедоступный пиринг Azure.**
   
    Прежде чем продолжить, убедитесь, что у вас есть следующие сведения.
   
   * Подсеть /30 для основной ссылки. Это должен быть допустимый префикс общедоступного адреса IPv4.
   * Подсеть /30 для дополнительной ссылки. Это должен быть допустимый префикс общедоступного адреса IPv4.
   * Действительный идентификатор виртуальной локальной сети для установки пиринга. Идентификатор не должен использоваться ни одним другим пирингом в канале.
   * Номер AS для пиринга. Можно использовать 2-байтовые и 4-байтовые номера AS.
   * Хэш MD5, если вы решите его использовать. **Это необязательно**.
     
    Чтобы настроить общедоступный пиринг Azure для своего канала, выполните следующий командлет.
     
        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
     
    Если вы решили использовать MD5-хэш, используйте следующий командлет:
     
        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
     
     > [!IMPORTANT]
     > Номер AS должен быть указан в качестве ASN пиринга, а не ASN клиента.
     > 
     > 

### <a name="to-view-azure-public-peering-details"></a>Просмотр сведений об общедоступном пиринге Azure
Для получения сведений о конфигурации можно использовать следующий командлет:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 131.107.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 131.107.0.4/30
    State                          : Enabled
    VlanId                         : 200


### <a name="to-update-azure-public-peering-configuration"></a>Обновление конфигурации общедоступного пиринга Azure
С помощью указанного ниже командлета можно обновить любую часть конфигурации.

    Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"

В приведенном выше примере идентификатор виртуальной локальной сети канала обновлен с 200 до 600.

### <a name="to-delete-azure-public-peering"></a>Удаление общедоступного пиринга Azure
Для удаления конфигурации пиринга выполните следующий командлет:

    Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

## <a name="microsoft-peering"></a>Пиринг Майкрософт
В этом разделе описано, как создать, получить, обновить и удалить конфигурацию пиринга Microsoft для канала ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Создание пиринга Майкрософт
1. **Импортируйте модуль PowerShell для ExpressRoute.**
   
    Чтобы приступить к использованию командлетов ExpressRoute, нужно импортировать модули Azure и ExpressRoute в сеанс PowerShell. Для этого выполните указанные ниже команды. Версия может отличаться.   
   
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
        Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
2. **Создание канала ExpressRoute**
   
    Выполните инструкции по созданию [канала ExpressRoute](expressroute-howto-circuit-classic.md). Поставщик услуг подключения должен подготовить его. Если поставщик услуг подключения оказывает услуги третьего уровня, он может включить для вас частный пиринг Azure. В этом случае инструкции в следующих разделах выполнять не нужно. Если же поставщик услуг подключения не управляет маршрутизацией за вас, после создания канала выполните приведенные ниже инструкции.
3. **Проверьте канал ExpressRoute и убедитесь, что он подготовлен.**
   
    Сначала убедитесь, что канал ExpressRoute подготовлен и включен.
   
        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   
        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled
   
    Убедитесь, что канал отображается как подготовленный и включенный. Если он не работает, обратитесь к поставщику услуг подключения чтобы перевести канал в нужное состояние.
   
        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled
4. **Настройка пиринга Microsoft для канала**
   
    Перед началом работы убедитесь, что у вас есть следующие сведения.
   
   * Подсеть /30 для основной ссылки. Это должен быть допустимый префикс общедоступного адреса IPv4, принадлежащего вам и зарегистрированного в RIR/IRR.
   * Подсеть /30 для дополнительной ссылки. Это должен быть допустимый префикс общедоступного адреса IPv4, принадлежащего вам и зарегистрированного в RIR/IRR.
   * Действительный идентификатор виртуальной локальной сети для установки пиринга. Идентификатор не должен использоваться ни одним другим пирингом в канале.
   * Номер AS для пиринга. Можно использовать 2-байтовые и 4-байтовые номера AS.
   * Объявленные префиксы: необходимо предоставить список всех префиксов, которые вы планируете объявить во время сеанса BGP. Допускаются только общедоступные префиксы IP-адресов. Если планируется отправить набор префиксов, можно отправить список значений, разделенных запятыми. Эти префиксы должны быть зарегистрированы в RIR/IRR на ваше имя.
   * Клиент ASN: для объявления префиксов, не зарегистрированных с номером AS для пиринга, можно указать номер AS, с которым они зарегистрированы. **Это необязательно**.
   * Имя реестра маршрутизации: можно указать RIR/IRR, в котором зарегистрированы номер AS и префиксы.
   * Хэш MD5, если вы решите его использовать. **Это необязательно.**
     
    Чтобы настроить пиринг Microsoft для своего канала, выполните следующий командлет:
     
        New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### <a name="to-view-microsoft-peering-details"></a>Просмотр сведений о пиринге Майкрософт
Для получения сведений о конфигурации можно использовать следующий командлет:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

    AdvertisedPublicPrefixes       : 123.0.0.0/30
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 2245
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : ARIN
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 300


### <a name="to-update-microsoft-peering-configuration"></a>Обновление конфигурации пиринга Майкрософт
С помощью указанного ниже командлета можно обновить любую часть конфигурации.

    Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### <a name="to-delete-microsoft-peering"></a>Удаление пиринга Майкрософт
Для удаления конфигурации пиринга выполните следующий командлет:

    Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

## <a name="next-steps"></a>Дополнительная информация
Затем необходимо выполнить [связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-classic.md).

* Дополнительные сведения о рабочих процессах см. в разделе [Рабочие процессы ExpressRoute](expressroute-workflows.md).
* Дополнительную информацию о пиринге канала см. в статье [Каналы ExpressRoute и домены маршрутизации](expressroute-circuit-peerings.md).

