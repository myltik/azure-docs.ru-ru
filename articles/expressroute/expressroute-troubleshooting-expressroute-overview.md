---
title: Проверка подключения. Руководство по устранению неполадок Azure ExpressRoute | Документация Майкрософт
description: В этой статье содержатся инструкции по устранению неполадок и проверке сквозного подключения канала ExpressRoute.
documentationcenter: na
services: expressroute
author: rambk
manager: tracsman
editor: ''
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: cherylmc
ms.openlocfilehash: 5d01f2e402e4b793274761703ec3ca1ea3ff8164
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30185991"
---
# <a name="verifying-expressroute-connectivity"></a>Проверка подключения ExpressRoute
Подключение ExpressRoute, расширяющее локальную сеть в Microsoft Cloud посредством частного подключения, которое обеспечивает поставщик услуг подключения, включает в себя следующие три различные сетевые зоны:

-   клиентскую сеть;
-   сеть поставщика;
-   центр обработки данных Майкрософт.

Цель данного документа — помочь пользователю определить, где (при наличии) существует проблема с подключением и в какой зоне, чтобы устранить ее при помощи соответствующей группы поддержки. Если для устранения проблемы необходима помощь службы поддержки Майкрософт, отправьте запрос в [службу поддержки Майкрософт][Support].

> [!IMPORTANT]
> Данный документ предназначен для диагностики и устранения простых проблем. Он не заменит услуг службы поддержки Майкрософт. Если вам не удается устранить проблему, используя приведенные здесь рекомендации, то необходимо отправить запрос в [службу поддержки Майкрософт][Support].
>
>

## <a name="overview"></a>Обзор
На следующей схеме показано логическое подключение между клиентской сетью и сетью Майкрософт через ExpressRoute.
[![1]][1]

На предыдущей схеме цифры обозначают ключевые точки сети. Для указания точек сети в этой статье часто используются номера.

В зависимости от модели подключения ExpressRoute (совместное размещение Cloud Exchange, Ethernet-подключение типа "точка — точка" или подключение типа "любой к любому" (IPVPN)) точки сети 3 и 4 могут быть коммутаторами (устройства уровня 2). Ниже приведены представленные на схеме ключевые точки сети.

1.  Клиентское вычислительное устройство (например, сервер или компьютер).
2.  Клиентские пограничные маршрутизаторы (CE). 
3.  Пограничные маршрутизаторы (коммутаторы) поставщика услуг связи, подключенные к клиентским пограничным маршрутизаторам (PE, подключенные к CE). В этом документе называются PE-CE.
4.  Пограничные маршрутизаторы (коммутаторы) поставщика услуг связи, подключенные к MSEE. В этом документе называются PE-MSEE.
5.  Маршрутизаторы MSEE ExpressRoute.
6.  Шлюз виртуальной сети
7.  Вычислительное устройство в виртуальной сети Azure

При использовании моделей подключения "совместное размещение Cloud Exchange" или "Ethernet-подключение типа "точка — точка" клиентский пограничный маршрутизатор (2) устанавливает пиринг BGP с маршрутизаторами MSEE (5). Точки сети 3 и 4 будут по-прежнему существовать, но будут в некоторой степени прозрачными как устройства уровня 2.

Если используется модель подключения типа "любой к любому" (IPVPN), маршрутизаторы PE (подключенные к MSEE) (4) устанавливают пиринг BGP с маршрутизаторами MSEE (5). Затем маршруты будут распространяться в обратном направлении в сеть клиента по сети поставщика услуг IPVPN.

>[!NOTE]
>Для обеспечения высокой доступности ExpressRoute корпорации Майкрософт необходима избыточная пара сеансов BGP между маршрутизаторами MSEE (5) и маршрутизаторами поставщика услуг связи, подключенными к MSEE (PE-MSEE) (4). Рекомендуется также избыточная пара сетевых путей между клиентской сетью и маршрутизаторами PE-CE. Однако в модели подключения типа "любой к любому" (IPVPN) одно устройство CE (2) может быть подключено к одному или нескольким маршрутизаторам PE (3).
>
>

Чтобы проверить канал ExpressRoute (точки сети обозначаются соответствующими номерами), необходимо выполнить следующие действия:
1. [Проверить подготовку и состояние канала (5).](#validate-circuit-provisioning-and-state)
2. [Проверить, что настроен по крайней мере один пиринг ExpressRoute (5).](#validate-peering-configuration)
3. [Проверить ARP между корпорацией Майкрософт и поставщиком услуг связи (соединение между точками 4 и 5).](#validate-arp-between-microsoft-and-the-service-provider)
4. [Проверить BGP и маршруты для MSEE (BGP между точками 4–5 и 5–6, если подключена виртуальная сеть).](#validate-bgp-and-routes-on-the-msee)
5. [Проверить статистику трафика (трафика, проходящего через точку 5).](#check-the-traffic-statistics)

Дополнительные проверки будут добавлены в будущем. Проверяйте ежемесячно!

## <a name="validate-circuit-provisioning-and-state"></a>Проверка подготовки и состояния канала
Независимо от модели подключения необходимо создать канал ExpressRoute и соответственно сгенерировать ключ службы для подготовки канала. При подготовке канала ExpressRoute устанавливаются избыточные подключения уровня 2 между маршрутизаторами PE-MSEE (4) и MSEE (5). Дополнительные сведения о создании, изменении, подготовке и проверке канала ExpressRoute см. в статье [Создание и изменение канала ExpressRoute][CreateCircuit].

>[!TIP]
>Ключ службы однозначно идентифицирует канал ExpressRoute. Этот ключ необходим для большинства команд PowerShell, упомянутых в данном документе. Если вам необходима помощь от корпорации Майкрософт или от партнера ExpressRoute в устранении неполадок с ExpressRoute, укажите ключ службы для оперативного определения канала.
>
>

### <a name="verification-via-the-azure-portal"></a>Проверка на портале Azure
На портале Azure можно проверить состояние канала ExpressRoute, выбрав ![2][2] в боковом меню слева, а затем выбрав канал ExpressRoute. Выберите канал ExpressRoute в разделе "Все ресурсы", после чего откроется колонка канала ExpressRoute. В ![3][3] разделе колонки отображаются основные сведения об ExpressRoute, как показано на следующем снимке экрана.

![4][4]    

Параметр *Состояние цепи* в разделе основных сведений об ExpressRoute указывает состояние канала на стороне Майкрософт. Параметр *Состояние поставщика* указывает, был ли канал *подготовлен* на стороне поставщика услуг. 

Для работы канала ExpressRoute параметр *Состояние цепи* должен иметь значение *Включено*, а параметр *Состояние поставщика* — значение *Подготовлено*.

>[!NOTE]
>Если параметр *Состояние цепи* имеет значение "Не включено", обратитесь в [службу поддержки Майкрософт][Support]. Если параметр *Состояние поставщика* имеет значение "Не подготовлено", обратитесь к поставщику услуг связи.
>
>

### <a name="verification-via-powershell"></a>Проверка с помощью PowerShell
Чтобы получить список всех каналов ExpressRoute в группе ресурсов, используйте следующую команду:

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Имя группы ресурсов можно получить с помощью Azure. Перейдите к предыдущему подразделу этого документа и обратите внимание, что имя группы ресурсов указано на снимке экрана.
>
>

Чтобы выбрать конкретный канал ExpressRoute в группе ресурсов, используйте следующую команду:

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Пример ответа:

    Name                             : Test-ER-Ckt
    ResourceGroupName                : Test-ER-RG
    Location                         : westus2
    Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                        "Name": "Standard_UnlimitedData",
                                        "Tier": "Standard",
                                        "Family": "UnlimitedData"
                                        }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             : 
    ServiceProviderProperties        : {
                                        "ServiceProviderName": "****",
                                        "PeeringLocation": "******",
                                        "BandwidthInMbps": 100
                                        }
    ServiceKey                       : **************************************
    Peerings                         : []
    Authorizations                   : []

Чтобы подтвердить работоспособность канала ExpressRoute, обратите особое внимание на следующие поля:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

>[!NOTE]
>Если параметр *CircuitProvisioningState* имеет значение Not Enabled, обратитесь в [службу поддержки Майкрософт][Support]. Если параметр *ServiceProviderProvisioningState* имеет значение Not Provisioned, обратитесь к поставщику услуг связи.
>
>

### <a name="verification-via-powershell-classic"></a>Проверка с помощью PowerShell (классическая модель)
Чтобы получить список всех каналов ExpressRoute в подписке, используйте следующую команду:

    Get-AzureDedicatedCircuit

Чтобы выбрать конкретный канал ExpressRoute, используйте следующую команду:

    Get-AzureDedicatedCircuit -ServiceKey **************************************

Пример ответа:

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Чтобы подтвердить работоспособность канала ExpressRoute, обратите особое внимание на следующие поля: ServiceProviderProvisioningState: Provisioned; Status: Enabled.

>[!NOTE]
>Если параметр *Status* имеет значение Not Enabled, обратитесь в [службу поддержки Майкрософт][Support]. Если параметр *ServiceProviderProvisioningState* имеет значение Not Provisioned, обратитесь к поставщику услуг связи.
>
>

## <a name="validate-peering-configuration"></a>Проверка настройки пиринга
Когда поставщик услуг завершит подготовку канала, можно создать конфигурацию маршрутизации через канал ExpressRoute между маршрутизаторами MSEE-PR (4) и MSEE (5). Каждый канал ExpressRoute может иметь включенными один, два или три контекста маршрутизации: частный пиринг Azure (трафик к частным виртуальным сетям в Azure), общедоступный пиринг Azure (трафик к общедоступным IP-адресам в Azure) и пиринг Майкрософт (трафик к Office 365 и Dynamics 365). Дополнительные сведения о создании и изменении настроек маршрутизации см. в статье [Создание и изменение маршрутизации для канала ExpressRoute][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Проверка на портале Azure

>[!NOTE]
>Если поставщик услуг предоставляет уровень 3, а пиринги на портале пусты, обновите конфигурацию схемы с помощью кнопки "Обновить" на портале. Эта операция применит правильную конфигурацию маршрутизации в вашей схеме. 
>
>

На портале Azure можно просмотреть состояние канала ExpressRoute, выбрав ![2][2] в боковом меню слева, а затем выбрав канал ExpressRoute. Выберите канал ExpressRoute в разделе "Все ресурсы", после чего откроется колонка канала ExpressRoute. В ![3][3] разделе колонки отображаются основные сведения об ExpressRoute, как показано на следующем снимке экрана.

![5][5]

Как отмечалось в предыдущем примере, контекст маршрутизации частного пиринга Azure включен, тогда как контекст маршрутизации общедоступного пиринга Azure и пиринга Майкрософт отключен. Если контекст пиринга включен, в списке также отобразятся первичные и вторичные подсети типа "точка — точка" (требуется для BGP). Подсети /30 используются для IP-адреса интерфейса маршрутизаторов MSEE и PE-MSEE. 

>[!NOTE]
>Если пиринг не включен, проверьте, соответствуют ли назначенные первичные и вторичные подсети конфигурации маршрутизаторов PE-MSEE. Если это не так, см. сведения об изменении настроек маршрутизаторов MSEE в статье [Создание и изменение маршрутизации для канала ExpressRoute][CreatePeering]
>
>

### <a name="verification-via-powershell"></a>Проверка с помощью PowerShell
Чтобы получить дополнительные сведения о конфигурации частного пиринга Azure, используйте следующие команды:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Пример ответа в случае успешной настройки частного пиринга:

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : ####
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 300
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 Если контекст пиринга включен, в списке отобразятся первичные и вторичные префиксы адресов. Подсети /30 используются для IP-адреса интерфейса маршрутизаторов MSEE и PE-MSEE.

Чтобы получить дополнительные сведения о настройке общедоступного пиринга Azure, используйте следующие команды:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Чтобы получить дополнительные сведения о настройке пиринга Майкрософт, используйте следующие команды:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Если пиринг не настроен, отобразится сообщение об ошибке. Пример ответа, если указанный пиринг (в этом примере общедоступный пиринг Azure) не настроен в канале.

    Get-AzureRmExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzureRmExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


<p/>
>[!NOTE]
>Если пиринг не включен, проверьте, соответствуют ли назначенные первичные и вторичные подсети конфигурации связанных маршрутизаторов PE-MSEE. Кроме того, проверьте, используются ли в маршрутизаторах MSEE правильные значения *VlandI*, *AzureASN*, *PeerASN* и соответствуют ли они тем, которые используются в связанных маршрутизаторах PE-MSEE. Если вы выбрали хэширование MD5, общий ключ должен быть одинаковым для пары маршрутизаторов MSEE и PE-MSEE. Дополнительные сведения об изменении настроек маршрутизаторов MSEE см. в статье [Создание и изменение маршрутизации для канала ExpressRoute][CreatePeering].  
>
>

### <a name="verification-via-powershell-classic"></a>Проверка с помощью PowerShell (классическая модель)
Чтобы получить дополнительные сведения о настройке частного пиринга Azure, используйте следующие команды:

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

Пример ответа в случае успешной настройки частного пиринга:

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : ####
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100

Если контекст пиринга включен, в списке отобразятся первичные и вторичные подсети. Подсети /30 используются для IP-адреса интерфейса маршрутизаторов MSEE и PE-MSEE.

Чтобы получить дополнительные сведения о настройке общедоступного пиринга Azure, используйте следующие команды:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Чтобы получить дополнительные сведения о настройке пиринга Майкрософт, используйте следующие команды:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

>[!IMPORTANT]
>Если сеансы пиринга уровня 3 заданы поставщиком услуг, параметры сеансов пиринга ExpressRoute, настроенные на портале или в PowerShell, перезапишут параметры поставщика услуг связи. Для сброса параметров пиринга со стороны поставщика потребуется помощь службы поддержки поставщика услуг связи. Изменяйте сеансы пиринга ExpressRoute, только если известно, что поставщик услуг связи предлагает только услуги уровня 2.
>
>

<p/>
>[!NOTE]
>Если пиринг не включен, проверьте, соответствуют ли назначенные первичные и вторичные пиринговые подсети конфигурации связанных маршрутизаторов PE-MSEE. Кроме того, проверьте, используются ли в маршрутизаторах MSEE правильные значения *VlandI*, *AzureASN*, *PeerASN* и соответствуют ли они тем, которые используются в связанных маршрутизаторах PE-MSEE. Дополнительные сведения об изменении настроек маршрутизаторов MSEE см. в статье [Создание и изменение маршрутизации для канала ExpressRoute][CreatePeering].
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>Проверка ARP между корпорацией Майкрософт и поставщиком услуг связи
В этом разделе используются классические команды PowerShell. Если вы использовали команды PowerShell для Azure Resource Manager, убедитесь, что у вас есть права администратора или соадминистратора для доступа к подписке. Сведения об устранении неполадок с помощью команд Azure Resource Manager см. в документе [Получение таблиц ARP в модели развертывания с помощью Resource Manager][ARP].

>[!NOTE]
>Чтобы получить ARP, можно использовать команды PowerShell для Azure Resource Manager и портал Azure. Если при использовании команд PowerShell для Azure Resource Manager возникают ошибки, должны работать классические команды PowerShell так же, как классические команды PowerShell работают с каналами ExpressRoute в Azure Resource Manager.
>
>

Для получения таблицы ARP из основного маршрутизатора MSEE частного пиринга используйте следующую команду:

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Пример ответа в случае успешного результата:

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

Аналогичным образом можно проверить таблицу ARP из MSEE по *основному*/*дополнительному* пути для *частного*/*публичного* пиринга или пиринга /*Майкрософт*.

В следующем примере показан ответ команды, если пиринг не существует.

    ARP Info:
       
>[!NOTE]
>Если в таблице ARP нет IP-адресов интерфейсов, сопоставленных с MAC-адресами, ознакомьтесь со следующими сведениями.
>1. Если первый IP-адрес подсети /30 назначен для связи между маршрутизатором MSEE-PR и MSEE используется интерфейсом MSEE-PR, Azure всегда использует второй IP-адрес для маршрутизаторов MSEE.
>2. Убедитесь, что клиентские (C-тег) и служебные теги (S-тег) виртуальной сети соответствуют паре MSEE-PR и MSEE.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>Проверка BGP и маршрутов MSEE
В этом разделе используются классические команды PowerShell. Если вы использовали команды PowerShell для Azure Resource Manager, убедитесь, что у вас есть права администратора или соадминистратора для доступа к подписке.

>[!NOTE]
>Чтобы получить сведения о BGP, можно использовать команды PowerShell для Azure Resource Manager и портала Azure. Если при использовании команд PowerShell для Azure Resource Manager возникают ошибки, должны работать классические команды PowerShell так же, как классические команды PowerShell работают с каналами ExpressRoute в Azure Resource Manager.
>
>

Чтобы получить сводные сведения о таблице маршрутизации (соседа BGP) для определенного контекста маршрутизации, используйте следующую команду:

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

Пример ответа:

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

Как показано в предыдущем примере, команда полезна для определения того, как давно установлен контекст маршрутизации. Она также показывает число префиксов маршрутов, объявленных пиринговым маршрутизатором.

>[!NOTE]
>Если состояние имеет значение Active (Активно) или Idle (Простаивает), проверьте, соответствуют ли назначенные первичные и вторичные подсети конфигурации связанных маршрутизаторов PE-MSEE. Кроме того, проверьте, используются ли в маршрутизаторах MSEE правильные значения *VlandI*, *AzureASN*, *PeerASN* и соответствуют ли они тем, которые используются в связанных маршрутизаторах PE-MSEE. Если вы выбрали хэширование MD5, общий ключ должен быть одинаковым для пары маршрутизаторов MSEE и PE-MSEE. Дополнительные сведения об изменении настроек маршрутизаторов MSEE см. в статье [Создание и изменение маршрутизации для канала ExpressRoute][CreatePeering].
>
>

<p/>
>[!NOTE]
>Если определенные получатели недоступны через определенный пиринг, проверьте таблицу маршрутов для маршрутизаторов MSEE, принадлежащих определенному контексту пиринга. Если соответствующий префикс (может быть преобразованный через NAT IP-адрес) присутствует в таблице маршрутизации, то проверьте, разрешают ли трафик существующие на пути брандмауэры, группы безопасности сети и списки управления доступом.
>
>

Чтобы получить полную таблицу маршрутизации из MSEE по *основному* пути для конкретного *частного* контекста маршрутизации, используйте следующую команду:

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Пример ответа в случае успешного результата:

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

Аналогичным образом можно проверить таблицу маршрутизации из MSEE по *основному*/*дополнительному* пути для *частного*/*публичного* пиринга или пиринга/ *Майкрософт*.

В следующем примере показан ответ команды, если пиринг не существует.

    Route Table Info:

## <a name="check-the-traffic-statistics"></a>Проверка статистики трафика
Для получения сводной статистики трафика (переданных и полученных байтов) по первичному и вторичному пути для контекста пиринга используйте следующую команду:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

Пример результата выполнения команды:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Пример результата выполнения команды при отсутствующем пиринге:

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения доступны в следующих источниках:

- [Служба технической поддержки Майкрософт][Support]
- [Создание и изменение канала ExpressRoute][CreateCircuit]
- [Создание и изменение маршрутизации для канала ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "Логическое подключение ExpressRoute"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Значок "Все ресурсы""
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Значок "Обзор""
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Снимок экран раздела с основными сведениями об ExpressRoute"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Снимок экран раздела с основными сведениями об ExpressRoute"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






