<properties
   pageTitle="Связывание виртуальной сети с каналом ExpressRoute с помощью классической модели развертывания и PowerShell | Microsoft Azure"
   description="В этом документе содержатся общие сведения о связывании виртуальных сетей с каналами ExpressRoute с помощью классической модели развертывания и PowerShell."
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/19/2016"
   ms.author="ganesr" />

# Связывание виртуальной сети с каналом ExpressRoute

> [AZURE.SELECTOR]
- [Портал Azure — Resource Manager](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell — Resource Manager](expressroute-howto-linkvnet-arm.md)
- [PowerShell — классическая модель](expressroute-howto-linkvnet-classic.md)



В этой статье содержатся сведения о связывании виртуальных сетей с каналами Azure ExpressRoute с помощью классической модели развертывания и PowerShell. Виртуальные сети могут быть в той же или другой подписке.

**О моделях развертывания Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Предварительные требования для настройки

1. Нужна последняя версия модулей Azure PowerShell. Последнюю версию модулей PowerShell можно скачать из раздела PowerShell на [странице скачивания Azure](https://azure.microsoft.com/downloads/). Пошаговые инструкции по настройке компьютера для использования модулей Azure PowerShell см. в разделе [Установка и настройка Azure PowerShell](../powershell-install-configure.md).
2. Прежде чем приступить к настройке, необходимо изучить [предварительные требования](expressroute-prerequisites.md), [требования к маршрутизации](expressroute-routing.md) и [рабочие процессы](expressroute-workflows.md).
3. Вам потребуется активный канал ExpressRoute.
	- Следуйте инструкциям, чтобы [создать канал ExpressRoute](expressroute-howto-circuit-classic.md) и включить его на стороне поставщика услуг подключения.
	- Убедитесь, что для вашего канала настроен частный пиринг Azure. Инструкции по маршрутизации см. в статье [Настройка маршрутизации](expressroute-howto-routing-classic.md).
	- Для создания сквозного подключения обязательно настройте частный пиринг Azure, а также пиринг BGP между вашей сетью и сетью Майкрософт.
    - Вам необходимо иметь созданные и полностью подготовленные виртуальную сеть и шлюз виртуальной сети. Следуйте инструкциям по [настройке виртуальной сети для ExpressRoute](expressroute-howto-vnet-portal-classic.md).

Вы можете связать с каналом ExpressRoute до 10 виртуальных сетей включительно. Все виртуальные машины должны находиться в одном геополитическом регионе. При использовании надстройки Premium вы сможете связать больше виртуальных сетей с каналом ExpressRoute или связать виртуальные сети, которые находятся в других геополитических регионах. Дополнительную информацию о надстройке "Премиум" см. в разделе [Вопросы и ответы](expressroute-faqs.md).

## Подключение к каналу виртуальной сети в той же подписке

Вы можете связать виртуальную сеть с каналом ExpressRoute, используя следующий командлет. Убедитесь в наличии шлюза виртуальной сети и его готовности к связыванию, прежде чем запускать командлет.

	New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
	Provisioned

## Подключение к каналу виртуальной сети в другой подписке

Канал ExpressRoute может совместно использоваться несколькими подписками. На рисунке ниже схематично показан способ совместного использования каналов ExpressRoute несколькими подписками.

Каждое маленькое облако внутри большого облака представляет подписки, принадлежащие различным подразделениям одной организации. Любое подразделение в организации может использовать свою собственную подписку для развертывания служб. Но оно может совместно использовать один выделенный канал ExpressRoute для подключения к локальной сети. Владельцем канала ExpressRoute может выступать одно подразделение (в данном примере — ИТ-подразделение). Другие подписки в организации могут использовать канал ExpressRoute.

>[AZURE.NOTE] Плата за подключение выделенного канала ExpressRoute и использование полосы пропускания будет взиматься с владельца выделенного канала. Полоса пропускания распределяется между всеми виртуальными сетями.

![Подключение между подписками](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### Администрирование

*Владелец канала* — это администратор или соадминистратор подписки, в которой создан канал ExpressRoute. Владелец канала может разрешить использовать свой выделенный канал администраторам и соадминистраторам других подписок (на схеме рабочего процесса они называются *пользователями канала*). Пользователи, получившие разрешение на использование канала ExpressRoute организации, смогут связать виртуальную сеть в своей подписке с этим каналом ExpressRoute.

Владелец канала имеет право изменить или отменить авторизацию в любое время. Отмена авторизации приводит к удалению всех ссылок из подписки, доступ которой был отменен.

### Действия владельца канала

#### Создание разрешения

Владелец канала разрешает администраторам других подписок использовать указанный канал. В приведенном ниже примере администратор канала (Contoso IT) разрешает администратору другой подписки (Dev-Test) привязать к этому каналу две виртуальные сети. Администратор Contoso IT разрешает это, указав идентификатор Майкрософт подписки Dev-Test. Командлет не отправляет по электронной почте указанный идентификатор Майкрософт. Владелец канала должен сам уведомить владельца другой подписки о том, что авторизация завершена.

	New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'

	Description         : Dev-Test Links
	Limit               : 2
	LinkAuthorizationId : **********************************
	MicrosoftIds        : devtest@contoso.com
	Used                : 0

#### Просмотр разрешений

Владелец канала может просмотреть все разрешения, выданные для определенного канала, выполнив следующий командлет.

	Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"

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

	Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5

	Description         : Dev-Test Links
	Limit               : 5
	LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
	MicrosoftIds        : devtest@contoso.com
	Used                : 0


#### Удаление разрешений

Владелец канала может отзывать (удалять) разрешения, выданные пользователю, с помощью следующего командлета.

	Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### Действия пользователя канала

#### Просмотр разрешений

Пользователь канала может просматривать разрешения с помощью следующего командлета.

	Get-AzureAuthorizedDedicatedCircuit

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

Пользователь канала может активировать разрешение на связь, выполнив следующий командлет.

	New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'

	State VnetName
	----- --------
	Provisioned SalesVNET1

## Дальнейшие действия

Дополнительную информацию об ExpressRoute см. в статье [Часто задаваемые вопросы об ExpressRoute](expressroute-faqs.md).

<!---HONumber=AcomDC_0810_2016-->