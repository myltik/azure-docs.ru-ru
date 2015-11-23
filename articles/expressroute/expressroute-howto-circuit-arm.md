<properties
   pageTitle="Настройка канала ExpressRoute с помощью диспетчера ресурсов Azure и PowerShell | Microsoft Azure"
   description="В этой статье описана процедура создания и подготовки канала ExpressRoute, а также показано, как проверить состояние, обновить или удалить и отозвать канал."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/06/2015"
   ms.author="cherylmc"/>

# Создание и изменение канала ExpressRoute с помощью диспетчера ресурсов Azure и PowerShell

> [AZURE.SELECTOR]
[PowerShell - Classic](expressroute-howto-circuit-classic.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

В этой статье описано, как создать канал ExpressRoute, используя командлеты PowerShell и модель развертывания диспетчера ресурсов Azure. Ниже описывается, как проверить состояние, обновить или удалить и отозвать канал ExpressRoute.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Предварительные требования для настройки

- Вам потребуются последние модули Azure PowerShell версии 1.0 или более поздней. Пошаговые инструкции по настройке компьютера для использования модулей Azure PowerShell приведены в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md). 
- Не забудьте изучить страницы [Предварительные требования](expressroute-prerequisites.md) и [Рабочие процессы](expressroute-workflows.md), прежде чем приступить к настройке.

## Создание и подготовка канала ExpressRoute

1. **Импортируйте модуль PowerShell для ExpressRoute.**

 	Чтобы начать использовать командлеты ExpressRoute, необходимо установить последнюю версию установщика Powershell из [коллекции PowerShell](http://www.powershellgallery.com/) и импортировать модули диспетчера ресурсов Azure в сеанс PowerShell. Будет необходимо запустить PowerShell от имени администратора.

	    Install-Module AzureRM

		Install-AzureRM

	Импортируйте все модули AzureRM.* в рамках диапазона известных семантических версий

		Import-AzureRM

	Можно также просто импортировать выбранный модуль в рамках диапазона известных семантических версий.
		
		Import-Module AzureRM.Network 

	Войдите в свою учетную запись

		Login-AzureRmAccount

	Выберите подписку, необходимую для создания канала ExpressRoute
		
		Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
			


2. **Получите список поддерживаемых поставщиков, расположений и значений пропускной способности.**

	Перед созданием канала ExpressRoute потребуется список поставщиков услуг, поддерживаемых расположений и значений пропускной способности. Командлет PowerShell *Get-AzureRmExpressRouteServiceProvider* возвращает сведения, которые будут использоваться в последующих шагах.

		PS C:\> Get-AzureRmExpressRouteServiceProvider

	Проверьте, указан ли в списке поставщик услуг подключения. Запишите следующую информацию, так как она потребуется для создания каналов.
	
	- Имя
	- PeeringLocations
	- BandwidthsOffered

	Теперь вы готовы создать канал ExpressRoute.

		
3. **Создайте канал ExpressRoute.**

	Перед созданием канала ExpressRoute необходимо создать группу ресурсов. Это можно сделать с помощью следующей команды.

		New-AzureRmResourceGroup -Name “ExpressRouteResourceGroup” -Location "West US"

	В приведенном ниже примере показано, как создать канал ExpressRoute со скоростью 200 Мбит/с каналом через Equinix в Кремниевой долине. Если вы используете другого поставщика и другие параметры, подставьте их данные при выполнении запроса.

	Ниже приведен пример запроса нового ключа службы.

		New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200

	Убедитесь, что указаны правильный уровень SKU и семейство SKU.
 
	 - Уровень SKU определяет, какая надстройка включена — ExpressRoute Standard или ExpressRoute Premium. Можно указать *Standard*, чтобы получить надстройку Standard SKU, или *Premium*, чтобы получить надстройку Premium.
	 - Семейство SKU определяет тип выставления счетов. Можно выбрать *metereddata* для тарифного плана с оплатой за трафик и "unlimiteddata" для безлимитного тарифного плана. **Примечание.** После создания канала вы не сможете изменить тип выставления счетов.

	Ответ будет содержать ключ службы. Подробное описание всех параметров можно получить, выполнив следующую команду:

		get-help New-AzureRmExpressRouteCircuit -detailed 

4. **Получите список всех каналов ExpressRoute.**

	Для получения списка всех созданных каналов ExpressRoute можно использовать команду *Get-AzureRmExpressRouteCircuit*.

		#Getting service key
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
		ServiceProviderProvisioningState : NotProvisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []


	Вы можете получить эти сведения в любое время с помощью командлета *Get-AzureRmExpressRouteCircuit*. Если выполнить этот вызов без параметров, то будут перечислены все каналы. Ваш ключ службы будет приведен в поле *ServiceKey*.

		Get-AzureRmExpressRouteCircuit

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

5. **Отправьте ключ службы поставщику подключения для инициализации.**

	При создании нового канала ExpressRoute он будет иметь следующее состояние:
	
		ServiceProviderProvisioningState : NotProvisioned
		
		CircuitProvisioningState         : Enabled

	Параметр *ServiceProviderProvisioningState* сообщает сведения о текущем состоянии подготовки на стороне поставщика службы, а параметр Status — состояние на стороне корпорации Майкрософт. Для того чтобы канал ExpressRoute можно было использовать, он должен находиться в следующем состоянии.

		ServiceProviderProvisioningState : Provisioned
		
		CircuitProvisioningState         : Enabled

	Когда поставщик услуг подключения находится в процессе его активации, канал переходит в следующее состояние:

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled



5. **Периодически проверяйте состояние и статус ключа канала.**

	Это позволяет узнать, когда поставщик включил ваш канал. После настройки канала значение параметра *ServiceProviderProvisioningState* изменится на *Подготовлен*, как показано в следующем примере.

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

6. **Создайте конфигурацию маршрутизации.**
	
	Пошаговые инструкции см. на странице [Конфигурация маршрутизации канала ExpressRoute (создание и изменение пиринга каналов)](expressroute-howto-routing-arm.md).

7. **Свяжите виртуальную сеть с каналом ExpressRoute.**

	Теперь свяжите виртуальную сеть с каналом ExpressRoute. Пошаговые инструкции см. на странице [Связывание каналов ExpressRoute с виртуальными сетями](expressroute-howto-linkvnet-arm.md). Инструкции по созданию виртуальной сети для ExpressRoute см. в статье [Создание виртуальной сети для ExpressRoute](expressroute-howto-createvnet-classic.md).

##  Получение состояния канала ExpressRoute

Вы можете получить эти сведения в любое время с помощью командлета *Get-AzureRmExpressRouteCircuit*. Если выполнить этот вызов без параметров, то будут перечислены все каналы.

		Get-AzureRmExpressRouteCircuit

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

	The response will be something similar to the example below:

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

## Изменение канала ExpressRoute

Некоторые свойства канала ExpressRoute можно изменить, не повлияв на подключение.

Можно сделать следующее:

- Включить или выключить надстройку Premium для канала ExpressRoute без простоев.
- Увеличить пропускную способность канала ExpressRoute без простоев.

Более подробные сведения об ограничениях см. на странице [ExpressRoute: часто задаваемые вопросы](expressroute-faqs.md).

### Включение надстройки ExpressRoute Premium

Вы можете включить надстройку ExpressRoute Premium для существующего канала с помощью следующего фрагмента PowerShell:

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		$ckt.Sku.Name = "Premium"
		$ckt.sku.Name = "Premium_MeteredData"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
	
		
Теперь для вашего канала включена надстройка ExpressRoute Premium. Обратите внимание, что с момента успешного выполнения команды мы начнем тарифицировать надстройку Premium.

### Выключение надстройки ExpressRoute Premium

Вы можете выключить надстройку ExpressRoute Premium для существующего канала с помощью следующего командлета PowerShell:
	
		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
		
		$ckt.Sku.Tier = "Standard"
		$ckt.sku.Name = "Standard_MeteredData"
		
		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


Теперь надстройка Premium для вашего канала выключена.

Обратите внимание, что эта операция может завершиться ошибкой, если использовать больше ресурсов, чем разрешено для стандартного канала.

- Прежде чем переходить с надстройки Premium на Standard, убедитесь, что к каналу привязано меньше 10 виртуальных сетей. Если этого не сделать, запрос на обновление завершится ошибкой и вам будет выставлен счет по тарифам Premium.
- Все связи с виртуальными сетями в других геополитических регионах необходимо разорвать. Если этого не сделать, запрос на обновление завершится ошибкой и вам будет выставлен счет по тарифам Premium.
- Для частного пиринга таблица маршрутизации должна содержать меньше 4000 маршрутов. Если в таблице маршрутизации больше 4000 маршрутов, сеанс BGP будет сброшен и снова активирован только после того, как количество объявленных префиксов станет меньше 4000.


### Обновление пропускной способности канала ExpressRoute

Параметры пропускной способности, поддерживаемые для вашего поставщика, см. на странице [ExpressRoute: часто задаваемые вопросы](expressroute-faqs.md). Можно выбрать любой размер, больший, чем размер существующего канала. Решив, какой размер вам необходим, вы можете использовать следующую команду для изменения размера канала.

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Размер вашего канала будет изменен со стороны Майкрософт. Свяжитесь с поставщиком услуг подключения и попросите обновить конфигурации с его стороны в соответствии с этим изменением. Обратите внимание, что с этого момента мы начнем тарифицировать обновленный параметр пропускной способности.

>[AZURE.IMPORTANT]Уменьшить пропускную способность канала ExpressRoute без прерывания его работы нельзя. Для снижения пропускной способности нужно будет отозвать канал ExpressRoute и повторно подготовить новый канал ExpressRoute.

## Удаление и отзыв канала ExpressRoute

Для удаления канала ExpressRoute выполните следующую команду:

		Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"

Обратите внимание, что для успешного выполнения этой операции необходимо разорвать связи между ExpressRoute и всеми виртуальными сетями. Если операция завершится ошибкой, проверьте, не привязаны ли к каналу какие-либо виртуальные сети.

Если подготовка поставщика услуг канала ExpressRoute включена, ее состояние изменится с "включено" на *отключено*. Свяжитесь с поставщиком услуг, чтобы отменить подготовку канала с его стороны. Мы будем резервировать ресурсы и выставлять вам счета до тех пор, пока поставщик услуг не завершит отзыв канала и не отправит нам соответствующее уведомление.

Если поставщик услуг отзовет канал (состояние подготовки у поставщика услуг изменилось на *не подготовлено*) до выполнения указанного выше командлета, мы отзовем канал и перестанем выставлять вам счета.

## Дальнейшие действия

- [Настройка маршрутизации](expressroute-howto-routing-arm.md)
- [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md) 

<!---HONumber=Nov15_HO3-->