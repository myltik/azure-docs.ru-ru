<properties 
   pageTitle="Сведения о шлюзах виртуальных сетей ExpressRoute | Microsoft Azure"
   description="Сведения о шлюзах виртуальных сетей ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/21/2016"
   ms.author="cherylmc" />

# Сведения о шлюзах виртуальных сетей ExpressRoute


Шлюз виртуальной сети используется для обмена сетевым трафиком между виртуальными и локальными сетями Azure. В процессе настройки подключения ExpressRoute необходимо создать и настроить шлюз виртуальной сети и подключение для него.

При создании шлюза виртуальной сети нужно указать несколько параметров. Один из обязательных параметров указывает, будет ли шлюз использоваться для трафика ExpressRoute или VPN-шлюза. В модели развертывания Resource Manager это параметр -GatewayType.

Если сетевой трафик направляется через выделенное частное подключение, используется тип шлюза ExpressRoute. Он также называется шлюзом ExpressRoute. Если сетевой трафик направляется в зашифрованном виде через общедоступное подключение, используется тип шлюза Vpn. Он также называется VPN-шлюзом. При подключениях типа "сеть — сеть", "точка — сеть" и "виртуальная сеть — виртуальная сеть" используется VPN-шлюз.

В виртуальной сети каждому типу шлюза может соответствовать только один шлюз виртуальной сети. Например, у вас может быть только один шлюз виртуальной сети типа VPN и только один типа ExpressRoute. Эта статья посвящена шлюзам виртуальных сетей ExpressRoute.

## <a name="gwsku"></a>SKU шлюзов

[AZURE.INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Если для создания шлюза виртуальной сети Resource Manager используется портал Azure, для шлюза по умолчанию указывается SKU Standard. На данный момент на портале Azure нельзя указать другой SKU для модели развертывания с помощью Resource Manager. Тем не менее после создания шлюз можно обновить до SKU более мощного шлюза (например, Basic или Standard до HighPerformance) с помощью командлета PowerShell "Resize-AzureRmVirtualNetworkGateway".

###  <a name="aggthroughput"></a>Расчетная суммарная пропускная способность в зависимости от SKU шлюза


В следующей таблице приведены типы шлюзов с приблизительной суммарной пропускной способностью. Эта таблица относится к классической модели развертывания и модели диспетчера ресурсов.

[AZURE.INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]


## <a name="resources"></a>Интерфейсы REST API и командлеты PowerShell

Дополнительные технические материалы и специальные требования к синтаксису, действующие при использовании интерфейсов REST API и командлетов PowerShell для настройки конфигураций шлюзов виртуальных сетей, доступны по приведенным ниже ссылкам.

|**Классический** | **Диспетчер ресурсов**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[ИНТЕРФЕЙС REST API](https://msdn.microsoft.com/library/jj154113.aspx)|[ИНТЕРФЕЙС REST API](https://msdn.microsoft.com/library/mt163859.aspx)|


## Дальнейшие действия

Дополнительные сведения о доступных конфигурациях подключений см. в статье [Технический обзор ExpressRoute](expressroute-introduction.md).







 

<!---HONumber=AcomDC_0921_2016-->