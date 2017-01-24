---
title: "Подключение виртуальной сети к нескольким сайтам с помощью VPN-шлюза и PowerShell | Документация Майкрософт"
description: "Данная статья поможет настроить подключение нескольких локальных сайтов к виртуальной сети с использованием VPN-шлюза для классической модели развертывания."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-service-management
ms.assetid: b043df6e-f1e8-4a4d-8467-c06079e2c093
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2016
ms.author: yushwang
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 3d6f2d2bc7bd55d7f7d609e66826e3a3722c8f32


---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Добавление подключения типа "сеть — сеть" к виртуальной сети с помощью существующего подключения VPN-шлюза
> [!div class="op_single_selector"]
> * [Resource Manager: портал](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [Классическая модель: PowerShell](vpn-gateway-multi-site.md)
> 
> 

В этой статье рассматривается использование PowerShell для добавления подключений типа "сеть — сеть" (S2S) к VPN-шлюзу с имеющимся подключением. Этот тип подключения часто называется "многосайтовым". 

Эта статья относится к виртуальным сетям, созданным с помощью классической модели развертывания (также называется Service Management). Эти действия не применяются к параллельно существующим конфигурациям подключений ExpressRoute и "сеть — сеть". Сведения о параллельно существующих подключениях см. в разделе [Параллельно существующие подключения ExpressRoute и "сеть — сеть"](../expressroute/expressroute-howto-coexist-classic.md).

### <a name="deployment-models-and-methods"></a>Модели и методы развертывания
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Мы обновляем эту таблицу по мере выпуска новых статей и дополнительных инструментов для этой конфигурации. При появлении статьи мы указываем прямую ссылку на нее в этой таблице.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>О подключении
Вы можете подключить несколько локальных сайтов к одной виртуальной сети. Это особенно привлекательно для создания гибридных облачных решений. Создание многосайтового подключения к шлюзу виртуальной сети Azure очень похоже на создание других подключений типа "сеть — сеть". На самом деле вы можете использовать имеющийся VPN-шлюз Azure, если это шлюз с динамической маршрутизацией (на основе маршрутов).

Если к вашей виртуальной сети уже подключен статический шлюз, вы можете изменить его тип на динамический, не перестраивая виртуальную сеть, чтобы обеспечить многосайтовое подключение. Прежде чем менять тип маршрутизации, убедитесь, что локальный VPN-шлюз поддерживает конфигурации VPN на основе маршрутов. 

![схема многосайтового подключения](./media/vpn-gateway-multi-site/multisite.png "multi-site")

## <a name="points-to-consider"></a>Что необходимо учесть
**Для внесения изменений в эту виртуальную сеть нельзя использовать классический портал Azure.**  Для этого выпуска изменения необходимо будет внести в файл конфигурации сети, а не использовать для этого классический портал Azure. Если внести изменения на классической версии портала, они заменят параметры ссылок на несколько сайтов для этой виртуальной сети. 

К завершению процедуры многосайтового подключения вы должны будете довольно уверенно использовать файл конфигурации сети. Однако при наличии нескольких людей, работающих над конфигурацией сети, необходимо убедиться, что все знают об этом ограничении. Это не значит, что использовать классический портал Azure вообще нельзя. Вы можете использовать его для всех действий, кроме изменения конфигурации этой конкретной виртуальной сети.

## <a name="before-you-begin"></a>Перед началом работы
Перед началом настройки убедитесь, что выполнены следующие предварительные требования:

* Подписка Azure. Если у вас нет подписки Azure, вы можете [активировать преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или [зарегистрировать бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/).
* Совместимое оборудование VPN для каждого местного расположения. Ознакомьтесь со статьей [VPN-устройства для создания виртуальных сетей](vpn-gateway-about-vpn-devices.md) , чтобы проверить, является ли совместимым устройство, которое вы хотите использовать.
* Внешний общедоступный IPv4-адрес для каждого VPN-устройства. Этот IP-адрес не может располагаться вне преобразования сетевых адресов (NAT). Это обязательное требование.
* Вам потребуется установить последнюю версию командлетов Azure PowerShell. Дополнительные сведения об установке командлетов PowerShell см. в статье [Как установить и настроить Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Пользователь, имеющий опыт в настройке вашего оборудования VPN. Вы не сможете использовать для настройки VPN-устройств сценарии VPN, автоматически созданные на классическом портале Azure. Это означает, что вам нужно будет хорошо понимать, как настраивать VPN-устройство, или воспользоваться помощью соответствующего специалиста.
* Диапазоны IP-адресов, которые вы хотите использовать для виртуальной сети (если она еще не создана). 
* Диапазоны IP-адресов для каждого сайта локальной сети, к которым вы будете подключаться. Понадобится убедиться, что диапазоны IP-адресов сайтов локальной сети, к которым требуется подключиться, не перекрываются. В противном случае классический портал Azure или REST API отклонит передаваемую конфигурацию. 
  
    Например, если два сайта локальной сети содержат диапазон IP-адресов 10.2.3.0/24 и имеется пакет с адресом назначения 10.2.3.3, Azure не сможет определить сайт, на который требуется отправить пакет, так как диапазоны адресов перекрываются. Для предотвращения проблем с маршрутизацией Azure не позволит передать файл конфигурации с перекрывающимися диапазонами адресов.

## <a name="1-create-a-site-to-site-vpn"></a>1. Создание подключения VPN типа "сеть — сеть"
Это отлично, если у вас уже есть подключение VPN типа "сеть — сеть" со шлюзом с динамической маршрутизацией. Можно перейти к [экспорту параметров конфигурации виртуальной сети](#export). Если нет, выполните следующее:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Если у вас уже есть виртуальная сеть типа "сеть — сеть", но со шлюзом со статической маршрутизацией (на основе политики)
1. Измените тип шлюза на шлюз с динамической маршрутизацией. Для многосайтового подключения VPN требуется шлюз с динамической маршрутизацией (также известный как шлюз на основе маршрутов). Чтобы изменить тип шлюза, необходимо сначала удалить существующий шлюз, а затем создать новый. Инструкции см. в разделе [Как изменить тип маршрутизации VPN для шлюза](vpn-gateway-configure-vpn-gateway-mp.md#how-to-change-the-vpn-routing-type-for-your-gateway).  
2. Настройте новый шлюз и создайте VPN-туннель. Инструкции см. в разделе [Настройка VPN-шлюза на классическом портале Azure](vpn-gateway-configure-vpn-gateway-mp.md). Сначала измените тип шлюза на шлюз с динамической маршрутизацией. 

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Если у вас нет виртуальной сети типа "сеть — сеть"
1. Создайте виртуальную сеть типа "сеть — сеть" с помощью указаний в статье [Создание виртуальной сети с VPN-подключением типа "сеть — сеть" с помощью классического портала Azure](vpn-gateway-site-to-site-create.md).  
2. Настройте шлюз с динамической маршрутизацией с помощью следующей процедуры: [Настройка VPN-шлюза](vpn-gateway-configure-vpn-gateway-mp.md). Не забудьте выбрать **динамическую маршрутизацию** в качестве типа шлюза.

## <a name="a-nameexporta2-export-the-network-configuration-file"></a><a name="export"></a>2. Экспорт файла конфигурации сети
Экспортируйте свой файл конфигурации сети. Экспортируемый файл будет использоваться для настройки новых параметров нескольких сайтов. Указания о том, как экспортировать файл, см. в разделе [Как создать виртуальную сеть с помощью файла конфигурации сети на портале Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). 

## <a name="3-open-the-network-configuration-file"></a>3. Открытие файла конфигурации сети
Откройте файл конфигурации сети, скачанный на последнем шаге. Используйте любой редактор XML. Файл должен выглядеть следующим образом:

        <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <LocalNetworkSites>
              <LocalNetworkSite name="Site1">
                <AddressSpace>
                  <AddressPrefix>10.0.0.0/16</AddressPrefix>
                  <AddressPrefix>10.1.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
              </LocalNetworkSite>
              <LocalNetworkSite name="Site2">
                <AddressSpace>
                  <AddressPrefix>10.2.0.0/16</AddressPrefix>
                  <AddressPrefix>10.3.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
              </LocalNetworkSite>
            </LocalNetworkSites>
            <VirtualNetworkSites>
              <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
                <AddressSpace>
                  <AddressPrefix>10.20.0.0/16</AddressPrefix>
                  <AddressPrefix>10.21.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="FE">
                    <AddressPrefix>10.20.0.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="BE">
                    <AddressPrefix>10.20.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.20.2.0/29</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="Site1">
                      <Connection type="IPsec" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

## <a name="4-add-multiple-site-references"></a>4. Добавление ссылок на несколько сайтов
При добавлении или удалении данных ссылок на сайты будут внесены изменения в элемент ConnectionsToLocalNetwork/LocalNetworkSiteRef. При добавлении новой ссылки на локальный сайт Azure инициирует создание нового туннеля. В следующем примере приведена конфигурации сети для подключения одного сайта. Сохраните файл после внесения изменений.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

    To add additional site references (create a multi-site configuration), simply add additional "LocalNetworkSiteRef" lines, as shown in the example below: 

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

## <a name="5-import-the-network-configuration-file"></a>5. Импорт файла конфигурации сети
Импортируйте файл конфигурации сети. После импорта этого файла с изменениями будут добавлены новые туннели. Туннели будут использовать динамический шлюз, который был создан ранее. Указания о том, как импортировать файл, см. в разделе [Как создать виртуальную сеть с помощью файла конфигурации сети на портале Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). 

## <a name="6-download-keys"></a>6. Скачивание ключей
После добавления новых туннелей используйте командлет PowerShell `Get-AzureVNetGatewayKey` , чтобы получить общие ключи IPsec/IKE для каждого туннеля.

Например:

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

Кроме того, при желании можно использовать REST API *получения общего ключа шлюза виртуальной сети* для получения общих ключей.

## <a name="7-verify-your-connections"></a>7. Проверка подключений
Проверьте состояние многосайтового туннеля. После скачивания ключей для каждого туннеля необходимо проверить подключения. Используйте командлет `Get-AzureVnetConnection` , чтобы получить список туннелей виртуальной сети, как показано в примере ниже. VNet1 — имя виртуальной сети.

    Get-AzureVnetConnection -VNetName VNET1

    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

## <a name="next-steps"></a>Дальнейшие действия
Чтобы больше узнать о VPN-шлюзах, см. статью [Шлюзы VPN](vpn-gateway-about-vpngateways.md).




<!--HONumber=Dec16_HO2-->


