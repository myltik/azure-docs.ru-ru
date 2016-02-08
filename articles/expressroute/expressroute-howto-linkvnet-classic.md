<properties 
   pageTitle="Связывание виртуальных сетей с каналами ExpressRoute | Microsoft Azure"
   description="В этой статье кратко описывается процедура связывания виртуальных сетей с каналами ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/16/2016"
   ms.author="cherylmc" />

# Связывание виртуальной сети с каналами ExpressRoute

> [AZURE.SELECTOR]
- [PowerShell - Classic](expressroute-howto-linkvnet-classic.md)
- [PowerShell - Resource Manager] (expressroute-howto-linkvnet-arm.md)  
- [Template - Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection)

В этой статье приводится краткое описание связывания виртуальных сетей с каналами ExpressRoute. Виртуальные сети могут быть в той же или другой подписке. Процедуры в этой статье относятся к виртуальным сетям, развернутым с помощью классической модели развертывания. Если требуется связать виртуальную сеть, которая была развернута с помощью метода развертывания диспетчера ресурсов Azure, это можно сделать с помощью шаблона. См. вкладку выше, чтобы перейти в шаблону.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Предварительные требования для настройки

- Вам потребуется последняя версия модулей Azure PowerShell. Последнюю версию модуля PowerShell можно загрузить из раздела PowerShell на [странице загрузок Azure](https://azure.microsoft.com/downloads/). Пошаговые инструкции по настройке компьютера для использования модулей Azure PowerShell приведены в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md). 
- Не забудьте изучить [предварительные требования](expressroute-prerequisites.md), [требования к маршрутизации](expressroute-routing.md) и [рабочие процессы](expressroute-workflows.md), прежде чем приступать к настройке.
- Вам потребуется активный канал ExpressRoute. 
	- Следуйте инструкциям, чтобы [создать канал ExpressRoute](expressroute-howto-circuit-classic.md) и включить его на стороне поставщика услуг подключения. 
	- Убедитесь, что для вашего канала настроен частный пиринг Azure. Инструкции по маршрутизации см. в статье [Настройка маршрутизации](expressroute-howto-routing-classic.md). 
	- Для создания сквозного подключения вам потребуется настроить частный пиринг Azure, а также пиринг BGP между вашей сетью и сетью Майкрософт.

Канал ExpressRoute позволяет связать до 10 виртуальных сетей. Все каналы ExpressRoute должны находиться в одном геополитическом регионе. При использовании надстройки Premium вы сможете связать больше виртуальных сетей с каналом ExpressRoute. Подробная информация о надстройке Premium приведена в статье [Вопросы и ответы](expressroute-faqs.md).

## Связывание виртуальной сети из одной подписки Azure с каналом ExpressRoute

Вы можете связать виртуальную сеть с каналом ExpressRoute, используя следующий командлет. Убедитесь в наличии шлюза виртуальной сети и его готовности к связыванию, прежде чем запускать командлет.

	C:\> New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
	Provisioned

## Связывание виртуальной сети из другой подписки Azure с каналом ExpressRoute

Один канал ExpressRoute может одновременно использоваться несколькими подписками. На рисунке ниже схематично показан способ совместного использования каналов ExpressRoute несколькими подписками. Каждое маленькое облако внутри большого облака представляет подписки, принадлежащие различным подразделениям одной организации. Любое подразделение в организации может использовать свою собственную подписку для развертывания служб. Кроме того, оно может совместно использовать один выделенный канал ExpressRoute для подключения к корпоративной сети. Владельцем канала ExpressRoute может выступать одно подразделение (в данном примере — ИТ-подразделение). Другие подписки в организации могут использовать канал ExpressRoute.

>[AZURE.NOTE] Плата за подключение выделенного канала ExpressRoute и использование полосы пропускания будет взиматься с владельца выделенного канала. Полоса пропускания распределяется между всеми виртуальными сетями.

![Подключение между подписками](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### Администрирование

*Владелец канала* — это администратор или соадминистратор подписки, в которой создан канал ExpressRoute. Владелец канала может разрешить использовать свой выделенный канал администраторам и соадминистраторам других подписок (на схеме рабочего процесса они называются *пользователями канала*). После получения разрешения пользователи корпоративного канала ExpressRoute смогут связать виртуальную сеть в своей подписке с корпоративным каналом ExpressRoute.

Владелец канала имеет право изменить или отменить авторизацию в любое время. Отмена авторизации приводит к удалению всех ссылок из подписки, доступ которой был отменен.

### Действия владельца канала 

#### Создание разрешения
	
Владелец канала разрешает администраторам других подписок использовать указанный канал. В приведенном ниже примере администратор канала (Contoso IT) разрешает использовать канал администратору другой подписки (Dev-Test). Для этого администратор канала указывает идентификатор Майкрософт другого администратора и разрешает ему подключать не более двух виртуальных сетей. Командлет не отправляет по электронной почте указанный идентификатор Майкрософт. Владелец канала должен сам уведомить владельца другой подписки о том, что проверка подлинности завершена.

		PS C:\> New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
		
		Description         : Dev-Test Links 
		Limit               : 2 
		LinkAuthorizationId : ********************************** 
		MicrosoftIds        : devtest@contoso.com 
		Used                : 0

#### Просмотр разрешений

Владелец канала может просмотреть все разрешения, выданные для определенного канала, выполнив следующий командлет.

	PS C:\> Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
	
	Description         : EngineeringTeam 
	Limit               : 3 
	LinkAuthorizationId : #################################### 
	MicrosoftIds        : engadmin@contoso.com 
	Used                : 1 
	
	Description         : MarketingTeam 
	Limit               : 1 
	LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ 
	MicrosoftIds        : marketingadmin@contoso.com 
	Used                : 0 
	
	Description         : Dev-Test Links 
	Limit               : 2 
	LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
	MicrosoftIds        : salesadmin@contoso.com 
	Used                : 2 
	

#### Изменение разрешений

Владелец канала может изменять разрешения с помощью следующего командлета.

	PS C:\> set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
		
	Description         : Dev-Test Links 
	Limit               : 5 
	LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
	MicrosoftIds        : devtest@contoso.com 
	Used                : 0


#### Удаление разрешений

Владелец канала может отзывать (удалять) разрешения, выданные пользователю, с помощью следующего командлета.

	PS C:\> Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### Действия пользователя канала

#### Просмотр разрешений

Пользователь канала может просматривать разрешения с помощью следующего командлета.

	PS C:\> Get-AzureAuthorizedDedicatedCircuit
		
	Bandwidth                        : 200
	CircuitName                      : ContosoIT
	Location                         : Washington DC
	MaximumAllowedLinks              : 2
	ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
	ServiceProviderName              : equinix
	ServiceProviderProvisioningState : Provisioned
	Status                           : Enabled
	UsedLinks                        : 0

#### Активация разрешений на связь

Пользователь канала может активировать разрешение на связь, запустив следующий командлет.

	PS C:\> New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1' 
		
	State VnetName 
	----- -------- 
	Provisioned SalesVNET1

## Дальнейшие действия

Дополнительную информацию об ExpressRoute см. в статье [Часто задаваемые вопросы об ExpressRoute](expressroute-faqs.md).

<!---HONumber=AcomDC_0128_2016-->