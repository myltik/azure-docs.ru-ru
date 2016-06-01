<properties
   pageTitle="Создание и изменение канала ExpressRoute с помощью диспетчера ресурсов и PowerShell | Microsoft Azure"
   description="В этой статье описывается создание, подготовка, проверка, обновление, удаление и отзыв канала ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/15/2016"
   ms.author="ganesr"/>


# Создание и изменение канала ExpressRoute

> [AZURE.SELECTOR]
[Azure Portal - Resource Manager](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)
[PowerShell - Classic](expressroute-howto-circuit-classic.md)


В этой статье объясняется, как создать канал Azure ExpressRoute, используя командлеты Windows PowerShell и модель развертывания диспетчера ресурсов Azure. В этой статье описывается, как проверить состояние канала, обновить его или удалить и отозвать.

**О моделях развертывания Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Перед началом работы


- Установите последнюю версию модулей Azure PowerShell (не ниже 1.0). Пошаговые инструкции по настройке компьютера для использования модулей PowerShell см. на странице [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

- Изучите [предварительные требования](expressroute-prerequisites.md) и [рабочие процессы](expressroute-workflows.md), прежде чем приступить к настройке.

## Создание и предоставление канала ExpressRoute

### 1\. Войдите в учетную запись Azure и выберите подписку.

Чтобы начать настройку, войдите в свою учетную запись Azure. Дополнительные сведения о PowerShell см. в статье [Использование Windows PowerShell с диспетчером ресурсов](../powershell-azure-resource-manager.md). Для подключения используйте следующие примеры:

	Login-AzureRmAccount

Просмотрите подписки учетной записи.

	Get-AzureRmSubscription

Выберите подписку, необходимую для создания канала ExpressRoute для следующих операций.

	Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

### 2\. Получение списка поддерживаемых поставщиков, расположений и значений пропускной способности

Перед созданием канала ExpressRoute потребуется список поддерживаемых поставщиков услуг подключения, расположений и вариантов пропускной способности.

Командлет PowerShell `Get-AzureRmExpressRouteServiceProvider` возвращает эти сведения, которые будут использоваться в последующих шагах.

	Get-AzureRmExpressRouteServiceProvider

Проверьте, указан ли в списке поставщик услуг подключения. Запишите следующие данные, так как они потребуются позже, при создании канала.

- Имя

- PeeringLocations

- BandwidthsOffered

Теперь все готово к созданию канала ExpressRoute.

### 3\. Создание канала ExpressRoute

Перед созданием канала ExpressRoute необходимо создать группу ресурсов (если вы этого еще не сделали) с помощью следующей команды:


	New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"


В приведенном ниже примере показано, как создать канал ExpressRoute со скоростью 200 Мбит/с каналом через Equinix в Кремниевой долине. Если вы используете другой поставщик и другие параметры, подставьте в запрос соответствующие данные. Пример запроса нового ключа службы:

	New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200

Убедитесь, что указаны правильный уровень SKU и семейство SKU:

- Уровень SKU определяет, какая надстройка включена — ExpressRoute Standard или ExpressRoute Premium. Укажите *Стандартый*, чтобы получить стандартный SKU, или *Премиум*, чтобы получить надстройку категории "Премиум".

- Семейство SKU определяет тип выставления счетов. Выберите *Metereddata* для тарифного плана с оплатой за трафик или *Unlimiteddata* для безлимитного тарифного плана. Обратите внимание, что тип выставления счетов можно изменить с *Metereddata* на *Unlimiteddata*, но не наоборот.


>[AZURE.IMPORTANT] Выставление счетов за использование ExpressRoute начинается после того, как клиент получает служебный ключ. Обязательно выполните эту операцию, как только поставщик услуг подключения будет готов предоставить канал.

Ответ будет содержать ключ службы. Подробное описание всех параметров можно получить, выполнив следующую команду:


	get-help New-AzureRmExpressRouteCircuit -detailed


### 4\. Получение списка всех каналов ExpressRoute

Чтобы получить список всех созданных вами каналов ExpressRoute, выполните команду `Get-AzureRmExpressRouteCircuit`.


	Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Вы получите ответ следующего вида:


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
	CircuitProvisioningState          : Enabled
	ServiceProviderProvisioningState  : NotProvisioned
	ServiceProviderNotes              :
	ServiceProviderProperties         : {
	                                      "ServiceProviderName": "Equinix",
	                                      "PeeringLocation": "Silicon Valley",
	                                      "BandwidthInMbps": 200
	                                    }
	ServiceKey                        : **************************************
	Peerings                          : []

Вы можете получить эти сведения в любое время с помощью командлета `Get-AzureRmExpressRouteCircuit`. Если командлет вызывается без параметров, выводится список всех каналов. Ваш ключ службы будет показан в поле *ServiceKey*.


	Get-AzureRmExpressRouteCircuit


Вы получите ответ следующего вида:


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
	ServiceProviderProvisioningState : NotProvisioned
	ServiceProviderNotes             :
	ServiceProviderProperties        : {
	                                     "ServiceProviderName": "Equinix",
	                                     "PeeringLocation": "Silicon Valley",
	                                     "BandwidthInMbps": 200
	   		                           }
	ServiceKey                       : **************************************
	Peerings                         : []


Подробное описание всех параметров можно получить, выполнив следующую команду:


	get-help Get-AzureRmExpressRouteCircuit -detailed

### 5\. Отправка ключа службы поставщику услуг подключения для подготовки

Параметр *ServiceProviderProvisioningState* предоставляет сведения о текущем состоянии подготовки на стороне поставщика услуг. Параметр Status предоставляет состояние на стороне Майкрософт. Дополнительные сведения о состояниях подготовки канала см. в статье [Рабочие процессы](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Вновь созданный канал ExpressRoute будет иметь следующее состояние:


	ServiceProviderProvisioningState : NotProvisioned
	CircuitProvisioningState         : Enabled



Когда поставщик услуг подключения находится в процессе его активации, канал переходит в следующее состояние:

	ServiceProviderProvisioningState : Provisioning
	Status                           : Enabled

Для того чтобы канал ExpressRoute можно было использовать, он должен находиться в следующем состоянии:

	ServiceProviderProvisioningState : Provisioned
	CircuitProvisioningState         : Enabled

### 6\. Периодическая проверка состояния и статуса ключа канала

Проверка состояния и состояния ключа канала позволит вам узнать, когда поставщик активирует ваш канал. После настройки канала значение параметра *ServiceProviderProvisioningState* изменится на *Provisioned*, как показано в ниже.


	Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


Вы получите ответ следующего вида:


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

### 7\. Создание конфигурации маршрутизации

Пошаговые инструкции по созданию и изменению пиринга каналов см. в статье [Создание и изменение маршрутизации для канала ExpressRoute](expressroute-howto-routing-arm.md).


>[AZURE.IMPORTANT] Эти инструкции распространяются только на каналы от поставщиков, предоставляющих услуги подключения второго уровня. Если вы работаете с поставщиком, предлагающим услуги третьего уровня (обычно это IPVPN, например MPLS), то ваш поставщик услуг подключения выполнит настройку и управление конфигурацией самостоятельно.

### 8\. Связывание виртуальной сети с каналом ExpressRoute

Теперь свяжите виртуальную сеть с каналом ExpressRoute. При работе с моделью развертывания Resource Manager пользуйтесь статьей [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md).

## Получение состояния канала ExpressRoute

Вы можете получить эти сведения в любое время с помощью командлета `Get-AzureRmExpressRouteCircuit`. Если командлет вызывается без параметров, выводится список всех каналов.

	Get-AzureRmExpressRouteCircuit


Ответ будет выглядеть примерно так:


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


Для получения сведений об определенном канале ExpressRoute передайте имя группы ресурсов и имя канала как параметр вызова.


	Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


Вы получите ответ следующего вида:


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


Подробное описание всех параметров можно получить, выполнив следующую команду:

	get-help get-azurededicatedcircuit -detailed


## <a name="modify"></a>Изменение канала ExpressRoute

Некоторые свойства канала ExpressRoute можно изменить, не повлияв на подключение.

Вы можете выполнять следующие действия без простоя:

- включать и отключать надстройку ExpressRoute "Премиум" для канала ExpressRoute;
- увеличивать пропускную способность канала ExpressRoute. Обратите внимание, что снижение пропускной способности канала не поддерживается.
- Перейдите с тарифного плана с оплатой за трафик на безлимитный тарифный план. Обратите внимание, что переход с безлимитного тарифного плана на тарифный план с оплатой за трафик не поддерживается.
-  Параметр *Allow Classic Operations* (Разрешить классические операции) можно включать и отключать.

Дополнительные сведения об ограничениях см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).

### Включение надстройки ExpressRoute "Премиум"

Вы можете включить надстройку ExpressRoute "Премиум" для существующего канала с помощью следующего фрагмента кода PowerShell:

	$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	$ckt.Sku.Tier = "Premium"
	$ckt.sku.Name = "Premium_MeteredData"

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


Теперь для канала включены функции надстройки ExpressRoute "Премиум". Обратите внимание, что с момента успешного выполнения команды мы начнем тарифицировать надстройку "Премиум".

### Отключение надстройки ExpressRoute "Премиум"

>[AZURE.IMPORTANT] Операция может завершиться ошибкой, если использовать больше ресурсов, чем разрешено для канала "Стандартный".

Обратите внимание на следующее.

- Прежде чем переходить с канала "Премиум" на "Стандартный", убедитесь, что к каналу привязано менее 10 виртуальных сетей. Если этого не сделать, запрос на обновление завершится ошибкой и мы будем выставлять вам счета по тарифам ценовой категории "Премиум".

- Все связи с виртуальными сетями в других геополитических регионах необходимо разорвать. Если этого не сделать, запрос на обновление завершится ошибкой и мы будем выставлять вам счета по тарифам ценовой категории "Премиум".

- Для частного пиринга таблица маршрутов должна содержать менее 4000 маршрутов. Если в ней больше 4000 маршрутов, сеанс BGP будет сброшен. Его можно будет снова активировать только после того, как количество объявленных префиксов станет меньше 4000.

Вы можете отключить надстройку ExpressRoute "Премиум" для существующего канала с помощью следующего командлета PowerShell:


	$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	$ckt.Sku.Tier = "Standard"
	$ckt.sku.Name = "Standard_MeteredData"

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### Обновление пропускной способности канала ExpressRoute

Параметры пропускной способности, поддерживаемые для вашего поставщика, приведены в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md). Можно выбрать любой размер, больший, чем размер существующего канала.

>[AZURE.IMPORTANT] Уменьшить пропускную способность канала ExpressRoute без прерывания его работы нельзя. Для снижения пропускной способности нужно будет отозвать канал ExpressRoute и повторно подготовить новый канал ExpressRoute.

Выберите необходимый размер и используйте следующую команду для изменения размера канала:


	$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


Размер канала будет изменен со стороны Майкрософт. Свяжитесь с поставщиком услуг подключения и попросите обновить конфигурации с его стороны в соответствии с этим изменением. Когда вы сообщите поставщику услуг об изменении размера канала, мы начнем выставлять счета за обновленную пропускную способность.


### Перевод SKU с измеряемых на неограниченные данные

Изменить SKU канала ExpressRoute можно, используя следующий фрагмент кода PowerShell:

	$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	$ckt.Sku.Family = "UnlimitedData"
	$ckt.sku.Name = "Premium_UnlimitedData"

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

### Управление доступом к классической среде и среде диспетчера ресурсов  

Инструкции см. в статье [Перемещение каналов ExpressRoute из классической модели развертывания в модель Resource Manager](expressroute-howto-move-arm.md).


## Удаление и отзыв канала ExpressRoute

Обратите внимание на следующее:

- Связь между ExpressRoute и всеми виртуальными сетями необходимо разорвать. Если операция завершится ошибкой, проверьте, не привязаны ли к каналу какие-либо виртуальные сети.

- Если подготовка поставщика услуг канала ExpressRoute включена, ее состояние изменится с "Включено" на *Отключение*. Свяжитесь с поставщиком услуг, чтобы отменить подготовку канала с его стороны. Мы будем резервировать ресурсы и выставлять вам счета до тех пор, пока поставщик услуг не завершит отзыв канала и не отправит нам соответствующее уведомление.

- Если поставщик услуг отзовет канал (состояние подготовки у поставщика услуг изменилось на *Не подготовлено*) до выполнения указанного выше командлета, мы отзовем канал и перестанем выставлять вам счета.

Для удаления канала ExpressRoute выполните следующую команду:

	Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"



## Дальнейшие действия

После создания канала обязательно выполните действия, описанные в следующих статьях:

- [Создание и изменение маршрутизации для канала ExpressRoute](expressroute-howto-routing-arm.md)
- [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0518_2016-->