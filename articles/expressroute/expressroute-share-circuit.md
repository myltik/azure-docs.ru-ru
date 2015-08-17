<properties 
   pageTitle="Совместное использование канала ExpressRoute несколькими подписками | Microsoft Azure"
   description="В этой статье описывается совместное использование канала ExpressRoute несколькими подписками Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/20/2015"
   ms.author="cherylmc" />

# Совместное использование канала ExpressRoute несколькими подписками

Один канал ExpressRoute могут совместно использовать несколько подписок. **На рис. 1** схематично показан способ совместного использования каналов ExpressRoute несколькими подписками.

Каждое маленькое облако внутри большого облака представляет подписки, принадлежащие различным подразделениям одной организации. Любое подразделение в организации может использовать свою собственную подписку для развертывания служб, но, кроме того, оно может совместно использовать один выделенный канал ExpressRoute для подключения к корпоративной сети. Отдельное подразделение (в данном примере — IT) может обладать выделенным каналом ExpressRoute. Плата за подключение и пропускную способность выделенного канала будет взиматься с владельца выделенного канала. Другие подписки в организации могут использовать канал ExpressRoute.

**Рисунок 1**

![Совместное использование подписки](./media/expressroute-share-circuit/IC766124.png)

## Администрирование

*Владелец канала* — это администратор или соадминистратор подписки, в которой был создан выделенный канал ExpressRoute. Владелец канала может разрешить администраторам и соадминистраторам других подписок (обозначенным как *пользователи канала* на диаграмме рабочего процесса) для использования своего выделенного канала. После получения разрешения пользователи выделенного канала могут связать виртуальную сеть в своей подписке с выделенным каналом организации.

Владелец канала имеет право изменить или отменить авторизацию в любое время. Отмена авторизации приводит к удалению всех ссылок из подписки, доступ которой был отменен.

## Рабочий процесс

1. Владелец канала разрешает администраторам других подписок использовать указанный канал. В приведенном ниже примере администратор канала (Contoso IT) разрешает администратору другой подписки (Contoso Sales) использовать канал, указав его идентификатор Майкрософт для подключения к каналу до двух виртуальных сетей. Командлет не отправляет по электронной почте указанный идентификатор Майкрософт. Владелец канала должен сам уведомить владельца другой подписки о том, что проверка подлинности завершена.

		PS C:\> New-AzureDedicatedCircuitLinkAuthorization -ServiceKey '6ed7e310-1a02-4261-915f-6ccfedc416f1' -Description 'SalesTeam' -Limit 2 -MicrosoftIds 'salesadmin@contoso.com'
		
		Description         : SalesTeam 
		Limit               : 2 
		LinkAuthorizationId : e2bc2645-6fd4-44a4-94f5-f2e43e6953ed 
		MicrosoftIds        : salesadmin@contoso.com 
		Used                : 0

1. После получения уведомления от владельца канала администратор авторизованной подписки может для получения ключа службы канала запустить следующий командлет. В этом примере администратор Contoso Sales должен сначала войти с использованием указанного идентификатора Майкрософт, salesadmin@contoso.com.

		PS C:\> Get-AzureAuthorizedDedicatedCircuit
		
		Bandwidth                        : 100
		CircuitName                      : ContosoIT
		Location                         : Washington DC
		MaximumAllowedLinks              : 2
		ServiceKey                       : 6ed7e310-1a02-4261-915f-6ccfedc416f1
		ServiceProviderName              : ###########
		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled
		UsedLinks                        : 0

1. Чтобы завершить операцию подключения, администратор авторизованной подписки выполняет следующий командлет.

		PS C:\> New-AzureDedicatedCircuitLink –servicekey 6ed7e310-1a02-4261-915f-6ccfedc416f1 –VnetName 'SalesVNET1' 
		
			State VnetName 
			----- -------- 
			Provisioned SalesVNET1

И это все. Виртуальная сеть Contoso Sales в Azure теперь подключена к каналу, созданному и принадлежащему Contoso IT.

## Управление авторизацией

Владелец канала может разрешить использование канала не более 10 подпискам Azure. Владелец канала может просматривать список пользователей, кому был разрешен доступ к каналу. Владелец может отменить авторизацию в любое время. Если владелец канала отменяет авторизацию, идентифицируемую LinkAuthorizationId, все ссылки, разрешенные данной авторизацией, будут немедленно удалены. Подключенные виртуальные сети потеряют соединение с локальной сетью через канал ExpressRoute.

	PS C:\> Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: 6ed7e310-1a02-4261-915f-6ccfedc416f1 
	
	Description         : EngineeringTeam 
	Limit               : 3 
	LinkAuthorizationId : cc958457-c8c1-4f16-af09-e7f099da64bf 
	MicrosoftIds        : engadmin@contoso.com 
	Used                : 1 
	
	Description         : MarketingTeam 
	Limit               : 1 
	LinkAuthorizationId : d972726f-c7b9-4658-8598-ad3208ac9348 
	MicrosoftIds        : marketingadmin@contoso.com 
	Used                : 0 
	
	Description         : SalesTeam 
	Limit               : 2 
	LinkAuthorizationId : e2bc2645-6fd4-44a4-94f5-f2e43e6953ed 
	MicrosoftIds        : salesadmin@contoso.com 
	Used                : 2 
	
	PS C:\> Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey '6ed7e310-1a02-4261-915f-6ccfedc416f1' -AuthorizationId 'e2bc2645-6fd4-44a4-94f5-f2e43e6953ed'


На приведенной ниже схеме показан рабочий процесс авторизации.

![Рабочий процесс совместного использования подписок](./media/expressroute-share-circuit/IC759525.png)

## Дальнейшие действия

Дополнительные сведения об ExpressRoute см. в разделе [Обзор ExpressRoute](expressroute-introduction.md).

<!---HONumber=August15_HO6-->